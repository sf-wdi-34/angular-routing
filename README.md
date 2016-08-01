# <img src="https://cloud.githubusercontent.com/assets/7833470/10899314/63829980-8188-11e5-8cdd-4ded5bcb6e36.png" height="60"> Angular Routing

| Objectives |
| :--- |
| Explain the advantages of client-side routing  |
| Implement client-side routing in a single-page app using Angular's `ngRoute` module |
| Be aware of `ng-view` and `ng-href` |

In a client-side framework like Angular, routing is not managed by the server, but by the client itself. Angular detects the path of your URL and maps that URL to a controller and template.

## Client-side routing?

What? Why?

Imagine that you're a goat-herder.  You have an app to keep track of your :goat:s.  Such an app might have

* An **index** route that displays the list of goats.    
* A **show** page that displays details about a specific goat.

OK, maybe it looks a little like this:

![](goats_app.png)

That seems alright, we can track each goat and we can click it to see more info about the goat.  

##### but wait...what if I want to share a goat?

In the S.P.A.s you've built before, you've mostly stuck to just one URL: `localhost:3000` or maybe `http://pathetically-dismal-bog.herokuapp.com`  That didn't make it very easy for users to bookmark or share URLs from your site.  There was no way for your user to share their favorite page on your site because when they sent the URL `http://pathetically-dismal-bog.herokuapp.com` to their friend it just showed the default homepage.  

> S.P.A.s??  Single Page Apps.

##### Enter client-side routing

* What if each view in your app changed the browser URL?
* What if that URL was unique depending on what the user was looking at?
* What if it looked-like it was working just like a non-SPA/multi-page app without rendering an entirely new page everytime?
* What if the **state** of the page was reflected in the **state** of the URL?

**Now you can!**

Take a look at the URLs in the goat-app above.  Notice how they indicate where we're at?  We're going to use a front-end/client-side router to add this feature today.  

#### What does a client-side router do?

1. It ties the URL in the browser to the **state** of the current page.
 
   If a user comes back to the same URL, they get to see the same **view**(s).
1. It lets us have front-end **views** that are injected inside of **layouts**.


#### Views & Layouts

Most apps have a certain amount of content that appears on every page.  It might be:

* a menu
* a header
* a sidebar
* a footer

![http://www.funnyant.com/angularjs-ui-router/](http://www.funnyant.com/wp-content/uploads/2014/12/multiple-views-sketch-1024x768.jpg)  - [image credit](http://www.funnyant.com/wp-content/uploads/2014/12/multiple-views-sketch-1024x768.jpg)


Using a **view template** we can keep the content that *changes* in each page separate from the static content.

The content that stays the same will live in our **layout**.  In other words, maybe our *sidebar*, *footer*, and *header* all stay the same.  We keep those in the layout.  The *content* that we change, depending on the URL, goes into a **view template**.  

##### back to goats...

![view and layout](views_layouts_malcolm.png)

In our goat-app above we have a **"Goat Tracker"** title on each page.  That can be in our main layout.  Then we just apply a different **view** when viewing the list of goats or a goat *show* page.  The goat **index** template or **show** template is *injected* into the layout.

> Note: Some client-side routers allow you to have multiple views per layout. (ui-router, Component Router)
> Others do not (ngRoute)


## ngRoute background

Angular first shipped with a simple routing module that let you connect one URL with one controller and one template. Very quickly developers in the Angular community wanted to go beyond this simple coupling and leverage Angular's ability to nest controllers, views, and scopes together to make more complex and modular client-side applications. The Angular community decided to extract the standard Angular routing module into its own separate module called `ngRoute`.

Angular's built-in `ngRoute` will most likely have all the routing features you need for your apps as you're first learning Angular. As your Angular apps become more robust, it's worth looking into the third-party `ui-router` module, which supports nested views, multiple named views, and "states".

<a href="http://stackoverflow.com/questions/21023763/angularjs-difference-between-angular-route-and-angular-ui-router" target="_blank">Difference between angular-route and angular-ui-router</a> [Stack Overflow]

Note that there is also another way to do routing in Angular 1.5 and 2.0 using components.  We won't be talking about this.


## ngRoute Setup

#### Add ngRoute to your Angular app

1. Include the CDN for `ngRoute` in your `index.html`, after the Angular CDN:

  ```html
  <!-- index.html -->

  <!DOCTYPE html>
  <html ng-app="sampleApp">
  <head>
    <base href="/">

    <!-- angular -->
    <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.5.3/angular.js"></script>

    <!-- angular route -->
    <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.5.3/angular-route.js"></script>

    <!-- custom script (angular app) -->
    <script type="text/javascript" src="app.js"></script>

  </head>
  <body>
    ...
  </body>
  </html>
  ```

2. Include the `ngRoute` module in your Angular app's list of dependencies:

  ```js
  // app.js

  var app = angular.module('sampleApp', ['ngRoute']);
  ```

#### Create templates for your app's "views"

As your app grows, your single `index.html` file is going to get very big and messy. To combat this, you're going to turn your `index.html` file into a "layout template." Depending on the current route, the Angular router (`ngRoute`) will include the correct template, or view, in the layout to be displayed to the user.  This layout can be thought of as a hole in the page into which ngRoute will pour the template; the main content on the page.

1. Create a folder in the top level of your application called `templates`.

  ```sh
  ➜  mkdir templates
  ```

2. Create some templates for the different "views" in your application. We'll start with templates for the homepage and the about page. Make sure to create your templates inside the `templates` folder.

  ```sh
  ➜  touch templates/home.html
  ➜  touch templates/about.html
  ```

3. In `index.html`, add the `ng-view` directive inside the `<body>` tag.  The `ng-view` is where our template html will go when the template is rendered.
  ```html
  <!-- index.html -->

  <body>
    <div ng-view></div>
  </body>
  ```

#### Configure your routes

1. Right after you define your Angular app, use the `.config` method to set up your routes. Your routes are configured by injecting `$routeProvider` into `config`'s dependencies and using the `$routeProvider.when()` method to define your routes.

  ```js
  // app.js

  angular.module('sampleApp', ['ngRoute'])
  	.config(config);
  
  
  config.$inject = ['$routeProvider'];
  function config(   $routeProvider   ) {
    $routeProvider
      .when('/', {
        templateUrl: 'templates/home.html',
        controller: 'HomeController',
        controllerAs: 'homeCtrl'        
      })
      .when('/about', {
        templateUrl: 'templates/about.html',
        controller: 'AboutController',
        controllerAs: 'aboutCtrl'
      })
      .otherwise({
        redirectTo: '/'
      });
  };
  ```

#### Set up your controllers

Each route that you just set up points to its own controller, which contains it's own scope. Following the homepage and about page example above, you'll need two controllers, `HomeCtrl` and `AboutCtrl`.

1. After configuring your routes, define your controllers in `app.js`:

  ```js
  // app.js

  angular.module('sampleApp', ['ngRoute'])
    .config(config)
    .controller('HomeController', HomeController)
    .controller('AboutController', AboutController);
    
    
  config.$inject = ['$routeProvider'];
  function config(   $routeProvider   ) {
    ...
  }

  HomeController.$inject = [];
  function HomeController() {
    ...
  };

  AboutController.$inject = [];
  function AboutController() {
    ...
  };
  ```

If you're having trouble getting your routes to work, the first thing to check is the spelling and file path of the `templateUrl` and the spelling of your controller (also double check that you properly defined the controller).


#### Accessing URL params

In some cases, you'll want to use `ngRoute` to access URL parameters in a controller, just like you're used to doing on the server-side. Say we have an application that keeps track of books for the local library. The route `/books/:bookId` should `show` one particular book based on the `bookId`.

```js
angular.module('sampleApp', ['ngRoute'])
	.config(config);

config.$inject = ['$routeProvider'];
function config(   $routeProvider   ) {
  $routeProvider
    .when('/books/:bookId', {
      templateUrl: 'templates/books/show.html',
      controller: 'BooksShowController',
      controllerAs: 'booksShowCtrl'
    });
}

BooksShowController.$inject = ['$routeParams'];
function BooksShowController(   $routeParams   ) {
  var vm = this;

  var bookId = $routeParams.bookId;
  // use `bookId` to find specific book
};
```

#### Linking to other routes

What if you want to link to another route in your router.  Maybe you'd like users to be able to click a book in the BooksIndexController's view and be taken to the right page. 

1. In your HTML for each book you could make use of `ng-href`.

	```html
	<!-- templates/books/index.html -->
	<div ng-repeat="book in booksIndexCtrl.books">
	
		<a ng-href="/books/{{book.id}}">{{book.name}}</a>
	
	</div>
	```
	
	The `ng-href="/books/{{book.id}}"` attribute tells angular to link this span to `/books/132` (if the `book.id` is 132).


# More resources

* [SO: difference between ngRoute and ui-router](http://stackoverflow.com/questions/21023763/angularjs-difference-between-angular-route-and-angular-ui-router)
