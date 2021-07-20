# How to install and setup passport in Laravel 8 for using from Vue Js

### To install Laravel Passport, run:

```
composer require laravel/passport
```

### Add the following provider in the config/app.php file:

```php
Laravel\Passport\PassportServiceProvider::class,
```

### Migrate Laravel Passport tables:

```
php artisan migrate
```

### And then install:

```
php artisan passport:install
```

### And now open `AuthServiceProvider.php` file. Add the following routes and expire time for token:

```php
public function boot()
    {
        $this->registerPolicies();

        Passport::routes();

        Passport::tokensExpireIn(Carbon::now()->addDays(15));

        Passport::refreshTokensExpireIn(Carbon::now()->addDays(30));
    }
```

### And uncomment in the `AuthServiceProvider.php` file:

```php
protected $policies = [
         'App\Models\Model' => 'App\Policies\ModelPolicy',
    ];
```

### Now open `config/auth.php` file and set passport as your API driver.

```php
'guards' => [
   'web' => [
       'driver' => 'session',
       'provider' => 'users',
   ],
   'api' => [
       'driver' => 'passport',
       'provider' => 'users',
   ],
],
```

### Add provider in the `config/app.php`

```php
'providers' => [
  ...
  \Laravel\Passport\PassportServiceProvider::class,
  ]
```

### Update the Users Model:

```php
use HasFactory, Notifiable, HasApiTokens;
```

### Add `$middlewareGroups` into `app/Http/Kernel.php`:

```php
'web' => [
            ...
            CreateFreshApiToken::class,
        ],

        'api' => [
            ...
            CreateFreshApiToken::class,
        ],
```

### Edit `app/Http\Middleware\VerifyCsrfToken`:

```php
protected $except = [
        "api/*"
    ];
```

### If adding your routes inside the `web middleware` doesn't work for any reason, then try adding this to `$middleware` into `app/Http/Kernel.php`:

```php
protected $middleware = [
        ...
        StartSession::class,
        ShareErrorsFromSession::class,
    ];
```

### Try to use middleware in `route/api.php`, for example:

```php
Route::middleware('auth:api')->group(function () {
    ...
});
```

### Now `X-XSRF-TOKEN` will be added in the header automatically from your Vue Js code,
