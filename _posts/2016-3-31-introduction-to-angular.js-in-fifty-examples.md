---
layout: post
title: Introduction to Angular.js in 50 Examples
tags: [Angular.js]
---

[part1](https://www.youtube.com/watch?v=TRrL5j3MIvo)<br>
[part2](https://www.youtube.com/watch?v=6J08m1H2BME)<br>
[Code](https://github.com/curran/screencasts/tree/gh-pages/introToAngular)

[ng-newsletter](http://www.ng-newsletter.com/)

<!--more-->


### About routing and caching

{% highlight js %}

var appOne = angular.module('appOne', ['ngRoute']);

appOne.config(function($routeProvider) {
  $routeProvider.
    when('/', {
      templateUrl: 'app/one/country-list.html',
      controller: 'CountryListController'
    }).
    when('/:countryName', {
      templateUrl: 'app/one/country-detail.html',
      controller: 'CountryDetailController'
    }).
    otherwise({
      redirectTo: '/'
    });
});

appOne.factory('getCountries', function($http) {

  var cacheData;

  function getData(callback) {
    $http({
      method: 'GET',
      url: 'assets/files/country.json',
      cache: true
    }).success(callback);
  }
  // alt
  // function getData(callback) {
  //   if (cacheData) {
  //     callback(cacheData);
  //   } else {
  //     $http.get('assets/country.json').success(function(data) {
  //       cacheData = data;
  //       callback(data);
  //     });
  //   }
  // }

  return {
    list: getData,
    find: function(name, callback) {
      getData(function(data) {
        var country = data.filter(function(entry) {
          return entry.name === name;
        })[0];
        callback(country);
      });
    }
  };

  // return {
  //   list: function(callback) {
  //     $http.get('assets/files/country.json').success(callback);
  //   },
  //   find: function(name, callback) {
  //     $http.get('assets/files/country.json').success(function(data) {
  //       var country = data.filter(function(entry) {
  //         return entry.name === name;
  //       })[0];
  //       callback(country);
  //     });
  //   }
  // };
});

appOne.controller('CountryListController', function($scope, $http, getCountries) {
  getCountries.list(function(countries) {
    $scope.countries = countries;
  });
});

appOne.controller('CountryDetailController', function($scope, $routeParams, getCountries) {
  $scope.name = $routeParams.countryName;

  getCountries.find($scope.name, function(country) {
    $scope.country = country;
  });

  // $http.get('assets/country.json').success(function(data) {
  //   $scope.country = data.filter(function(entry) {
  //     return entry.name === $scope.name;
  //   })[0];
  //   console.log($scope.country);
  // });
});

appOne.filter('encodeURI', function() {
  return window.encodeURI;
});


// appOne.controller('CountryCtrl', ['$scope', '$http', function(scope, http) {
//   http.get('assets/country.json').success(function(data) {
//     scope.countries = data;
//     scope.sortField = 'population';
//     scope.reverse = true;
//   });
// }]);

{% endhighlight %}


### About searching and sorting

{% highlight html %}
<div>search:
  <input type="text" ng-model="query">
</div>
<div>sort:
  <a href="" ng-click="sortField='name';reverse=!reverse">country</a> | <a href="" ng-click="sortField='population';reverse=!reverse">population</a>
</div>
<ul>
  <li ng-repeat="country in countries"><a href="#/{{country.name | encodeURI}}">{{country.name}}</a></li>
</ul>
<table>
  <tr>
    <th ng-click="sortField='name';reverse=!reverse">Country</th>
    <th ng-click="sortField='population';reverse=!reverse">Population</th>
    <th>Flag</th>
    <th ng-click="sortField='capital';reverse=!reverse">Capital</th>
    <th ng-click="sortField='-gdp';reverse=!reverse">GDP</th>
  </tr>
  <tr ng-repeat="country in countries | filter:query | orderBy:sortField:reverse">
    <td>{{country.name}}</td>
    <td>{{country.population | number}}</td>
    <td><img ng-src="{{country.flagURL}}" width="100"></td>
    <td>{{country.capital}}</td>
    <td>{{country.gdp | currency}}</td>
  </tr>
</table>
{% endhighlight %}



### Creating a custom Angular directive

["Writing Directives" talk by Misko Hevery](https://www.youtube.com/watch?v=WqmeI5fZcho)<br>
[Build custom directives with AngularJS](http://www.ng-newsletter.com/posts/directives.html)

