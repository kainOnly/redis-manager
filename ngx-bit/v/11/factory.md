# 生产

## Operates 操作库

辅助框架还提供一些常用到的操作库

### 创建异步验证器

- asyncValidator(handle: Observable<boolean>, field = 'duplicated', dueTime = 500): Observable<any>
  - handle `Observable<boolean>` 返回验证结果
  - field `string` 自定义返回
  - dueTime `number` 防抖动延时，默认 `500` ms

假设验证 key 字段是否唯一，`response` 是模拟请求的响应值，通常这样使用请求服务

```typescript
import { Component, OnInit } from "@angular/core";
import { AbstractControl, FormBuilder, FormGroup } from "@angular/forms";
import { asyncValidator } from "ngx-bit/operates";
import { of } from "rxjs";

@Component({
  selector: "app-welcome",
  templateUrl: "./welcome.component.html",
  styleUrls: ["./welcome.component.scss"],
})
export class WelcomeComponent implements OnInit {
  form: FormGroup;

  constructor(private fb: FormBuilder) {}

  ngOnInit(): void {
    this.form = this.fb.group({
      key: [null, [], [this.existsKey]],
    });
  }

  existsKey = (control: AbstractControl) => {
    const handle = of(true);
    return asyncValidator(handle);
  };
}
```

### 判断是否为空

- empty(value: any): boolean
  - value `any` 数值
  - Return `boolean`

```typescript
empty(undefined);
// true
empty("");
// true
empty(0);
// true
empty(false);
// true
empty(null);
// true
empty([]);
// true
empty({});
// true
```

### 生产语言包工具

- factoryLocales(dataset: object, mapping: Map< number, string >)
  - dataset `object` 语言包数据源
  - mapping `Map<number, string>` 语言包文件中的索引映射

```typescript
import("../common.language").then((result) => {
  const lang = factoryLocales(result.default, environment.bit.locale.mapping);
});
```

### 字符串模板

- print(str: string, ...vars: any): string
  - str `string` 字符串文本
  - vars `any[]` 变量，替换$0,$1,$2....$N

可适用于多语言模板

```typescript
print(
  "$0 是遵循 $1 设计规范的 $2 组件库",
  "ng-zorro-antd",
  "Ant Design",
  "Angular UI"
);

// ng-zorro-antd 是遵循 Ant Design 设计规范的 Angular UI 组件库
```

### 字符串脱敏

- privacy(text: string, start: number, end: number): string
  - text `string` 文本
  - start `number` 起始索引
  - end `number` 结束索引

```typescript
privacy("123456789", 3, 6);

// 123***789
```

---

## ListByPage 分页列表

ListByPage 提供了分页列表结构所需的基本条件，创建通常需要 `BitService` 协助，例如：

```typescript
import { Component, OnInit } from "@angular/core";
import { BitService, ListByPage } from "ngx-bit";

@Component({
  selector: "app-welcome",
  templateUrl: "./welcome.component.html",
  styleUrls: ["./welcome.component.scss"],
})
export class WelcomeComponent implements OnInit {
  lists: ListByPage;

  constructor(public bit: BitService) {}

  ngOnInit(): void {
    this.lists = this.bit.listByPage({
      id: "test",
      limit: 10,
      query: [{ field: "username", op: "=", value: "" }],
    });
  }
}
```

| 属性            | 说明                     | 类型                                | 默认值      |
| --------------- | ------------------------ | ----------------------------------- | ----------- |
| `search`        | 搜索字段定义             | `{ [field: string]: SearchOption }` | `{}`        |
| `order`         | 排序字段定义             | `OrderOption`                       | `undefined` |
| `data`          | 分页列表数据             | `any[]`                             | `[]`        |
| `loading`       | 分页列表加载状态         | `boolean`                           | `true`      |
| `limit`         | 分页记录数量             | `number`                            | `0`         |
| `totals`        | 分页总数                 | `number`                            | `0`         |
| `index`         | 分页页码                 | `number`                            | `1`         |
| `checked`       | 分页列表是否全被选中     | `boolean`                           | `false`     |
| `indeterminate` | 分页列表是否不完全被选中 | `boolean`                           | `false`     |
| `batch`         | 是否可进行批量处理       | `boolean`                           | `false`     |
| `checkedNumber` | 分页列表被选中的数量     | `number`                            | `0`         |

### 返回 QuerySchema

- static getQuerySchema(options: SearchOption[]): any[]
  - options `SearchOption[]` 条件数组

通过 `SearchOption[]` 组合成 Laravel Query 的条件数组（ThinkPHP 同样支持）

```typescript
let schema = ListByPage.getQuerySchema([
  { field: "username", op: "=", value: "" },
]);

// console: []

schema = ListByPage.getQuerySchema([
  { field: "username", op: "=", value: "", exclude: [0, null] },
]);

// console: ['username', '=', '']]

schema = ListByPage.getQuerySchema([
  { field: "username", op: "=", value: "kain" },
]);

// console: [['username', '=', 'kain']]

schema = ListByPage.getQuerySchema([
  { field: "username", op: "=", value: null },
  { field: "type", op: "=", value: 0 },
  { field: "ids", op: "in", value: [] },
  { field: "error", op: "=", value: {} },
]);

// console: []
```

### 设置分页列表数据

- setData(data: any[])
  - data `any[]` 数据源

当分页列表请求返回时设置，`getLists` 因酌情处理，这里 `event` 代表分页页码，可直接供 Table 组件使用

```typescript
getLists(refresh = false, event?:any) {
    service.lists(
        this.lists,
        refresh,
        event !== undefined
    ).subscribe(data => {
        this.lists.setData(data);
    });
}
```

### 判断是否包含该字段的搜索条件

- hasSearch(field: string)
  - field `string` 字段名称

通常这被使用在模板上

```html
<nz-space-item *ngIf="lists.hasSearch('username')">
  <nz-input-group nzSearch [nzAddOnAfter]="nzAddOnAfter" style="width: 320px">
    <input
      nz-input
      [bitSearchStart]="lists"
      [placeholder]="bit.l['search']"
      [(ngModel)]="lists.search['username'].value"
      (after)="getLists(true)"
    />
  </nz-input-group>
  <ng-template #nzAddOnAfter>
    <button
      nzSearch
      nz-button
      nzType="primary"
      [bitSearchStart]="lists"
      (after)="getLists(true)"
    >
      <i nz-icon nzType="search"></i>
    </button>
  </ng-template>
</nz-space-item>
```

### 主动触发搜索变动之后

- afterSearch(): Observable< any >
  - Return `Observable<any>`

在不是 `bit-search-change` 与 `bit-search-start` 指令触发的情况下，就需要主动执行：

```typescript
this.lists.afterSearch().subscribe((status) => {
  // ...
});
```

### 主动触发搜索清空之后

- clearSearch(reset: any = {}): Observable< any >
  - reset `any` 重置的数值
  - Return `Observable<any>`

在不是 `bit-search-clear` 指令触发的情况下，就需要主动执行：

```typescript
this.lists
  .clearSearch({
    username: "",
  })
  .subscribe((status) => {
    // ...
  });
```

### 更新分页列表状态

- refreshStatus()

即是否全部选中、不完全选中、是否可执行批量与选中数量，通常列表数据中发生变化需主动执行

```html
<nz-table
  #table
  [nzData]="lists.data"
  [nzLoading]="lists.loading"
  [nzTotal]="lists.totals"
  [nzPageSize]="lists.limit"
  [nzFrontPagination]="false"
  [(nzPageIndex)]="lists.index"
  (nzPageIndexChange)="getLists()"
  nzSize="middle"
>
  <thead>
    <tr>
      <th
        nzShowCheckbox
        nzWidth="65px"
        [(nzChecked)]="lists.checked"
        [nzIndeterminate]="lists.indeterminate"
        (nzCheckedChange)="lists.checkedAll($event)"
      ></th>
      <th>{{bit.l['username']}}</th>
    </tr>
  </thead>
  <tbody>
    <tr *ngFor="let data of table.data">
      <!-- 这里执行 -->
      <td
        nzShowCheckbox
        [(nzChecked)]="data.checked"
        (nzCheckedChange)="lists.refreshStatus()"
      ></td>
      <td>{{data.username}}</td>
    </tr>
  </tbody>
</nz-table>
```

### 更改所有分页列表选中状态

- checkedAll(event: boolean)
  - event `boolean` 选中状态

可附加在主选择器的状态监听中

```html
<nz-table
  #table
  [nzData]="lists.data"
  [nzLoading]="lists.loading"
  [nzTotal]="lists.totals"
  [nzPageSize]="lists.limit"
  [nzFrontPagination]="false"
  [(nzPageIndex)]="lists.index"
  (nzPageIndexChange)="getLists()"
  nzSize="middle"
>
  <thead>
    <tr>
      <!-- 这里执行 -->
      <th
        nzShowCheckbox
        nzWidth="65px"
        [(nzChecked)]="lists.checked"
        [nzIndeterminate]="lists.indeterminate"
        (nzCheckedChange)="lists.checkedAll($event)"
      ></th>
      <th>{{bit.l['username']}}</th>
    </tr>
  </thead>
  <tbody>
    <tr *ngFor="let data of table.data">
      <td
        nzShowCheckbox
        [(nzChecked)]="data.checked"
        (nzCheckedChange)="lists.refreshStatus()"
      ></td>
      <td>{{data.username}}</td>
    </tr>
  </tbody>
</nz-table>
```

### 返回所有被选中的列表

- getChecked(): any[]
  - Return `any[]`

```typescript
const checkedLists = this.lists.getChecked();
```

### 获取当前的页码

- getPage(): Observable<any>
  - Return `Observable<any>`

```typescript
this.lists.getPage().subscribe((index) => {
  // index
});
```

### 主动执行分页页码的持久化记录

- persistence()

```typescript
this.lists.persistence();
```

### 返回查询定义数组

- toQuery(): SearchOption[]

```typescript
const search = this.lists.toQuery();
```

### 返回 QuerySchema

- toQuerySchema(): any[]

将 `search` 组合成 Laravel Query 的条件数组（ThinkPHP 同样支持）

```typescript
const schema = this.lists.toQuerySchema();

// console: []
```
