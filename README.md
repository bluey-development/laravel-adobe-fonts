# Manage self-hosted Adobe Fonts in Laravel apps

[![Latest Version on Packagist](https://img.shields.io/packagist/v/bluey-development/laravel-adobe-fonts.svg?style=flat-square)](https://packagist.org/packages/bluey-development/laravel-adobe-fonts)
[![GitHub Tests Action Status](https://img.shields.io/github/workflow/status/bluey-development/laravel-adobe-fonts/run-tests?label=tests)](https://github.com/bluey-development/laravel-adobe-fonts/actions?query=workflow%3Arun-tests+branch%3Amaster)
[![GitHub Code Style Action Status](https://img.shields.io/github/workflow/status/bluey-development/laravel-adobe-fonts/Check%20&%20fix%20styling?label=code%20style)](https://github.com/bluey-development/laravel-adobe-fonts/actions?query=workflow%3A"Check+%26+fix+styling"+branch%3Amaster)
[![Total Downloads](https://img.shields.io/packagist/dt/bluey-development/laravel-adobe-fonts.svg?style=flat-square)](https://packagist.org/packages/bluey-development/laravel-adobe-fonts)

This package makes self-hosting Adobe Fonts as frictionless as possible for Laravel users. To load fonts in your application, register a Adobe Fonts embed URL and load it with the `@adobefonts` Blade directive.

```php
// config/adobe-fonts.php

return [
    'fonts' => [
        'default' => 'https://use.typekit.net/duhsjsd.css',
        'code' => 'https://use.typekit.net/2uhjdhd.css',
    ],
];
```

```blade
{{-- resources/views/layouts/app.blade.php --}}

<head>
    {{-- Loads Inter --}}
    @adobefonts

    {{-- Loads IBM Plex Mono --}}
    @adobefonts('code')
</head>
```

When fonts are requested the first time, this package will scrape the CSS, fetch the assets from Adobe's servers, store them locally, and render the CSS inline.

If anything goes wrong in this process, the package falls back to a `<link>` tag to load the fonts from Adobe.

## Why we created this package

Adobe Fonts hosts an impressive catalog of fonts, but relying on it has its costs. By hosting fonts on an external domain, browsers need to perform an additional DNS lookup. This slows down the initial page load. In addition, you're directing your visitors to Adobe property, which privacy-minded users might not appreciate.

You can download fonts from Adobe Fonts and self-host them, but it's more work than embedding a code. Keeping up with the latest font version can also be a chore.

This package makes self-hosting Adobe Fonts as frictionless as possible for Laravel users.

## Credits

Credits to Spatie for the [Laravel Google Fonts](https://github.com/spatie/laravel-google-fonts) package. We needed this for Adobe Fonts, so use the Google Fonts package as a starter.

## Installation

You can install the package via composer:

```bash
composer require bluey-development/laravel-adobe-fonts
```

You may optionally publish the config file:

```bash
php artisan vendor:publish --provider="Bluey\AdobeFonts\AdobeFontsServiceProvider" --tag="adobe-fonts-config"
```

Here's what the config file looks like:

```php
return [

    /*
     * Here you can register fonts to call from the @adobefonts Blade directive.
     * The adobe-fonts:fetch command will prefetch these fonts.
     */
    'fonts' => [
        'default' => 'https://use.typekit.net/duhsjsd.css',
    ],

    /*
     * This disk will be used to store local Adobe Fonts. The public disk
     * is the default because it can be served over HTTP with storage:link.
     */
    'disk' => 'public',

    /*
     * Prepend all files that are written to the selected disk with this path.
     * This allows separating the fonts from other data in the public disk.
     */
    'path' => 'fonts',

    /*
     * By default, CSS will be inlined to reduce the amount of round trips
     * browsers need to make in order to load the requested font files.
     */
    'inline' => true,

    /*
     * When something goes wrong fonts are loaded directly from Adobe.
     * With fallback disabled, this package will throw an exception.
     */
    'fallback' => ! env('APP_DEBUG'),

    /*
     * This user agent will be used to request the stylesheet from Adobe Fonts.
     * This is the Safari 14 user agent that only targets modern browsers. If
     * you want to target older browsers, use different user agent string.
     */
    'user_agent' => 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_6) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0.3 Safari/605.1.15',

];
```

## Usage

To add fonts to your application, grab an embed code from Adobe Fonts, register it in the config and use the `@adobefonts` Blade directive.

```php
// config/adobe-fonts.php

return [
    'fonts' => [
        'default' => 'https://use.typekit.net/duhsjsd.css',
        'code' => 'https://use.typekit.net/2uhjdhd.css',
    ],
];
```

```blade
{{-- resources/views/layouts/app.blade.php --}}

<head>
    {{-- Loads Inter --}}
    @adobefonts

    {{-- Loads IBM Plex Mono --}}
    @adobefonts('code')
</head>
```

This will inline the CSS, so the browser needs to do one less round-trip. If you prefer an external CSS file, you may disable the `inline` option in the package configuration.

Fonts are stored in a `fonts` folder on the `public` disk. You'll need to run `php artisan storage:link` to ensure the files can be served over HTTP. If you wish to store fonts in the git repository, make sure `storage/app/public` is not ignored.

If you want to serve fonts from a CDN, you may set up a different disk configuration.

## Prefetching fonts

If you want to make sure fonts are ready to go before anyone visits your site, you can prefetch them with this artisan command.

```bash
php artisan adobe-fonts:fetch
```

### Caveats for legacy browsers

Adobe Fonts' servers sniff the visitor's user agent header to determine which font format to serve. This means fonts work in all modern and legacy browsers.

This package isn't able to tailor to different user agents. With the default configuration, only browsers that can handle WOFF 2.0 font files are supported. At the time of writing, this is >95% of all users according to [caniuse](https://caniuse.com/woff2). Most notably, IE doesn't support WOFF 2.0.

If you need to serve fonts to a legacy browser, you may specify a different user agent string in the configuration. Keep in mind that makes the page load heavier for all visitors, including modern browsers.

## Testing

```bash
composer test
```

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Security Vulnerabilities

Please review [our security policy](../../security/policy) on how to report security vulnerabilities.

## Credits

- [Robin Dirksen](https://github.com/robindirksen1)
- [Bluey Development](https://bluey.dev/?ref=github-laravel-adobe-fonts)
- [Spatie (Laravel Google Fonts)](https://github.com/spatie/laravel-google-fonts)
- [All Contributors](../../contributors)

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
