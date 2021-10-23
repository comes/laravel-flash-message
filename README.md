# Flash multiple advanced messages with both text, messages and links

[![Latest Version on Packagist](https://img.shields.io/packagist/v/bilfeldt/laravel-flash-message.svg?style=flat-square)](https://packagist.org/packages/bilfeldt/laravel-flash-message)
[![GitHub Tests Action Status](https://img.shields.io/github/workflow/status/bilfeldt/laravel-flash-message/run-tests?label=tests)](https://github.com/bilfeldt/laravel-flash-message/actions?query=workflow%3Arun-tests+branch%3Amain)
[![GitHub Code Style Action Status](https://img.shields.io/github/workflow/status/bilfeldt/laravel-flash-message/Check%20&%20fix%20styling?label=code%20style)](https://github.com/bilfeldt/laravel-flash-message/actions?query=workflow%3A"Check+%26+fix+styling"+branch%3Amain)
[![Total Downloads](https://img.shields.io/packagist/dt/bilfeldt/laravel-flash-message.svg?style=flat-square)](https://packagist.org/packages/bilfeldt/laravel-flash-message)

An opinionated solution for flashing multiple advanced messages from the backend and showing these on the frontend using customizable Tailwind CSS alerts.

## Installation

Install the package via composer and you are ready to add messages that show these on the frontend.

```bash
composer require bilfeldt/laravel-flash-message
```

**Optional:** In case you wish to use the [message flashing](https://laravel.com/docs/master/responses#redirecting-with-flashed-session-data) feature allowing messages to be made available on the next request (usefull in combination with redirects) simply add the `ShareMessagesFromSession` middleware to the `web` group defined in `$middlewareGroups` just after the `ShareErrorsFromSession`:

```php
// app/Http/Kernel.php

/**
 * The application's route middleware groups.
 *
 * @var array
 */
protected $middlewareGroups = [
    'web' => [
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Laravel\Jetstream\Http\Middleware\AuthenticateSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \Bilfeldt\LaravelFlashMessage\Http\Middleware\ShareMessagesFromSession::class, // <------ ADDED HERE
        \App\Http\Middleware\VerifyCsrfToken::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],
    ...
```

## Usage

```php
<?php

namespace App\Http\Controllers;

use Bilfeldt\LaravelFlashMessage\Message;
use Illuminate\Http\Request;

class PostController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @param  Request  $request
     */
    public function index(Request $request)
    {
        $message = Message::warning('This is a simple message intended for you') // message/success/info/warning/error
            ->title('This is important')
            ->addMessage('account', 'There is 10 days left of your free trial')
            ->link('Read more', 'https://example.com/signup');
            
        return view('posts')->withMessage($message);
    }
}
```

Sometimes a redirect is returned to the user instead of a view. In that case the message must be flashed to the session so that they are available on the subsequent request. This is simply done by flashing the `$message`:

```php
<?php

namespace App\Http\Controllers;

use Bilfeldt\LaravelFlashMessage\Message;
use Illuminate\Http\Request;

class PostController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @param  Request  $request
     */
    public function index(Request $request)
    {
        Message::warning('This is a simple message intended for you') // message/success/info/warning/error
            ->title('This is important')
            ->addMessage('account', 'There is 10 days left of your free trial')
            ->link('Read more', 'https://example.com/signup')
            ->flash(); // This will flash the message to the Laravel session
            
        return redirect('/posts');
    }
}
```

Note that for this to work you will need to add the `ShareMessagesFromSession` middleware to `app/Http/Kernel.php` as described in the [installation section](#installation) above.

### Show messages

Once messages have been passed to the frontend these can be shown by simply using the following component in any view file:

```php
<x-flash-alert-messages />
```

## Tip

You might have a layout where you would always like to flash the messages above the main content or just below the title. You can simply add the `<x-flash-message.alert />` to your layout file in the place you would like the messages to show and that way you do not need to call this in multiple views.

## Testing

```bash
composer test
```

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## Credits

- [Anders Bilfeldt](https://github.com/bilfeldt)
- [iAmine](https://tailwindcomponents.com/u/iaminos) for the [Alert blade components](https://tailwindcomponents.com/component/alerts-components)

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
