# requestInterceptor
It's a requestInterceptor for angularJs application . It shows a loading icon while Any of your api ajax call is pending . It eliminates the need of showing individual loading icon for each API call . Following interceptor will intercept all your ajax calls and will show the loading icon while any of the requests is pending

###### Prerequisite
- Following code can only be used in an angularJs 1.* project . This code is ES6 compatiable

###### Code and instruction.
1.  **Include following file in your angularJs project** and include that file in index.html . *Instead of* creating a new file you can also add the config block to your `app.js` file i.e. only `.config(function($httpProvider){ ...})` part.  

```
  angular.module('yourAppNAME').config(function($httpProvider) {
    $httpProvider.interceptors.push(function($q, $rootScope) {
      $rootScope.loaderInAction = [];
      return {
        request: function(config) {
          var url = config.url;
          if (url.indexOf('/api/') > -1) {
            $rootScope.loaderInAction.push(url);
          }
          return config || $q.when(config);
        },
        requestError: function(request) {
          var url = request.config.url;
          $rootScope.loaderInAction = _.without($rootScope.loaderInAction, url);
          return $q.reject(request);
        },
        response: function(response) {
          var url = response.config.url;
          $rootScope.loaderInAction = _.without($rootScope.loaderInAction, url);
          return response || $q.when(response);
        },
        responseError: function(response) {
          var url = response.config.url;
          $rootScope.loaderInAction = _.without($rootScope.loaderInAction, url);
          if (response.status >= 400) {
            $rootScope.serverError = response.data.error.message;
            $('#j-serverError').modal('show');
          }
          return $q.reject(response);
        }
      };
    });
  })
```
