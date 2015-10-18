# HTTP Request Interceptor
It's a request Interceptor for angularJs application . It shows a loading icon while Any of your api ajax call is pending . It eliminates the need of showing individual loading icon for each API call . Following interceptor will intercept all your ajax calls and will show the loading icon while any of the api requests is pending.

###### Prerequisite
- Following code can only be used in an angularJs 1.* project . This code is ES6 compatiable
- Project should have [lodash](https://lodash.com/)
- To show error modal we are using BootStrap modal.

###### Code and instruction.
1)  **Include following file in your angularJs project** and include that file in index.html . *Instead of* creating a new file you can also add the config block to your `app.js` file i.e. only `.config(function($httpProvider){ ...})` part.  

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

2)  **Remove hard coded Parts from the above code**

Code above has some assumptions i.e. all your API urls include the term `/api/` . If this doesn't hold true replace the string in line `url.indexOf('/api/')` with `url.indexOf('aCommonTermThats presentInAllYourAPIURL')` . Also don't forget to change the `yourAppNAME` in the 1st line with your application's name.


3)   **HTML  Code for Loading div**

If size of loader in action array is > 0 that means some ajax req are pending and hence show loader.
```
 <div style="position: absolute" ng-hide="loaderInAction.length === 0">
    Loading ...
 </div>
```

4)   **HTML  Code for Error Modal (Using bootstrap modal)**

```
 <div id="j-serverError" class="modal fade" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <button type="button" class="close" data-dismiss="modal">&times;</button>
          <h4 class="modal-title">Error</h4>
        </div>
        <div class="modal-body">
          <p>{{serverError}}</p>
        </div>
      </div>
    </div>
 </div>
```

Now you have a global handler for pending request and a global error handler as well . That's it . Thank you :) 


