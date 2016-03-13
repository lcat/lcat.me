---
layout: post
title: angular repeat validation
tags: [angular, validation, repeat]
---

项目angular版本1.2.22，有个表单里面有个动态repeat的form-group。


```html
<form name="form">
  <a href="" ng-click="add()">add one</a>
  <div ng-repeat="person in persons">
    <input type="text" name="person_{ {$index} }" ng-model="person.name" required />
    <span class="error" ng-show="form['person_{ {$index} }'].$invalid">required</span>
  </div>
</form>
```

```js
  $scope.persons = [];
  $scope.add = function() {
    $scope.persons.push({
      name: ''
    })
  }
```
[plnkr demo](http://embed.plnkr.co/D9UkQvOuAcjpdUDBScOQ/preview)

不是我们想要的效果。然后把angular版本换成1.3.0，然后就可以了，可以了..

查了查文档，发现另一种方法

```html
<form name="form">
  <a href="" ng-click="add()">add one</a>
  <div ng-repeat="person in persons" ng-form="subForm">
    <input type="text" name="person" ng-model="person.name" required />
    <span class="error" ng-show="subForm['person'].$invalid">required</span>
  </div>
</form>
```

```js
  $scope.persons = [];
  $scope.add = function() {
    $scope.persons.push({
      name: ''
    })
  }
```

[plnkr demo](http://plnkr.co/edit/6esVwBX7NPxfHqlmbdWu?p=preview)

解决..