# API开发规范

## 文件位置
- 文件统一放在如下位置：
```
  app/{$app_name}/api/
```

## API注册
- 注册文件
```
  config/apis.php
```

- 注册格式
```
    'api的method' => ['uses' => '调用哪个类@哪个方法', 'version'=>['支持的版本号']],
```
```
    'item.search' => ['uses' => 'sysitem_api_item_search@getList', 'version'=>['v1']],
```

- API请求权限注册
API请求权限是注册给app的，比如topc应用可以请求systrade应用提供的api，需要注册如下信息：
```
//表示topc可以调用systrade的api，并且每60秒可以调用1000次
    'depends'=>array (
         'topc' => array (
             'systrade' => array ( 'appName' => 'systrade', 'path' => '*', 'limit_count' => 1000, 'limit_seconds' => 60,),
         ),
    ),
```
dev应用提供了一个命令：cmd dev:rpc depends_info命令可以获取当前代码的调用情况。直接将显示出来的数组复制到config/apis.php的depends项即可


## API开发
- CLASS格式

```
class sysitem_api_item_get{

    public $apiDescription = '获取单个商品的详细信息';
    //参数注册方法
    public function getParams()
    {
        'item_id' => ['type'=>'int','required'=>true,'description'=>'商品编号','example'=>'2','default'=>''],
        $return['params'] = array(
            'params_name' => [
                'type' => '这里写数据验证规则',
                'required' => '是否必填',
                'descript' =>'说明',
                'example' => '示例数据',
                'default' => '默认数据'
            ]
        );
        //扩展列的配置
        $return['extendsFields'] = ['item_desc','item_count','item_store','item_status','sku','item_nature','spec_index'];
    }

    //方法名可以自定义
    public function apiAction($params)
    {
        return $result;
    }
```

```
class sysitem_api_item_get{

    /**
     * 接口作用说明
     */
    public $apiDescription = '获取单个商品的详细信息';

    /**
     * 定义应用级参数，参数的数据类型，参数是否必填，参数的描述
     * 用于在调用接口前，根据定义的参数，过滤必填参数是否已经参入
     */
    public function getParams()
    {
        $return['params'] = array(
            'item_id' => ['type'=>'int','required'=>true,'description'=>'商品编号','example'=>'2','default'=>''],
            'shop_id' => ['type'=>'int','required'=>false,'description'=>'店铺id','example'=>'2','default'=>''],
            'fields' => ['type'=>'field_list','required'=>false,'description'=>'要获取的商品字段集','example'=>'title,item_store.store,item_status.approve_status','default'=>''],
        );

        $return['extendsFields'] = ['item_desc','item_count','item_store','item_status','sku','item_nature','spec_index'];
        return $return;
    }

    public function get($params)
    {
        return $result;
    }
```

