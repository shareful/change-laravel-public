# Change laravel public folder:

1- rename `public` folder to `public_html`.  
2- create new folder next-to `public_html` and name it `laravel-core`.  
3- move all files and folders except `.public_html, .idea, .git, .gitattributes, .gitignore` into `laravel-core` folder.  
4- edit `index.php` in `public_html` folder and change `__DIR__.'/../` to `__DIR__.'/../laravel-core/`. (2 case)  
5- edit `server.php` in `laravel-core` folder and change `/public` to `/../public_html`. (2 case)  
6- edit `AppServiceProviver.php` and add below code in `register` method:  
```php
$this->app->bind('path.public', function() {
  return realpath(base_path().'/../public_html');
});
```

7- edit `webpack.mix.js` and change `mix.` to `mix.setPublicPath('../public_html/').` and change all `public/` to `../public_html/` like below:  
```javascript
mix.setPublicPath('../public_html/')
    .sass("resources/scss/app.scss", "../public_html/css/main.css")
    ....
```
8- edit `.gitignore` and change all `public/` to `public_html/` and add `/laravel-core/` before others.  
9- run `git rm -rf --cached .` in terminal to update `.gitignore`.  
```
/.idea
/.git
/.gitattributes
/.gitignore
/public_html
  /index.php
/laravel-core
  /server.php
  /webpack.mix.js
  /app
    /Providers
      /AppServiceProviver.php
```

An alternative solution is create .htaccess file in `public_html/` and put these lines. This will point every request to `public_html/public/` 
```
RewriteEngine on
RewriteCond %{REQUEST_URI} !^public
RewriteRule ^(.*)$ public/$1 [L]
```


10- Storage path linking to public
```
php artisan storage:link
```

When don't have ssh access then can try via Artisan class. You can create a route in web.php file then pass a parameter to call method 'storage:link' Then access this URL in the browser.

```
Route::get('/storage', function(){
    \Artisan::call('storage:link');
    return "link process successfully completed";
});
```

Second solution is via a php script. Create a simple php file into your public directory just like public/link.php set the file name what ever you want then past bellow code. please remove htaccess file from root if you have. .httacces file should not to be root folder for redirect public/index.php. when you run link.php file. then go to browser write http://domain.com/link.php

If your public folder is `public_html` and laravel core files in `../laravel_core` then 
```
<?php
    $targetFolder = $_SERVER['DOCUMENT_ROOT'].'/../laravel-core/storage/app/public';
    $linkFolder = $_SERVER['DOCUMENT_ROOT'].'/storage';
    symlink($targetFolder,$linkFolder);
    echo 'link process successfully completed';
?>
```

11- Laravel cache clear
Laravel has caching process. it caches so many things. When we manually deploy laravel project so some times it clashed for caching issue. You can simply place the below code in your routes/web.php file of the Laravel application. Then access this URL in the browser to clear the cache of the Laravel application. You can also pass the call function parameter route:clear , config:clear , view:clear

Route::get('/clear-cache', function() {
    Artisan::call('cache:clear');
    return "Cache is cleared";
});
