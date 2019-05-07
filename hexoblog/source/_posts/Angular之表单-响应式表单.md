---
title: Angular之表单-响应式表单
date: 2019-03-02 18:36:13
categories:
  - Angular
tags:
  - Angular
---

# Angular表单之响应式表单

响应式表单提供了一种模型驱动的方式来处理表单输入，其中的值会随时间而变化。响应式表单使用显式的、不可变的方式，管理表单在特定的时间点上的状态。对表单状态的每一次变更都会返回一个新的状态，这样可以在变化时维护模型的整体性。响应式表单是围绕 Observable 的流构建的，表单的输入和值都是通过这些输入值组成的流来提供的，它可以同步访问。

响应式表单还提供了一种更直观的测试路径，因为在请求时你可以确信这些数据是一致的、可预料的。这个流的任何一个消费者都可以安全地操纵这些数据。

响应式表单与模板驱动的表单有着显著的不同点。响应式表单通过对数据模型的同步访问提供了更多的可预测性，使用 Observable 的操作符提供了不可变性，并且通过 Observable 流提供了变化追踪功能。 如果你更喜欢在模板中直接访问数据，那么模板驱动的表单会显得更明确，因为它们依赖嵌入到模板中的指令，并借助可变数据来异步跟踪变化。参见表单概览来了解这两种范式之间的详细比较。

<!-- more -->

## 如何使用

### 首先在 `app.module.ts` 中注入 `ReactiveFormModule` 模块

```code
import { ReactiveFormsModule } from '@angular/forms';

@NgModule({
  imports: [
    // other imports ...
    ReactiveFormsModule
  ],
})
export class AppModule { }
```

### 组件中使用，需要先导入 `FormControl` 类，并创建一个 `FormControl` 实例，保存在类的某个实例中

```code
import { Component } from '@angular/core';
import { FormControl } from '@angular/forms';

@Component({
  selector: 'app-reactiveform',
  templateUrl: './reactiveform.component.html',
  styleUrls: ['./reactiveform.component.css']
})
export class ReactiveformComponent {
  name = new FormControl('');
}
```

模板中使用：

```code
<label>
  姓名:<input type="text" [formControl]="name">
</label>
```

采用插值方式查看表单的值：

```code
<p>Value: {{ name.value }}</p>
```

设置表单的值：

FormControl 提供了一个 setValue() 方法，它会修改这个表单控件的值，并且验证与控件结构相对应的值的结构

```code
setName(){
 this.name.setValue('张三');
}
```

## 表单分组及表单分组嵌套

FormGroup 的实例也能跟踪一组 FormControl 实例（比如一个表单）的表单状态。当创建 FormGroup 时，会根据其名字进行跟踪每个控件。

需要在组件中导入 `FormGroup` 类和 `FormControl`类

```code
import { Component } from '@angular/core';
import { FormGroup, FormControl } from '@angular/forms';

@Component({
  selector: 'app-reactiveform',
  templateUrl: './reactiveform.component.html',
  styleUrls: ['./reactiveform.component.css']
})
export class ReactiveformComponent {
  peopleInfo = new FormGroup({
    name: new FormControl(''), // 模板中 formControlName 使用的是 FormControl 实例
    age: new FormControl(''),

    // FormGroup 嵌套
    address: new FormGroup({
      province: new FormControl('')
    })
  });
}
```

在模板中关联表单，关联表单首先在form标签上将FormGroup实例绑定到 `formGroup`属性上，然后使用 `formControlName`指令关联 `FormControl` 实例名称到具体的表单元素:

```code
<form [formGroup]="peopleInfo">
  姓名：<input type="text" formControlName="name" /><br>
  年龄：<input type="text" formControlName="age" />
  
  <!-- FormGroup 嵌套 -->
  <div formControlName="address">
    省：<input formControlName="province" />
  </div>
</form>
```

## 更新表单的值

官方文档中提供了两种修改，表单值的方法：

* setValue 只能修改单个表单 `this.name.setValue('李四')`
* patchValue 可以对表单对象中定义的所有属性进行修改

```code
// patchValue
this.peopleInfo.patchValue({
  name: '李四',
  address: {
    province: '上海'
  }
})
```

## 使用 FormBuilder 来生成表单控件

`FormBuilder`实际上是 `FormControl`、`FormGroup` 和 `FormArray`的一个工厂方法，用于在组件类中分别生成 `FormControl`、`FormGroup` 和 `FormArray`的实例， `FormBuilder` 服务有三个方法：`control()`、`group()` 和 `array()`。

### 导入`FormBuilder`服务

```code
import { FormBuilder } from '@angular/forms';
```

### 构造函数中注入

```code
constructor(private fb: FormBuilder) {

}
```

### 使用 `Formbuilder` 创建`peopleInfo`实例

```code
peopleInfo = this.fb.group({
  name:[''], // 模板中 formControlName 使用的是 FormControl 实例
  age: [''], // 第一个参数是默认值

  // FormGroup 嵌套
  address: this.fb.group({
    province: ['']
  })
});
```

## 表单验证

```code
import { Validators } from '@angular/forms';
```

## 使用表单数组管理动态控件

FormArray 是 FormGroup 之外的另一个选择，用于管理任意数量的匿名控件。像 FormGroup 实例一样，你也可以往 FormArray 中动态插入和移除控件，并且 FormArray 实例的值和验证状态也是根据它的子控件计算得来的。 不过，你不需要为每个控件定义一个名字作为 key，因此，如果你事先不知道子控件的数量，这就是一个很好的选择。下面的例子展示了如何在 ProfileEditor 中管理一组绰号（aliases）。

### 导入`FormArray`

```code
import { FormArray } from '@angular/forms';
```

### 定义`FormArray`

```code
peopleInfo = this.fb.group({
  name:[''], // 模板中 formControlName 使用的是 FormControl 实例
  age: [''], // 第一个参数是默认值
  address: this.fb.group({
    province: ['']
  })

  // 定义 一个FormArray实例
  testformarray: this.fb.array({[
    this.fb.control('') // 默认添加一个控件
  ]})
});
```

### 访问 `FormArray` 控件

getter 可以轻松访问表单数组各个实例中的别名,表单数组实例用一个数组来代表未定数量的控件。通过 getter 来访问控件很方便，这种方法还能很容易地重复处理更多控件。

```code
get testAliases() {
  return this.peopleInfo.get('testformarray') as FormArray;
}
```

注意：因为返回的控件的类型是 AbstractControl，所以你要为该方法提供一个显式的类型声明来访问 FormArray 特有的语法。

定义一个方法来把一个绰号控件动态插入到绰号 FormArray 中。用 FormArray.push() 方法把该控件添加为数组中的新条目。

```cdoe
addAlias() {
  this.testAliases.push(this.fb.control(''));
}
```

### 模板中显示

```code
<!-- formArrayName指向定义的FormArray实例 -->
<div formArrayName="testformarray">
  <h3>Aliases</h3>
  <button (click)="addAlias()">Add</button>

  <div *ngFor="let item of testAliases.controls; let i=index">
    <!-- The repeated alias template -->
    <label>
      动态控件:
      <input type="text" [formControlName]="i">
    </label>
  </div>
</div>
```

## 响应式表单 API

### 类

| 类              | 说明                                                                                                         |
| --------------- | ------------------------------------------------------------------------------------------------------------ |
| AbstractControl | 所有三种表单控件类（FormControl、FormGroup 和 FormArray）的抽象基类。它提供了一些公共的行为和属性。          |
| FormControl     | 管理单体表单控件的值和有效性状态。它对应于 HTML 的表单控件，比如 `<input>` 或 `<select>`。                   |
| FormGroup       | 管理一组 AbstractControl 实例的值和有效性状态。该组的属性中包括了它的子控件。组件中的顶级表单就是FormGroup。 |
| FormArray       | 管理一些 AbstractControl 实例数组的值和有效性状态。                                                          |
| FormBuilder     | 一个可注入的服务，提供一些用于提供创建控件实例的工厂方法。                                                   |

### 指令

| 指令                 | 说明                                                                   |
| -------------------- | ---------------------------------------------------------------------- |
| FormControlDirective | 把一个独立的 FormControl 实例绑定到表单控件元素。                      |
| FormControlName      | 把一个现有 FormGroup 中的 FormControl 实例根据名字绑定到表单控件元素。 |
| FormGroupDirective   | 把一个现有的 FormGroup 实例绑定到 DOM 元素。                           |
| FormGroupName        | 把一个内嵌的 FormGroup 实例绑定到一个 DOM 元素。                       |
| FormArrayName        | 把一个内嵌的 FormArray 实例绑定到一个 DOM 元素。                       |
