# CURD

## 新增数据请求

### customAction(name: string)

设置自定义函数名

- name 函数名，请求地址默认为 `model+'/add'`，通过 `name` 修改 `'/add'`

### factory(model: string, data: any)

生成新增请求

- model 模块名称
- data 发送数据
- Return `Observable<any>`

为管理员服务生成新增请求接口

```typescript
@Injectable()
export class AdminService {
  private model = "admin";

  constructor(private addService: AddService) {}

  add(data: any) {
    return this.addService.factory(this.model, data);
  }
}
```

将管理员服务注入在应用模块下的供应商内

```typescript
@NgModule({
  providers: [AdminService],
})
export class AppModule {}
```

在组件中使用

```typescript
export class AdminAddComponent implements OnInit {
  constructor(private adminService: AdminService) {}

  submit = (data) => {
    this.adminService.add(data).subscribe((res) => {
      // res
    });
  };
}
```

## 删除数据请求

### customAction(name: string)

设置自定义函数名

- name 函数名，请求地址默认为 `model+'/delete'`，通过 `name` 修改 `'/delete'`

### factory(model: string, condition: any)

生成删除请求

- model 模块名称
- condition 删除条件
- Return `Observable<any>`

将管理员服务注入在应用模块下的供应商内

```typescript
@NgModule({
  providers: [AdminService],
})
export class AppModule {}
```

例如：如果 `id` 为条件，这里可以是 `单个id` 也可以是 `多个id构成的数组`

```typescript
@Injectable()
export class AdminService {
  private model = "admin";

  constructor(private deleteService: DeleteService) {}

  delete(id: any): Observable<any> {
    return this.deleteService.factory(this.model, {
      id: id,
    });
  }
}
```

例如：如果不以 `id` 为条件，这里需要用条件数组来做参数

```typescript
@Injectable()
export class AdminService {
  private model = "admin";

  constructor(private deleteService: DeleteService) {}

  deleteNoId(condition: any): Observable<any> {
    return this.deleteService.factory(this.model, condition);
  }
}
```

调用自定删除

```typescript
this.admin.deleteNoId([["name", "=", "one"]]);
```

## 编辑数据请求

### customAction(name: string)

设置自定义函数名

- name 函数名，请求地址默认为 `model+'/edit'`，通过 `name` 修改 `'/edit'`

### factory(model: string, data: any, condition: any = [])

生成新增请求

- model 模块名称
- data 发送数据
- condition 条件数组
- Return `Observable<any>`

将管理员服务注入在应用模块下的供应商内

```typescript
@NgModule({
  providers: [AdminService],
})
export class AppModule {}
```

例如：请求数据包含 id 无需使用条件数组

```typescript
@Injectable()
export class AdminService {
  private model = "admin";

  constructor(private editService: EditService) {}

  edit(data: any): Observable<any> {
    return this.editService.factory(this.model, data);
  }
}
```

例如：请求数组需要其他条件时，需要定义条件数组

```typescript
@Injectable()
export class AdminService {
  private model = "admin";

  constructor(private editService: EditService) {}

  editSpecial(data: any, condition: any): Observable<any> {
    return this.editService.factory(this.model, data, condition);
  }
}
```

调用编辑请求

```typescript
this.admin.editSpecial({ name: "two" }, [["uuid", "=", "xxx"]]);
```

## 获取单条数据请求

### customAction(name: string)

设置自定义函数名

- name 函数名，请求地址默认为 `model+'/get'`，通过 `name` 修改 `'/get'`

### factory(model: string, condition: any)

生成新增请求

- model 模块名称
- condition 条件数组
- Return `Observable<any>`

将管理员服务注入在应用模块下的供应商内

```typescript
@NgModule({
  providers: [AdminService],
})
export class AppModule {}
```

例如：如果 `id` 为条件，这里只能是 `单个id`

```typescript
@Injectable()
export class AdminService {
    private model = 'admin';

    constructor(private getService: GetService) {}

    get(id: any) {
        return this.getService.factory(this.model, {
            id: id
        });
    }
  }
}
```

例如：如果不以 `id` 为条件，这里需要用条件数组来做参数

```typescript
@Injectable()
export class AdminService {
  private model = "admin";

  constructor(private getService: GetService) {}

  getNoId(condition: any): Observable<any> {
    return this.getService.factory(this.model, condition);
  }
}
```

调用自定获取

```typescript
this.admin.getNoId([["name", "=", "one"]]);
```

## 获取分页数据请求

### customAction(name: string)

设置自定义函数名

- name 函数名，请求地址默认为 `model+'/lists'`，通过 `name` 修改 `'/lists'`

### factory(model: string, condition: any[] = [], like: any = [], refresh?: boolean)

生成新增请求

- model 模块名称
- condition 条件数组
- like 模糊搜索数组
- refresh 刷新参数，将分页、列表等初始化
- Return `Observable<any>`

将管理员服务注入在应用模块下的供应商内

```typescript
@NgModule({
  providers: [AdminService],
})
export class AppModule {}
```

为管理员 api 服务生成分页列表数据请求服务

```typescript
@Injectable()
export class AdminService {
  private model = "admin";

  constructor(private listsService: ListsService) {}

  lists(search: any, refresh: boolean): Observable<any> {
    return this.listsService.factory(this.model, [], search, refresh);
  }
}
```

模糊搜索调用

```typescript
this.admin.lists([{ field: "username", value: "o" }], true);
```

## 获取列表数据请求

### customAction(name: string)

设置自定义函数名

- name 函数名，请求地址默认为 `model+'/originLists'`，通过 `name` 修改 `'/originLists'`

### factory(model: string, condition: any[] = [], like: any = [])

生成新增请求

- model 模块名称
- condition 条件数组
- like 模糊搜索数组
- Return `Observable<any>`

将管理员服务注入在应用模块下的供应商内

```typescript
@NgModule({
  providers: [AdminService],
})
export class AppModule {}
```

为管理员 api 服务生成分页列表数据请求服务

```typescript
@Injectable()
export class AdminService {
  private model = "admin";

  constructor(private originListsService: OriginListsService) {}

  lists(search: any): Observable<any> {
    return this.originListsService.factory(this.model, [], search);
  }
}
```

模糊搜索调用

```typescript
this.admin.lists([{ field: "username", value: "o" }]);
```

## 状态变更请求

### customAction(name: string)

设置自定义函数名

- name 函数名，请求地址默认为 `model+'/edit'`，通过 `name` 修改 `'/edit'`

### factory(model: string, data: any, field = 'status')

生成状态切换请求

- model 模块名称
- data 状态修改数据引用，必须包含主键与状态字段
- field 状态字段名称
- Return `Observable<any>`

将管理员服务注入在应用模块下的供应商内

```typescript
@NgModule({
  providers: [AdminService],
})
export class AppModule {}
```

为管理员接口服务生成分页列表数据请求服务

```typescript
@Injectable()
export class AdminService {
  private model = "admin";

  constructor(private statusService: StatusService) {}

  status(data: any): Observable<any> {
    return this.statusService.factory(this.model, data);
  }
}
```

在模版中

```html
<nz-switch
  [nzCheckedChildren]="bit.l['on']"
  [nzUnCheckedChildren]="bit.l['off']"
  [(ngModel)]="data.status"
  [nzControl]="true"
  (click)="bit.statusChange(adminService.status(data),statusCallback)"
>
</nz-switch>
```

组件中

```typescript
export class AdminIndexComponent implements OnInit {
  constructor(
    public bit: BitService,
    public adminService: AdminService,
    private notification: NzNotificationService
  ) {}

  ngOnInit() {
    // customize
  }

  statusCallback = (res: any) => {
    switch (res.msg) {
      case "error:self":
        this.notification.error(
          this.bit.l["operate_error"],
          this.bit.l["error_status_self"]
        );
        break;
      default:
        this.notification.error(
          this.bit.l["operate_error"],
          this.bit.l["status_error"]
        );
    }
  };
}
```
