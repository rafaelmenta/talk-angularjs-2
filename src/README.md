<!--

WARNING!! DON'T EDIT THE FILE README.md on the root of the project, that one is a GENERATED FILE!

You should just edit the source file at src/README.md - the one which stars with ## @@title

-->

## @@title

<img src="img/angularjs.png" />

@@author @ [Avenue Code](http://www.avenuecode.com)

*@@email*

@@date

---

## Agenda

 - Directives
  - Custom directives
 - Services x Factories x Providers
 - URLs
 - Filters
 - Events
  - Broadcast x Emit
 - $watch, $digest, $apply
 - Conclusion
 - Challenge

----

## Prerequisites

- Intermediate Javascript
- Design Patterns
- AngularJS basics

---

## Directives

![Directives](img/directives.jpg "Directives!")

- Independent web components
- Commonly used for DOM manipulation

----

## How it works

AngularJS:

1. Looks for directives (built-in & customs)
2. Compiles using $compile()
  - It keeps track and sorts directives by priority
3. Links with created scope
  - Registering listeners or Setting up $watcher

After these steps 2-way data-binding is set.

----

## Useful directives

- ng-repeat
- ng-show
- ng-class
- ng-enabled
- ng-switch
- ng-hide
- ng-href
- ng-src

----

## Custom Directives

> If you end writing $(element) somewhere in your controller then it's an indication
that you need a directive.

----

## Blueprint

```javascript
app.directive('directiveName', function (injectables) {
  return {
    restrict: 'A',
    template: '<div></div>',
    templateUrl: 'directive.html',
    replace: false,
    priority: 0,
    transclude: false,
    scope: false,
    terminal: false,
    require: false,
    controller: function($scope, $element, $attrs, $transclude, otherInjectables) { ... },
    compile: function compile(tElement, tAttrs, transclude) {
      return {
        pre: function preLink(scope, iElement, iAttrs, controller) { ... },
        post: function postLink(scope, iElement, iAttrs, controller) { ... }
      }
    },
    link: function postLink(scope, iElement, iAttrs) { ... }
  };
});
```

---

## Services

- Ready functions (No need of preprocessing)
- Shared data through application
- Expect a *constructor* function
- Generate singleton objects

```javascript
myApp.service('GreetService',  function(){
  this.greet = function (name) {
    return 'Hello ' + name;
  };
});

function MyController(GreetService) {
  GreetService.greet('World');
}
```

----

## Factories

- Allow logic before object creation
- Expect a function that factory will *invoke*
- Controllers have single instances

```javascript
myApp.factory('GreetService', function () {
  var GreetClass = function(name) {
    this.name = name;
    this.greet = function () {
      return 'Hello ' + this.name;
    }
  }
  return GreetClass;
});
function MyController(GreetService) {
  var world = new GreetService('World'),
      angular = new GreetService('Angular');

  world.greet();
  angular.greet();
```

----

## Providers

- Defined at configuration level
- Runs *before injection* takes place

```javascript
myApp.provider('Greet', function () {
  var hello = true;
  this.sayHello = function(sayHello){
    sayHello = sayHello;
  };

  var HelloClass = function(name) {
   this.name = name;
   this.greet = function () { return 'Hello ' + this.name; }
  };

  var ByeClass = function(name) {
   this.name = name;
   this.greet = function () { return 'Bye ' + this.name; }
  };

  this.$get = function() {
    return sayHello ? HelloClass : ByeClass;
  };
});

myApp.config(function(Greet){
  Greet.sayHello(false);
});

function MyController(Greet) {
  var world = new Greet('World'),
      angular = new Greet('Angular');
  world.greet();
  angular.greet();
}
```

----

## Worth mentioning

There are also *constant* and *value* which work similar,
but constant is defined before configuration level.

```javascript
myApp.value('lang','PT-BR');
function MyController(lang) {
  ...
};

myApp.constant('PUBLIC_API_TOKEN','1QAZ2WSX3EDC');
myApp.config(function(PUBLIC_API_TOKEN){
  ...
});
```

---

## URLs - Routing

```javascript
myApp .config(['$routeProvider', function($routeProvider) {
  $routeProvider.
    when('/list', {
      templateUrl: 'views/list.html',
      controller: 'ListController'
    }).
    when('/add', {
      templateUrl: 'views/add.html',
      controller: 'AddController'
    }).
    otherwise({
      redirectTo: '/list'
    });
  }]);
```

----

## URLs - Controlling

Angular provides a *$location* service to handle URLs
For instance, for http://localhost:9000/#/todo?a=b

```javascript
function MyController($location) {
  $location.path(); // /todo
  $location.url(); // /todo?a=b
  $location.search(); // Object { a="b"}
  $location.search().a; // b
  $location.absUrl(); // http://localhost:9000/#/todo?a=b
  $location.port(); // 9000
}
```

Tip: some of these functions (like url, search) are also setters.

----

## URLs - Eventing

- $locationChangeStart
  - Broadcasts before URL actually changes
- $locationChangeSuccess
  - Broadcasts right after URL changes

> Usage example: handling permissions

---

## Filters

- Most often you will need to filter by a single property
   - ```html
      Search by name: <input type="text" ng-model="search.name">
      <div ng-repeat="product in products | filter:search">
        ...
     ```
- When a complex filter is needed, custom filters take place
  - ```javascript
      myApp.filter('ageFilter', function() {
        return function(player) {
          return (player.age > MIN_AGE && player.age < MAX_AGE);
        };
      });
    ```

---

## Events

Whenever an event which affects other components takes place
the best way to set this behavior is through events.

- ```javascript
    function MyCtrl = function($scope) {
      $scope.$on('logout', function(args) {
        // redirects user to /index
      });
    }
  ```
- $scope.emit('logout')
  - Communicate event from child to parent
- $scope.broadcast('logout')
  - Communicate event from parent to child

---

## $watch

- $watch can watch any value and trigger a function when it's changed
  - ```javascript
      // Will watch $scope.name
      $scope.$watch('name', function(newVal, oldVal) {
        console.log(newVal, oldVal);
      });
    ```

----

## $digest, $apply

- $digest loops through all thee *watchers on the scope it was called and
also on children scope*
  - It evaluates if any of them has changed and execute bound handlers
- $apply is a wrapper around $digest that evaluates expression prior to calling $digest
  - Often used when external libraries outside of 'Angular World' are used
  - ```javascript
      $scope.$apply(function(){
        $scope.name = 'test';
      });
    ```

---

## Conclusion

- AngularJS provides different ways of solving the same problem. It's up to us
decide which way fits better on system's architecture
- AngularJS can work well of other libraries but it will need some handling in
order to work as expected

---

## Reference

- http://www.benlesh.com/2013/08/angularjs-watch-digest-and-apply-oh-my.html
- http://trochette.github.io/Angular-Design-Patterns-Best-Practices
- http://www.yearofmoo.com/2012/10/more-angularjs-magic-to-supercharge-your-webapp.html
- http://amitgharat.wordpress.com/2013/06/08/the-hitchhikers-guide-to-the-directive/
- http://elikirk.com/2013/07/22/angularjs-communication-between-controllers/
- http://angular-tips.com/blog/2013/08/watch-how-the-apply-runs-a-digest/
- http://docs.angularjs.org/guide

---

## Challenge

- Create a directive which renders user information
- HTML template should render (dynamically) a table with each property and value
  - ```html
      <userinfo user='myUser'></userinfo>
    ```
    generates
    ```
      | property   | value         |
      | -----------| --------------|
      | first_name | john          |
      | email      | john@email.com|
    ```


*Submit the code on your Github account*

---

## Thank you!

 Questions?
