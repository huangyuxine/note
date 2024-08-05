# 引入

```
composer require elasticsearch/elasticsearch
```

# 准备工作

简单创建一个产品表

```mysql
CREATE TABLE `product` (
  `id` int unsigned NOT NULL AUTO_INCREMENT,
  `title` varchar(255) COLLATE utf8mb4_general_ci NOT NULL DEFAULT '',
  `content` varchar(255) COLLATE utf8mb4_general_ci NOT NULL DEFAULT '',
  `created_at` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

用navicat生成一千条中文数据

创建模型

```
php artisan make:model Product
```

# 配置

config/database.php

```php
'elasticsearch' => [
   // Elasticsearch 支持多台服务器负载均衡，因此这里是一个数组
	'hosts' => explode(',',env('ES_HOSTS')),
]
```

.env

```
ES_HOSTS=127.0.0.1:9200
```

App/Providers/AppServiceProvider.php

```php
  public function register(): void
  {
      // 注册一个名为 es 的单例
      $this->app->singleton('es',function (){
          // 从配置文件读取 Elasticsearch 服务器列表
          $builder = ClientBuilder::create()->setHosts(config('database.elasticsearch.hosts'));
          if (app()->environment()==='local'){
              // 配置日志
              $builder->setLogger(app('log')->driver());
          }

          return $builder->build();
      });
  }
```

# 控制器

```
php artisan make:controller productController --api
```

bootstrap/app.php

```php
Route::apiResources([
    'products' => ProductController::class,
]);
```

增删改查，先执行创建索引操作

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Psr\Container\ContainerExceptionInterface;
use Psr\Container\NotFoundExceptionInterface;

class ProductController extends Controller
{
    private $ESClient;

    public function __construct()
    {
        $this->ESClient = app('es');
    }
    public function index(Request $request)
    {
        $param = $request->all();
        $keyword = $param['keyword'];
        $page = $param['page'];
        $size = 20;
        $limit = ($page - 1) * $size;
        //设置查询的条件
        $params = [
            'index' => 'products',
            'body' => [
                //查询内容
                'query' => [
                    'multi_match' => [//匹配
                        'query' => $keyword,
                        'fields' => ['title', 'content'],
                    ],
                ],
                'sort' => [
                    ['created_at' => ['order' => 'desc']]
                ],
                'highlight' => [//高亮
                    'pre_tags' => ["<em style='color: red'>"],
                    'post_tags' => ["</em>"],
                    'fields' => [
                        "title" => new \stdClass(),
                        "content" => new \stdClass(),
                    ],
                ],
            ],
        ];
        if (!$this->ESClient->indices()->exists(['index' => 'products'])) {
            return [];
        }
        //分页限制
        $params["size"] = $size; //每页显示条数
        $params["from"] = $limit; //偏移量
        $products = $this->ESClient->search($params)->asArray();//es搜索

        foreach ($products['hits']['hits'] as $k => &$product) {
            $product['_source']['title'] = $product['highlight']['title'][0];
            $product['_source']['content'] = $product['highlight']['content'][0];
        }

        $list = array_column($products['hits']['hits'], '_source');

        $arr['list'] = $list;
        $arr['total'] = $products['hits']['total']['value']; //总条数
        $arr['current_page'] = $page; //当前页
        $arr['total_page'] = (int)round($products['hits']['total']['value'] / $size, 2); //总页数

        return $arr;
    }

    // 创建索引
    public function store(Request $request)
    {
        if (!$this->ESClient->indices()->exists(['index' => 'products'])) {
            $params = [
                'index' => 'products',
                'body' => [
                    'mappings' => [
                        'properties' => [
                            'title' => [
                                'type' => 'text',
                            ],
                            'content' => [
                                'type' => 'text',
                            ],
                            'created_at' => [
                                'type' => 'date',
                                'format' => 'yyyy-MM-dd HH:mm:ss',
                            ],
                        ],
                    ],
                ],
            ];
           $this->ESClient->indices()->create($params);
        }
    }

    public function show(string $id)
    {
        $params = [
            'index' => 'products',
            'id' => $id,
        ];

        try {
            $response = $this->ESClient->get($params)->asArray();
            dd($response);
        } catch (\Exception $e) {
            dd($e->getMessage());
        }
    }

    public function update(Request $request, string $id)
    {
        $params = [
            'index' => 'products', // 替换为你的索引名
            'id' => $id,
            'body' => [
                'doc' => [
                    'title' => '测试1',
                ]
            ]
        ];

        try {
            $response = $this->ESClient->update($params);
            // $response['result'] == updated 更新成功 noop 值相同
            dd($response);
        } catch (\Exception $e) {
            return $e->getMessage();
        }
    }

    public function destroy(string $id)
    {
        $params = [
            'index' => 'products',
            'id' => $id // 不带id删除整个索引
        ];
        try {
            $this->ESClient->indices()->delete($params);
        } catch (\Exception $e) {
            dd($e->getMessage());
        }
    }
}

```



# 同步数据

```
php artisan make:command Elasticsearch/SyncProducts
```

```php
<?php

namespace App\Console\Commands\Elasticsearch;

use App\Models\Product;
use Illuminate\Console\Command;
use Illuminate\Database\Eloquent\Collection;
class SyncProducts extends Command
{
    protected $signature = 'sync-products';

    protected $description = '';

		// 创建数据
    public function handle()
    {
        $es = app('es');

        Product::chunk(100, function (Collection $products) use ($es) {
            $params = [];
            foreach ($products as $product) {
                $params['body'][] = [
                    'index' => [
                        '_index' => 'products',
                        '_id'    => $product->id
                    ]
                ];

                $params['body'][] = [
                    'title'     => $product->title,
                    'content'     => $product->content,
                    'created_at'     => $product->created_at,
                ];

            }
            $responses = $es->bulk($params);
            unset($responses);
        });
    }
}
```



