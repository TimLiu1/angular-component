##### angular1.5中component组件详解

###### angular1.5 component组件设计思想

angular1.5中的component组件是为了能够平稳过渡到angular2加入的新组件。也就是为了让前端更组件化，权责更清晰，首先我们来简单的创建一个组件
```
angular.module("app", [])
  .component("helloWorld",{
      template: 'Hello World!'
  });
```
```
<div ng-app="app"> 
  <hello-world> </hello-world>
</div>
```
这样页面就会出现Hello world

如果一个单页面运用含有几个组件时，子组件需要和父组件通信，通信一共包含以下几种场景
- 传输字符串到子组件：

```
<!-- 字符串传输给子组件 -->
<!DOCTYPE html>
<html ng-app="app">

<head>
    <meta charset="utf-8">
    <title>angular-component</title>
    <script src="http://cdn.bootcss.com/angular.js/1.5.5/angular.min.js"></script>
</head>

<body>
    <hello-world name="tim"></hello-world>
</body>
<script>
    angular.module("app", []).component("helloWorld", {
        template: 'Hello {{$ctrl.name}}!',
        bindings:{name:'@'}
    })
</script>

</html>


```

网页会输出 Hello tim。
组件默认会绑定一个叫$ctrl的控制器。

- 实现组件的数据的单项绑定

```
<!--数据的单向流  -->
<!DOCTYPE html>
<html ng-app="app">

<head>
    <meta charset="utf-8">
    <title>angular-component</title>
    <script src="http://cdn.bootcss.com/angular.js/1.5.5/angular.min.js"></script>
</head>

<body ng-controller="appCtrl">
    <input type="text" ng-model="name" />
    <hello-world name="name"></hello-world>
</body>
<script>
    angular.module("app", []).component("helloWorld", {
        template: "Hello {{$ctrl.name}}!",
        bindings: { name: '<' }
    }).controller("appCtrl", function ($scope) {
        $scope.name = "tim"
    })
</script>

</html>
```
- 实现组件的数据的双向绑定

```
<!--数据的双向绑定  -->
<!DOCTYPE html>
<html ng-app="app">

<head>
    <meta charset="utf-8">
    <title>angular-component</title>
    <script src="http://cdn.bootcss.com/angular.js/1.5.5/angular.min.js"></script>
</head>

<body ng-controller="appCtrl">
    <input type="text" ng-model="name" />
    <hello-world name="name"></hello-world>
</body>
<script>
    angular.module("app", []).component("helloWorld", {
        template: "Hello !<input type='text' ng-model='$ctrl.name' /> ",
        bindings: { name: '=' }
    }).controller("appCtrl", function ($scope) {
        $scope.name = "tim"
    })
</script>

</html>
```
- 事件回调，子组件处理完逻辑之后回调父组件

```
<!--函数回调  -->
<!DOCTYPE html>
<html ng-app="app">

<head>
    <meta charset="utf-8">
    <title>angular-component</title>
    <script src="http://cdn.bootcss.com/angular.js/1.5.5/angular.min.js"></script>
</head>

<body ng-controller="appCtrl">
    <input type="text" ng-model="name" />
    <hello-world call-step="dealStep($event)"></hello-world>
</body>
<script>
    angular.module("app", []).component("helloWorld", {
        template: "<button ng-click='step($event)'>回调</button> ",
        bindings: { name: '=',callStep:"&" },
        controller: function ($scope) {
            var vm = this;
            $scope.step = function () {
                console.log('234')
                vm.callStep({
                    $event: {
                        name: 'lisa'
                    }
                });
            }
        }
    }).controller("appCtrl", function ($scope) {
        $scope.name = "tim";
        $scope.dealStep = function (event) {
            $scope.name = event.name;
        }
    })

</script>
</html>
```
