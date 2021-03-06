# CURD 请求处理库

## GetModel 获取单个数据

条件查询：请求可使用 id 或 where 字段进行查询，二者选一即可

- id `int|string`
- where `array`，必须使用数组方式来定义

```php
$this->post['where'] = [
    ['name', '=', 'van']
];
```

引入特性，需要定义必须的操作模型 model

```php
use think\bit\traits\GetModel;

class AdminClass extends Base {
    use GetModel;

    protected $model = 'admin';
}
```

自定义获取验证器为 get_validate，默认为

```php
protected $get_validate = [
    'id' => 'require'
];
```

也可以在控制器中针对性修改

```php
use think\bit\traits\GetModel;

class AdminClass extends Base {
    use GetModel;

    protected $model = 'admin';
    protected $get_validate = [
        'id' => 'require',
        'name' => 'require'
    ];
}
```

判断是否有前置处理，则需要调用生命周期 GetBeforeHooks

```php
use think\bit\traits\GetModel;
use think\bit\lifecycle\GetBeforeHooks;

class AdminClass extends Base implements GetBeforeHooks {
    use GetModel;

    protected $model = 'admin';

    public function __getBeforeHooks()
    {
        return true;
    }
}
```

\_\_getBeforeHooks 的返回值为 `false` 则在此结束执行，并返回 get_before_result 属性的值，默认为：

```php
protected $get_before_result = [
    'error' => 1,
    'msg' => 'error:before_fail'
];
```

在生命周期函数中可以通过重写自定义前置返回

```php
use think\bit\traits\GetModel;
use think\bit\lifecycle\GetBeforeHooks;

class AdminClass extends Base implements GetBeforeHooks {
    use GetModel;

    protected $model = 'admin';

    public function __getBeforeHooks()
    {
        $this->get_before_result = [
            'error'=> 1,
            'msg'=> 'error:only'
        ];
        return false;
    }
}
```

如需要给接口在后端就设定固定条件，只需要重写 get_condition，默认为

```php
protected $get_condition = [];
```

例如加入企业主键限制

```php
use think\bit\traits\GetModel;

class AdminClass extends Base {
    use GetModel;

    protected $model = 'admin';
    protected $get_condition = [
        ['enterprise', '=', 1]
    ];
}
```

如需要给接口限制返回字段，只需要重写 get_field，默认为

```php
protected $get_field = ['update_time,create_time', true];
```

例如返回除 update_time 修改时间所有的字段

```php
use think\bit\traits\GetModel;

class AdminClass extends Base {
    use GetModel;

    protected $model = 'admin';
    protected $get_field = ['update_time', true];
}
```

如自定义返回结果，则需要调用生命周期 GetCustom

```php
use think\bit\traits\GetModel;
use think\bit\lifecycle\GetCustom;

class AdminClass extends Base implements GetCustom {
    use GetModel;

    protected $model = 'admin';

    public function __getCustomReturn($data)
    {
        return [
            'error' => 0,
            'data' => $data
        ];
    }
}
```

\_\_getCustomReturn 需要返回整体的响应结果

```php
return [
    'error' => 0,
    'data' => $data
];
```

- data `array` 原数据

## OriginListsModel 获取列表数据

条件合并: 请求中的 where 将于 origin_lists_condition 合并

- where `array` 必须使用数组方式来定义

```php
$this->post['where'] = [
    ['name', 'like', '%v%']
];
```

模糊查询在 post 请求中加入参数 like，他将于以上条件共同合并

- like `array` 模糊搜索条件
  - field 模糊搜索字段名
  - value 模糊搜索字段值

```json
[{ "field": "name", "value": "a" }]
```

引入特性需要定义必须的操作模型 model

```php
use think\bit\traits\OriginListsModel;

class AdminClass extends Base {
    use OriginListsModel;

    protected $model = 'admin';
}
```

合并模型验证器下 origin 场景，所以需要对应创建验证器场景 validate/AdminClass， 并加入场景 `origin`

```php
use think\Validate;

class AdminClass extends Validate
{
    protected $rule = [
        'status' => 'require',
    ];

    protected $scene = [
        'origin' => ['status'],
    ];
}
```

可定义固定条件属性 `$this->origin_lists_condition`，默认为 `[]`

```php
use think\bit\traits\OriginListsModel;

class NoBodyClass extends Base {
    use OriginListsModel;

    protected $model = 'nobody';
    protected $origin_lists_condition = [
        ['status', '=', 1]
    ];
}
```

如果接口的查询条件较为特殊，可以重写 **origin_lists_condition_query**

```php
use think\bit\traits\OriginListsModel;

class NoBodyClass extends Base {
    use OriginListsModel;

    protected $model = 'nobody';

    public function __construct(App $app = null)
    {
        parent::__construct($app);
        $this->origin_lists_condition_query = function (Query $query) {
            $query->whereOr([
                'type' => 1
            ]);
        };
    }
}
```

如自定义前置处理，则需要调用生命周期 OriginListsBeforeHooks

```php
use think\bit\traits\OriginListsModel;
use think\bit\lifecycle\OriginListsBeforeHooks;

class AdminClass extends Base implements OriginListsBeforeHooks {
    use OriginListsModel;

    protected $model = 'admin';

    public function __originListsBeforeHooks()
    {
        return true;
    }
}
```

\_\_originListsBeforeHooks 的返回值为 `false` 则在此结束执行，并返回 origin_lists_before_result 属性的值，默认为：

```php
protected $origin_lists_before_result = [
    'error' => 1,
    'msg' => 'error:before_fail'
];
```

在生命周期函数中可以通过重写自定义前置返回

```php
use think\bit\traits\OriginListsModel;
use think\bit\lifecycle\OriginListsBeforeHooks;

class AdminClass extends Base implements OriginListsBeforeHooks {
    use OriginListsModel;

    protected $model = 'admin';

    public function __originListsBeforeHooks()
    {
        $this->origin_lists_before_result = [
            'error'=> 1,
            'msg'=> 'error:only'
        ];
        return false;
    }
}
```

如需要给接口在后端就设定固定条件，只需要重写 origin_lists_condition，默认为

```php
protected $origin_lists_condition = [];
```

例如加入企业主键限制

```php
use think\bit\traits\OriginListsModel;

class AdminClass extends Base {
    use OriginListsModel;

    protected $model = 'admin';
    protected $origin_lists_condition = [
        ['enterprise', '=', 1]
    ];
}
```

如果需要列表按条件排序，只需要重写 origin_lists_orders，默认为

```php
protected $origin_lists_orders = 'create_time desc';
```

例如按年龄进行排序

```php
use think\bit\traits\OriginListsModel;

class AdminClass extends Base {
    use OriginListsModel;

    protected $model = 'admin';
    protected $origin_lists_orders = 'age desc';
}
```

如需要给接口限制返回字段，只需要重写 origin_lists_field，默认为

```php
protected $origin_lists_field = ['update_time,create_time', true];
```

例如返回除 update_time 修改时间所有的字段

```php
use think\bit\traits\OriginListsModel;

class AdminClass extends Base {
    use OriginListsModel;

    protected $model = 'admin';
    protected $origin_lists_field = ['update_time', true];
}
```

如自定义返回结果，则需要调用生命周期 OriginListsCustom

```php
use think\bit\traits\OriginListsModel;
use think\bit\lifecycle\OriginListsCustom;

class AdminClass extends Base implements OriginListsCustom {
    use OriginListsModel;

    protected $model = 'admin';

    public function __originListsCustomReturn(Array $lists)
    {
        return [
            'error' => 0,
            'data' => $lists
        ];
    }
}
```

\_\_originListsCustomReturn 需要返回整体的响应结果

```php
return [
    'error' => 0,
    'data' => $data
];
```

- data `array` 原数据

## ListsModel 获取分页数据

条件合并: 请求中的 where 将于 lists_condition 合并

- where `array` 必须使用数组方式来定义

```php
$this->post['where'] = [
    ['name', '=', 'van']
];
```

引入特性，需要定义必须的操作模型 model

```php
use think\bit\traits\ListsModel;

class AdminClass extends Base {
    use ListsModel;

    protected $model = 'admin';
}
```

如自定义前置处理，则需要调用生命周期 ListsBeforeHooks

```php
use think\bit\traits\ListsModel;
use think\bit\lifecycle\ListsBeforeHooks;

class AdminClass extends Base implements ListsBeforeHooks {
    use ListsModel;

    protected $model = 'admin';

    public function __listsBeforeHooks()
    {
        return true;
    }
}
```

\_\_listsBeforeHooks 的返回值为 `false` 则在此结束执行，并返回 lists_before_result 属性的值，默认为：

```php
protected $lists_before_result = [
    'error' => 1,
    'msg' => 'error:before_fail'
];
```

在生命周期函数中可以通过重写自定义前置返回

```php
use think\bit\traits\ListsModel;
use think\bit\lifecycle\ListsBeforeHooks;

class AdminClass extends Base implements ListsBeforeHooks {
    use ListsModel;

    protected $model = 'admin';

    public function __listsBeforeHooks()
    {
        $this->lists_before_result = [
            'error'=> 1,
            'msg'=> 'error:only'
        ];
        return false;
    }
}
```

如需要给接口在后端就设定固定条件，只需要重写 lists_condition，默认为

```php
protected $lists_condition = [];
```

例如加入企业主键限制

```php
use think\bit\traits\ListsModel;

class AdminClass extends Base {
    use ListsModel;

    protected $model = 'admin';
    protected $lists_condition = [
        ['enterprise', '=', 1]
    ];
}
```

如果接口的查询条件较为特殊，可以重写 lists_condition_query

```php
use think\bit\traits\ListsModel;

class AdminClass extends Base {
    use ListsModel;

    protected $model = 'admin';

    public function __construct(App $app = null)
    {
        parent::__construct($app);
        $this->lists_condition_query = function (Query $query) {
            $query->whereOr([
                'type' => 1
            ]);
        };
    }
}
```

如果需要列表按条件排序，只需要重写 lists_orders，默认为

```php
protected $lists_orders = 'create_time desc';
```

例如按年龄进行排序

```php
use think\bit\traits\ListsModel;

class AdminClass extends Base {
    use ListsModel;

    protected $model = 'admin';
    protected $lists_orders = 'age desc';
}
```

如需要给接口限制返回字段，只需要重写 lists_field，默认为

```php
protected $lists_field = ['update_time,create_time', true];
```

例如返回除 update_time 修改时间所有的字段

```php
use think\bit\traits\ListsModel;

class AdminClass extends Base {
    use ListsModel;

    protected $model = 'admin';
    protected $lists_field = ['update_time', true];
}
```

如自定义返回结果，则需要调用生命周期 ListsCustom

```php
use think\bit\traits\ListsModel;
use think\bit\lifecycle\ListsCustom;

class AdminClass extends Base implements ListsCustom {
    use ListsModel;

    protected $model = 'admin';

    public function __listsCustomReturn(Array $lists, int $total)
    {
        return [
            'error' => 0,
            'data' => [
                'lists' => $lists,
                'total' => $total,
            ]
        ];
    }
}
```

\_\_listsCustomReturn 需要返回整体的响应结果

```php
return [
    'error' => 0,
    'data' => [
        'lists' => $lists,
        'total' => $total,
    ]
];
```

- data `array` 原数据

## AddModel 新增数据

引入特性，需要定义必须的操作模型 model

```php
use think\bit\traits\AddModel;

class AdminClass extends Base {
    use AddModel;

    protected $model = 'admin';
}
```

合并模型验证器下 add 场景，所以需要对应创建验证器场景 validate/AdminClass， 并加入场景 `add`

```php
use think\Validate;

class AdminClass extends Validate
{
    protected $rule = [
        'name' => 'require',
    ];

    protected $scene = [
        'add' => ['name'],
    ];
}
```

如自定义前置处理，则需要调用生命周期 AddBeforeHooks

```php
use think\bit\traits\AddModel;
use think\bit\lifecycle\AddBeforeHooks;

class AdminClass extends Base implements AddBeforeHooks {
    use AddModel;

    protected $model = 'admin';

    public function __addBeforeHooks()
    {
        return true;
    }
}
```

\_\_addBeforeHooks 的返回值为 `false` 则在此结束执行，并返回 add_before_result 属性的值，默认为：

```php
protected $add_before_result = [
    'error' => 1,
    'msg' => 'error:before_fail'
];
```

在生命周期函数中可以通过重写自定义前置返回

```php
use think\bit\traits\AddModel;
use think\bit\lifecycle\AddBeforeHooks;

class AdminClass extends Base implements AddBeforeHooks {
    use AddModel;

    protected $model = 'admin';

    public function __addBeforeHooks()
    {
        $this->add_before_result = [
            'error'=> 1,
            'msg'=> 'error:only'
        ];
        return false;
    }
}
```

如自定义后置处理，则需要调用生命周期 AddAfterHooks

```php
use think\bit\traits\AddModel;
use think\bit\lifecycle\AddAfterHooks;

class AdminClass extends Base implements AddAfterHooks {
    use AddModel;

    protected $model = 'admin';

    public function __addAfterHooks($pk)
    {
        return true;
    }
}
```

pk 为模型写入后返回的主键，\_\_addAfterHooks 的返回值为 `false` 则在此结束执行进行事务回滚，并返回 add_after_result 属性的值，默认为：

```php
protected $add_after_result = [
    'error' => 1,
    'msg' => 'error:after_fail'
];
```

在生命周期函数中可以通过重写自定义后置返回

```php
use think\bit\traits\AddModel;
use think\bit\lifecycle\AddAfterHooks;

class AdminClass extends Base implements AddAfterHooks {
    use AddModel;

    protected $model = 'admin';

    public function __addAfterHooks($pk)
    {
        $this->add_after_result = [
            'error'=> 1,
            'msg'=> 'error:redis'
        ];
        return false;
    }
}
```

## EditModel 编辑数据

条件查询：请求可使用 id 或 where 字段进行查询，二者选一即可

- id `int|string`
- where `array`，必须使用数组方式来定义

```php
$this->post['where'] = [
    ['name', '=', 'van']
];
```

引入特性，需要定义必须的操作模型 model

```php
use think\bit\traits\EditModel;

class AdminClass extends Base {
    use EditModel;

    protected $model = 'admin';
}
```

自定义删除验证器为 edit_validate，默认为

```php
protected $edit_validate = [
    'id' => 'require',
    'switch' => 'bool'
];
```

也可以在控制器中针对性修改

```php
use think\bit\traits\EditModel;

class AdminClass extends Base {
    use EditModel;

    protected $model = 'admin';
    protected $edit_validate = [
        'id' => 'require'
        'switch' => 'bool',
        'status' => 'require',
    ];
}
```

合并模型验证器下 edit 场景，所以需要对应创建验证器场景 validate/AdminClass，edit_switch 为 `false` 下有效， 并加入场景 `edit`

```php
use think\Validate;

class AdminClass extends Validate
{
    protected $rule = [
        'name' => 'require',
    ];

    protected $scene = [
        'edit' => ['name'],
    ];
}
```

如自定义前置处理，则需要调用生命周期 EditBeforeHooks

```php
use think\bit\traits\EditModel;
use think\bit\lifecycle\EditBeforeHooks;

class AdminClass extends Base implements EditBeforeHooks {
    use EditModel;

    protected $model = 'admin';

    public function __editBeforeHooks()
    {
        return true;
    }
}
```

\_\_editBeforeHooks 的返回值为 `false` 则在此结束执行，并返回 edit_before_result 属性的值，默认为：

```php
protected $edit_before_result = [
    'error' => 1,
    'msg' => 'error:before_fail'
];
```

在生命周期函数中可以通过重写自定义前置返回

```php
use think\bit\traits\EditModel;
use think\bit\lifecycle\EditBeforeHooks;

class AdminClass extends Base implements EditBeforeHooks {
    use EditModel;

    protected $model = 'admin';

    public function __editBeforeHooks()
    {
        $this->edit_before_result = [
            'error'=> 1,
            'msg'=> 'error:only'
        ];
        return false;
    }
}
```

如自定义后置处理，则需要调用生命周期 EditAfterHooks

```php
use think\bit\traits\EditModel;
use think\bit\lifecycle\EditAfterHooks;

class AdminClass extends Base implements EditAfterHooks {
    use EditModel;

    protected $model = 'admin';

    public function __editAfterHooks()
    {
        return true;
    }
}
```

\_\_editAfterHooks 的返回值为 `false` 则在此结束执行进行事务回滚，并返回 edit_after_result 属性的值，默认为：

```php
 protected $edit_after_result = [
    'error' => 1,
    'msg' => 'error:after_fail'
];
```

在生命周期函数中可以通过重写自定义后置返回

```php
use think\bit\traits\EditModel;
use think\bit\lifecycle\EditAfterHooks;

class AdminClass extends Base implements EditAfterHooks {
    use EditModel;

    protected $model = 'admin';

    public function __editAfterHooks()
    {
        $this->edit_after_result = [
            'error'=> 1,
            'msg'=> 'error:redis'
        ];
        return false;
    }
}
```

## DeleteModel 删除数据

条件查询：请求可使用 id 或 where 字段进行查询，二者选一即可

- id `int|string`
- where `array`，必须使用数组方式来定义

```php
$this->post['where'] = [
    ['name', '=', 'van']
];
```

引入特性，需要定义必须的操作模型 model

```php
use think\bit\traits\DeleteModel;

class AdminClass extends Base {
    use DeleteModel;

    protected $model = 'admin';
}
```

自定义删除验证器为 delete_validate，默认为

```php
protected $delete_validate = [
    'id' => 'require'
];
```

也可以在控制器中针对性修改

```php
use think\bit\traits\DeleteModel;

class AdminClass extends Base {
    use DeleteModel;

    protected $model = 'admin';
    protected $delete_validate = [
        'id' => 'require',
        'name' => 'require'
    ];
}
```

如自定义前置处理，则需要调用生命周期 DeleteBeforeHooks

```php
use think\bit\traits\DeleteModel;
use think\bit\lifecycle\DeleteBeforeHooks;

class AdminClass extends Base implements DeleteBeforeHooks {
    use DeleteModel;

    protected $model = 'admin';

    public function __deleteBeforeHooks()
    {
        return true;
    }
}
```

\_\_deleteBeforeHooks 的返回值为 `false` 则在此结束执行，并返回 delete_before_result 属性的值，默认为：

```php
protected $delete_before_result = [
    'error' => 1,
    'msg' => 'error:before_fail'
];
```

在生命周期函数中可以通过重写自定义前置返回

```php
use think\bit\traits\DeleteModel;
use think\bit\lifecycle\DeleteBeforeHooks;

class AdminClass extends Base implements DeleteBeforeHooks {
    use DeleteModel;

    protected $model = 'admin';

    public function __deleteBeforeHooks()
    {
        $this->delete_before_result = [
            'error'=> 1,
            'msg'=> 'error:only'
        ];
        return false;
    }
}
```

判断是否有存在事务之后模型写入之前的处理，如该周期处理，则需要调用生命周期 DeletePrepHooks

```php
use think\bit\traits\DeleteModel;
use think\bit\lifecycle\DeletePrepHooks;

class AdminClass extends Base implements DeletePrepHooks {
    use DeleteModel;

    protected $model = 'admin';

    public function __deletePrepHooks()
    {
        return true;
    }
}
```

\_\_deletePrepHooks 的返回值为 `false` 则在此结束执行进行事务回滚，并返回 delete_prep_result 属性的值，默认为：

```php
protected $delete_prep_result = [
    'error' => 1,
    'msg' => 'error:prep_fail'
];
```

在生命周期函数中可以通过重写自定义返回

```php
use think\bit\traits\DeleteModel;
use think\bit\lifecycle\DeletePrepHooks;

class AdminClass extends Base implements DeletePrepHooks {
    use DeleteModel;

    protected $model = 'admin';

    public function __deletePrepHooks()
    {
        $this->delete_prep_result = [
            'error'=> 1,
            'msg'=> 'error:insert'
        ];
        return false;
    }
}
```

如自定义后置处理，则需要调用生命周期 DeleteAfterHooks

```php
use think\bit\traits\DeleteModel;
use think\bit\lifecycle\DeleteAfterHooks;

class AdminClass extends Base implements DeleteAfterHooks {
    use DeleteModel;

    protected $model = 'admin';

    public function __deleteAfterHooks()
    {
        return true;
    }
}
```

\_\_deleteAfterHooks 的返回值为 `false` 则在此结束执行进行事务回滚，并返回 delete_after_result 属性的值，默认为：

```php
protected $delete_after_result = [
    'error' => 1,
    'msg' => 'error:after_fail'
];
```

在生命周期函数中可以通过重写自定义后置返回

```php
use think\bit\traits\DeleteModel;
use think\bit\lifecycle\DeleteAfterHooks;

class AdminClass extends Base implements DeleteAfterHooks {
    use DeleteModel;

    protected $model = 'admin';

    public function __deleteAfterHooks()
    {
        $this->delete_after_result = [
            'error'=> 1,
            'msg'=> 'error:redis'
        ];
        return false;
    }
}
```

## lifecycle 生命周期

- GetBeforeHooks 单条数据的通用请求处理前置自定义周期
  - \_\_getBeforeHooks() 单条数据前置周期
    - return `boolean`，返回值为 `false` 则在此结束执行
- GetCustom 单条数据的通用请求处理自定义返回周期
  - \_\_getCustomReturn($data) 单条数据前置周期
    - data `array` 原数据
- ListsBeforeHooks 分页数据的通用请求处理前置自定义周期
  - \_\_listsBeforeHooks() 分页数据前置周期
    - return `boolean`，返回值为 `false` 则在此结束执行
- ListsCustom 分页数据的通用请求处理自定义返回周期
  - \_\_listsCustomReturn(Array $lists, int $total) 分页数据自定义返回周期
    - lists `array` 原数据
    - total `int` 数据总数
- OriginListsBeforeHooks 列表数据请求前置处理周期
  - \_\_originListsBeforeHooks() 列表数据前置周期
    - return `boolean`，返回值为 `false` 则在此结束执行
- OriginListsCustom 列表数据的通用请求处理自定义返回周期
  - \_\_originListsCustomReturn(Array $lists) 列表数据自定义返回周期
    - lists `array` 原数据
- AddBeforeHooks 新增数据的通用请求处理前置自定义周期
  - \_\_addBeforeHooks() 新增前置周期
    - return `boolean`，返回值为 `false` 则在此结束执行
- AddAfterHooks 新增数据的通用请求处理后置自定义周期
  - \_\_addAfterHooks($pk) 新增后置周期
    - pk `string|int` 模型写入后返回的主键
    - return `boolean`，返回值为 `false` 则在此结束执行进行事务回滚
- EditBeforeHooks 编辑数据的通用请求处理前置自定义周期
  - \_\_editBeforeHooks() 编辑前置周期
    - return `boolean`，返回值为 `false` 则在此结束执行
- EditAfterHooks 编辑数据的通用请求处理后置自定义周期
  - \_\_editBeforeHooks() 编辑前置周期
    - return `boolean`，返回值为 `false` 则在此结束执行进行事务回滚
- DeleteBeforeHooks 删除数据的通用请求处理前置自定义周期
  - \_\_deleteBeforeHooks() 删除前置周期
    - return `boolean`，返回值为 `false` 则在此结束执行
- DeletePrepHooks 删除数据的通用请求处理在事务之后模型写入之前的的自定义周期
  - \_\_deletePrepHooks() 删除在事务之后模型写入之前的周期
    - return `boolean`，返回值为 `false` 则在此结束执行进行事务回滚
- DeleteAfterHooks 删除数据的通用请求处理后置自定义周期
  - \_\_deleteAfterHooks() 删除后置周期
    - return `boolean`，返回值为 `false` 则在此结束执行进行事务回滚
