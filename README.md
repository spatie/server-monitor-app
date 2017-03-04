# A PHP application to monitor your servers' health

[![Latest Version on Packagist](https://img.shields.io/packagist/v/spatie/server-monitor-app.svg?style=flat-square)](https://packagist.org/packages/spatie/server-monitor-app)
[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE.md)
[![Total Downloads](https://img.shields.io/packagist/dt/spatie/server-monitor-app.svg?style=flat-square)](https://packagist.org/packages/spatie/server-monitor-app)

We all dream of servers that need no maintenance at all. But unfortunately in reality this is not the case. Disks can get full, processes can crash, the server can run out of memory...

This commandline app keeps an eye on the health of all your servers. There are a few [checks that come out of the box](https://docs.spatie.be/laravel-server-monitor/v1/monitoring-basics/built-in-checks).

When something goes wrong it can [notify you](https://docs.spatie.be/laravel-server-monitor/v1/monitoring-basics/notifications-and-events) via Slack or mail. This is what a Slack notification looks like:

![Screenshot slack](https://docs.spatie.be/images/server-monitor/check-failed.jpg)


Under the hood the server-monitor-app is a vanilla Laravel 5.4 application with the [laravel-server-monitor](https://docs.spatie.be/laravel-server-monitor) installed into it. If you know your way around Laravel, it's recommended that you use [laravel-server-monitor](https://docs.spatie.be/laravel-server-monitor) instead of this app.

## Requirements

See the requirements [in the docs of the underlying package](https://docs.spatie.be/laravel-server-monitor/v1/requirements).

## Installation

You can install the application by issuing this command:

```bash
composer create-project spatie/server-monitor-app <name of install directory>
```

To complete your installation these steps must be performed:
            
First you should add the following command to your cron table. It should run
every minute:

```bash
php <installation path>/artisan schedule:run
```

Secondly, specify a Slack webhook url in the `SERVER_MONITOR_SLACK_WEBHOOK_URL` key 
in the `.env` file found in the installation directory. 
You can [create a new webhook url](https://my.slack.com/services/new/incoming-webhook/) on the Slack website.

Optionally you can configure e-mail notifications by filling in the `MAIL_` keys in the `.env` file located in the
installation directory and adding `mail` to the `notifications` array in the `configuration.php` file.

## Configuration

The configuration file `configuration.php` is located inside the installation directory.

Reading it is a good way to quickly get a feel of what `server-monitor-app` can do. Here's the content of the config file:

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
     * Right before running a check it's process will be given to this class. Here you
     * can perform some last minute manipulations on it before it will
     * actually be run.
     *
     * This class should implement Spatie\ServerMonitor\Manipulators\Manipulator
     */
    'process_manipulator' => Spatie\ServerMonitor\Manipulators\Passthrough::class,
];
```

## Basic usage

To start monitoring a server:

```php
php artisan server-monitor:add-host
```

and answer the questions that are asked.`

To stop monitoring an url issue this command:

```php
php artisan server-monitor:delete-host <name>
```

To list all monitors you can perform:

```php
php artisan server-monitor:list-hosts
```

You can also list all active checks on all hosts:

```php
php artisan server-monitor:list-checks
```

Finally, if you've got a large amount of servers to monitor you can [import them from a JSON file](https://docs.spatie.be/laravel-server-monitor/v1/monitoring-basics/managing-hosts#syncing-from-a-file):

```php
php artisan server-monitor:sync-file <hosts.json>
```

## Advanced usage

Under the hood the `server-monitor-app` is a vanilla Laravel 5.4 application with our [laravel-server-monitor](https://github.com/spatie/laravel-server-monitor) installed into it. Please refer to [it's extensive documentation](https://docs.spatie.be/laravel-server-monitor) to know more how to configure and use this application. 

By default the application will use a `sqlite` database located at `<installation directory>/database.sqlite` to store all monitors.

## Documentation

You'll find the documentation of the underlying `laravel-server-monitor` package on [https://docs.spatie.be/laravel-server-monitor/v1](https://docs.spatie.be/laravel-server-monitor/v1). 

Find yourself stuck using this app or the underlying package? Found a bug? Do you have general questions or suggestions for improving the uptime monitor? Feel free to [create an issue on GitHub](https://github.com/spatie/laravel-server-monitor/issues), we'll try to address it as soon as possible.

## Postcardware

You're free to use this package (it's [MIT-licensed](LICENSE.md)), but if it makes it to your production environment we highly appreciate you sending us a postcard from your hometown, mentioning which of our package(s) you are using.

Our address is: Spatie, Samberstraat 69D, 2060 Antwerp, Belgium.

The best postcards will get published on the open source page on our website.

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Security

If you discover any security related issues, please email freek@spatie.be instead of using the issue tracker.

## Credits

- [Freek Van der Herten](https://github.com/freekmurze)
- [Alex Vanderbist](https://github.com/AlexVanderbist)
- [All Contributors](../../contributors)

## About Spatie
Spatie is a webdesign agency based in Antwerp, Belgium. You'll find an overview of all our open source projects [on our website](https://spatie.be/opensource).

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
