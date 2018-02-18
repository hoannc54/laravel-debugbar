## Laravel Debugbar
[![Packagist License](https://poser.pugx.org/barryvdh/laravel-debugbar/license.png)](http://choosealicense.com/licenses/mit/)
[![Latest Stable Version](https://poser.pugx.org/barryvdh/laravel-debugbar/version.png)](https://packagist.org/packages/barryvdh/laravel-debugbar)
[![Total Downloads](https://poser.pugx.org/barryvdh/laravel-debugbar/d/total.png)](https://packagist.org/packages/barryvdh/laravel-debugbar)

### Chú ý cho phiên bản 3: Debugbar bây giờ đã được kích hoạt yêu cầu của gói, nhưng vẫn cần APP_DEBUG=true theo mặc định!
### Cho Laravel < 5.5, Vui lòng dùng [2.4 branch](https://github.com/barryvdh/laravel-debugbar/tree/2.4)!

Đây là gói để tích hợp [PHP Debug Bar](http://phpdebugbar.com/) với Laravel 5. Nó bao gồm một ServiceProvider để đăng ký debugbar và đính kèm nó cho đầu ra. Bạn có thể đẩy tài nguyên và cấu hình nó thông qua Laravel. Nó khởi động vài Collectors để làm việc với Laravel và thực hiện một cặp tuỳ biến Collectors, đặc biệt cho Laravel. Nó đã được cấu hình để hiển thị các chuyển hướng và (jQuery) Ajax Requests. (Hiển thị trong một danh sách thả xuống).Đọc [Tài liệu](http://phpdebugbar.com/docs/) để biết thêm nhiều tuỳ chọn cấu hình.

![Screenshot](https://cloud.githubusercontent.com/assets/973269/4270452/740c8c8c-3ccb-11e4-8d9a-5a9e64f19351.png)

Chú ý: Dùng DebugBar chỉ trong môi trường phát triển. Nó có thểm làm chậm ứng dụng tải xuống (bởi vì nó phải thu thập dữ liệu). Bởi vậy khi trải nghiệm chậm hơn, thử tắt một vài collectors.

Gói này bao gồm một số collectors tuỳ biến:
 - QueryCollector: Hiển thị tất cả truy vấn, bao gồm các ràng buộc và thời gian.
 - RouteCollector: Hiện thị thông tin về Route hiện tại.
 - ViewCollector: Hiển thị views được tải hiện tại. (Tuỳ chọn: hiển thị dữ liệu được chia sẻ)
 - EventsCollector: Hiển thị tất cả sự kiện.
 - LaravelCollector: Hiển thị phiên bản và môi trường Laravel (Mặc định là tắt)
 - SymfonyRequestCollector: thay thế RequestCollector với thông tin về request/response
 - LogsCollector: Hiển thị log mới nhập từ thư mục lưu trữ logs. (Mặc định là tắt)
 - FilesCollector: Hiển thị các file được include hoặc require trong PHP(mặc định là tắt)
 - ConfigCollector: Hiển thị các giá trị trong file cấu hình. (Mặc định là tắt)
 - CacheCollector: Hiển thị tất cả sự kiện cache. (mặc định là tắt)

Khởi động các collector sau cho Laravel:
 - LogCollector: Hiện thị tất cả bản tin log.
 - SwiftMailCollector and SwiftLogCollector for Mail

Và các collector mặc định:
 - PhpInfoCollector
 - MessagesCollector
 - TimeDataCollector (With Booting and Application timing)
 - MemoryCollector
 - ExceptionsCollector

Nó cũng cung cấp một Facade interface để dễ dàng ghi lại Messages, Exceptions and Time

## Cài đặt

Yêu cầu gói thông qua composer. Nó được chỉ được đề nghị gói cho môi trường phát triển.

```shell
composer require barryvdh/laravel-debugbar --dev
```

Laravel 5.5 sử dụng Package Auto-Discovery, nên không yêu cầu bạn thêm bằng tay ServiceProvider.

Debugbar sẽ được bật khi `APP_DEBUG` là `true`.

> If you use a catch-all/fallback route, make sure you load the Debugbar ServiceProvider before your own App ServiceProviders.

### Laravel 5.5+:

Nếu bạn không dùng auto-discovery, thì thêm ServiceProvider để cung cấp cho mảng trong config/app.php

```php
Barryvdh\Debugbar\ServiceProvider::class,
```

Nếu bạn muốn dùng facade để ghi messages, thêm nó vào facade của bạn trong app.php:

```php
'Debugbar' => Barryvdh\Debugbar\Facade::class,
```

The profiler is enabled by default, if you have APP_DEBUG=true. You can override that in the config (`debugbar.enabled`) or by setting `DEBUGBAR_ENABLED` in your `.env`. See more options in `config/debugbar.php`
You can also set in your config if you want to include/exclude the vendor files also (FontAwesome, Highlight.js and jQuery). If you already use them in your site, set it to false.
You can also only display the js or css vendors, by setting it to 'js' or 'css'. (Highlight.js requires both css + js, so set to `true` for syntax highlighting)

Copy the package config to your local config with the publish command:

```shell
php artisan vendor:publish --provider="Barryvdh\Debugbar\ServiceProvider"
```

### Lumen:

For Lumen, register a different Provider in `bootstrap/app.php`:

```php
if (env('APP_DEBUG')) {
 $app->register(Barryvdh\Debugbar\LumenServiceProvider::class);
}
```

To change the configuration, copy the file to your config folder and enable it:

```php
$app->configure('debugbar');
```

## Usage

You can now add messages using the Facade (when added), using the PSR-3 levels (debug, info, notice, warning, error, critical, alert, emergency):

```php
Debugbar::info($object);
Debugbar::error('Error!');
Debugbar::warning('Watch out…');
Debugbar::addMessage('Another message', 'mylabel');
```

And start/stop timing:

```php
Debugbar::startMeasure('render','Time for rendering');
Debugbar::stopMeasure('render');
Debugbar::addMeasure('now', LARAVEL_START, microtime(true));
Debugbar::measure('My long operation', function() {
    // Do something…
});
```

Or log exceptions:

```php
try {
    throw new Exception('foobar');
} catch (Exception $e) {
    Debugbar::addThrowable($e);
}
```

There are also helper functions available for the most common calls:

```php
// All arguments will be dumped as a debug message
debug($var1, $someString, $intValue, $object);

start_measure('render','Time for rendering');
stop_measure('render');
add_measure('now', LARAVEL_START, microtime(true));
measure('My long operation', function() {
    // Do something…
});
```

If you want you can add your own DataCollectors, through the Container or the Facade:

```php
Debugbar::addCollector(new DebugBar\DataCollector\MessagesCollector('my_messages'));
//Or via the App container:
$debugbar = App::make('debugbar');
$debugbar->addCollector(new DebugBar\DataCollector\MessagesCollector('my_messages'));
```

By default, the Debugbar is injected just before `</body>`. If you want to inject the Debugbar yourself,
set the config option 'inject' to false and use the renderer yourself and follow http://phpdebugbar.com/docs/rendering.html

```php
$renderer = Debugbar::getJavascriptRenderer();
```

Note: Not using the auto-inject, will disable the Request information, because that is added After the response.
You can add the default_request datacollector in the config as alternative.

## Enabling/Disabling on run time
You can enable or disable the debugbar during run time.

```php
\Debugbar::enable();
\Debugbar::disable();
```

NB. Once enabled, the collectors are added (and could produce extra overhead), so if you want to use the debugbar in production, disable in the config and only enable when needed.


## Twig Integration

Laravel Debugbar comes with two Twig Extensions. These are tested with [rcrowe/TwigBridge](https://github.com/rcrowe/TwigBridge) 0.6.x

Add the following extensions to your TwigBridge config/extensions.php (or register the extensions manually)

```php
'Barryvdh\Debugbar\Twig\Extension\Debug',
'Barryvdh\Debugbar\Twig\Extension\Dump',
'Barryvdh\Debugbar\Twig\Extension\Stopwatch',
```

The Dump extension will replace the [dump function](http://twig.sensiolabs.org/doc/functions/dump.html) to output variables using the DataFormatter. The Debug extension adds a `debug()` function which passes variables to the Message Collector,
instead of showing it directly in the template. It dumps the arguments, or when empty; all context variables.

```twig
{{ debug() }}
{{ debug(user, categories) }}
```

The Stopwatch extension adds a [stopwatch tag](http://symfony.com/blog/new-in-symfony-2-4-a-stopwatch-tag-for-twig)  similar to the one in Symfony/Silex Twigbridge.

```twig
{% stopwatch "foo" %}
    …some things that gets timed
{% endstopwatch %}
```
