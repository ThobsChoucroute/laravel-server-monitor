# An easy to use powerful server monitor

[![Latest Version on Packagist](https://img.shields.io/packagist/v/spatie/laravel-server-monitor.svg?style=flat-square)](https://packagist.org/packages/spatie/laravel-server-monitor)
![GitHub Workflow Status](https://img.shields.io/github/workflow/status/spatie/laravel-server-monitor/run-tests?label=tests)
[![Total Downloads](https://img.shields.io/packagist/dt/spatie/laravel-server-monitor.svg?style=flat-square)](https://packagist.org/packages/spatie/laravel-server-monitor)

We all dream of servers that need no maintenance at all. But unfortunately in reality this is not the case. Disks can get full, processes can crash, the server can run out of memory...

This package keeps an eye on the health of all your servers. There are a few [checks that come out of the box](https://docs.spatie.be/laravel-server-monitor/v1/monitoring-basics/built-in-checks). [Adding new checks](https://docs.spatie.be/laravel-server-monitor/v1/monitoring-basics/writing-your-own-checks) is a breeze.

When something goes wrong it can [notify you](https://docs.spatie.be/laravel-server-monitor/v1/monitoring-basics/notifications-and-events) via Slack or mail.

## Support us

[<img src="https://github-ads.s3.eu-central-1.amazonaws.com/laravel-server-monitor.jpg?t=1" width="419px" />](https://spatie.be/github-ad-click/laravel-server-monitor)

We invest a lot of resources into creating [best in class open source packages](https://spatie.be/open-source). You can support us by [buying one of our paid products](https://spatie.be/open-source/support-us).

We highly appreciate you sending us a postcard from your hometown, mentioning which of our package(s) you are using. You'll find our address on [our contact page](https://spatie.be/about-us). We publish all received postcards on [our virtual postcard wall](https://spatie.be/open-source/postcards).

## Documentation

The full documentation is available on [our documentation site](https://docs.spatie.be/laravel-server-monitor).

## Installation

You can install this package via composer using this command:

```bash
composer require spatie/laravel-server-monitor
```

In Laravel 5.5 the service provider will automatically get registered. In older versions of the framework, you must install the service provider:

```php
// config/app.php
'providers' => [
    ...
    Spatie\ServerMonitor\ServerMonitorServiceProvider::class,
];
```

You can publish the migrations with:
```bash
php artisan vendor:publish --provider="Spatie\ServerMonitor\ServerMonitorServiceProvider" --tag="migrations"
```

After the migration has been published you can create the `hosts`  and `checks `tables by running the migrations:

```bash
php artisan migrate
```

You must publish the config-file with:
```bash
php artisan vendor:publish --provider="Spatie\ServerMonitor\ServerMonitorServiceProvider" --tag="config"
```

This is the contents of the published config file:

```php
return [

    /*
     * These are the checks that can be performed on your servers. You can add your own
     * checks. The only requirement is that they should extend the
     * `Spatie\ServerMonitor\Checks\CheckDefinitions\CheckDefinition` class.
     */
    'checks' => [
        'diskspace' => Spatie\ServerMonitor\CheckDefinitions\Diskspace::class,
        'elasticsearch' => Spatie\ServerMonitor\CheckDefinitions\Elasticsearch::class,
        'memcached' => Spatie\ServerMonitor\CheckDefinitions\Memcached::class,
        'mysql' => Spatie\ServerMonitor\CheckDefinitions\MySql::class,
    ],

    /*
     * The performance of the package can be increased by allowing a high number
     * of concurrent ssh connections. Set this to a lower value if you're
     * getting weird errors running the check.
     */
    'concurrent_ssh_connections' => 5,

    /*
     * This string will be appended to the ssh command generated by the package.
     */
    'ssh_command_suffix' => '',

    'notifications' => [

        'notifications' => [
            Spatie\ServerMonitor\Notifications\Notifications\CheckSucceeded::class => [],
            Spatie\ServerMonitor\Notifications\Notifications\CheckRestored::class => ['slack'],
            Spatie\ServerMonitor\Notifications\Notifications\CheckWarning::class => ['slack'],
            Spatie\ServerMonitor\Notifications\Notifications\CheckFailed::class => ['slack'],
        ],

        /*
         * To avoid burying you in notifications, we'll only send one every given amount
         * of minutes when a check keeps emitting warning or keeps failing.
         */
        'throttle_failing_notifications_for_minutes' => 60,

        'mail' => [
            'to' => 'your@email.com',
        ],

        'slack' => [
            'webhook_url' => env('SERVER_MONITOR_SLACK_WEBHOOK_URL'),
        ],

        /*
         * Here you can specify the notifiable to which the notifications should be sent. The default
         * notifiable will use the variables specified in this config file.
         */
        'notifiable' => \Spatie\ServerMonitor\Notifications\Notifiable::class,

        /*
         * The date format used in notifications.
         */
        'date_format' => 'd/m/Y',
    ],

    /*
     * To add or modify behaviour to the `Check` model you can specify your
     * own model here. The only requirement is that they should
     * extend the `Check` model provided by this package.
     */
    'check_model' => Spatie\ServerMonitor\Models\Check::class,

    /*
     * Right before running a check its process will be given to this class. Here you
     * can perform some last minute manipulations on it before it will
     * actually be run.
     *
     * This class should implement Spatie\ServerMonitor\Manipulators\Manipulator
     */
    'process_manipulator' => Spatie\ServerMonitor\Manipulators\Passthrough::class,
    
    /*
     * Thresholds for disk space's alert.
     */
    'diskspace_percentage_threshold' => [
        'warning' => 80,
        'fail' => 90,
    ],
];
```

## Need a UI?

The package doesn't come with any screens out of the box. You may use the [Nova package by @paras-malhotra](https://github.com/insenseanalytics/nova-server-monitor) for monitoring servers on [Laravel Nova](https://nova.laravel.com).

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information what has changed recently.

## Testing

To run the tests you'll have to start the included node based dummy ssh server first in a separate terminal window.

```bash
cd tests/server
npm install
./start_server.sh
```

With the server running, you can start testing.

```bash
vendor/bin/phpunit
```

## Contributing

Please see [CONTRIBUTING](https://github.com/spatie/.github/blob/main/CONTRIBUTING.md) for details.

## Security

If you've found a bug regarding security please mail [security@spatie.be](mailto:security@spatie.be) instead of using the issue tracker.

## Credits

- [Freek Van der Herten](https://github.com/freekmurze)
- [All Contributors](../../contributors)

The code to execute commands on a remote server was copied from [Envoy](https://github.com/laravel/envoy).

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
