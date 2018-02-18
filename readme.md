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

> Nếu bạn dùng một catch-all/fallback route, chắc chắn rằng bạn đã load Debugbar ServiceProvider trước App ServiceProviders của chính bạn.

### Laravel 5.5+:

Nếu bạn không dùng auto-discovery, thì thêm ServiceProvider để cung cấp cho mảng trong config/app.php

```php
Barryvdh\Debugbar\ServiceProvider::class,
```

Nếu bạn muốn dùng facade để ghi messages, thêm nó vào facade của bạn trong app.php:

```php
'Debugbar' => Barryvdh\Debugbar\Facade::class,
```

Nó được bật theo mặc định, nếu bạn có APP_DEBUG=true. Bạn có thể ghi đè nó trong cấu hình (`debugbar.enabled`) hoặc bởi cài đặt `DEBUGBAR_ENABLED` trong `.env` của bạn. Xem thêm tuỳ chọn trong `config/debugbar.php`
Bạn cũng có thể thiết lập cấu hình của bạn nếu bạn muốn bao gồm hoặc loại trừ file nguồn (FontAwesome, Highlight.js and jQuery). Nếu bạn đã sử dụng chúng trên site của bạn, thiết lập chúng là false.
Bạn cũng có thể chỉ hiển thị nguồn js hoặc css, bằng cách thiết lập nó để 'js' hoặc 'css'(Highlight.js yêu cầu cả css + js, nên thiết lập `true` cho cú pháp highlighting)

Sao chép cấu hình của gói vào cấu hình trên máy của bạn với câu lệnh đẩy:

```shell
php artisan vendor:publish --provider="Barryvdh\Debugbar\ServiceProvider"
```

### Lumen:

Cho Lumen, đăng ký một Provider khác trong `bootstrap/app.php`:

```php
if (env('APP_DEBUG')) {
 $app->register(Barryvdh\Debugbar\LumenServiceProvider::class);
}
```

Để thay đổi cấu hình, sao chép file vào thư mục cấu hình của bạn và bật nó:

```php
$app->configure('debugbar');
```

## Sử dụng

Bạn có thể thêm message sử dụng Facade (khi được thêm), sử dụng PSR-3 levels (debug, info, notice, warning, error, critical, alert, emergency):

```php
Debugbar::info($object);
Debugbar::error('Error!');
Debugbar::warning('Watch out…');
Debugbar::addMessage('Another message', 'mylabel');
```

Và start/stop thời gian:

```php
Debugbar::startMeasure('render','Time for rendering');
Debugbar::stopMeasure('render');
Debugbar::addMeasure('now', LARAVEL_START, microtime(true));
Debugbar::measure('My long operation', function() {
    // Do something…
});
```

Hoặc ghi ngoại lệ:

```php
try {
    throw new Exception('foobar');
} catch (Exception $e) {
    Debugbar::addThrowable($e);
}
```

Ngoài ra còn các hàm helper sẵn sàng cho hầu hết lời gọi chung:

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

Nếu bạn muốn bạn có thể thêm DataCollectors, thông qua Container hoặc Facade:

```php
Debugbar::addCollector(new DebugBar\DataCollector\MessagesCollector('my_messages'));
//Or via the App container:
$debugbar = App::make('debugbar');
$debugbar->addCollector(new DebugBar\DataCollector\MessagesCollector('my_messages'));
```

Theo mặc định, Debugbar được chèn ngay trước `</body>`. Nếu bạn muốn chèn Debugbar chính bạn, thiết lập tuỳ chọn cấu hình 'inject' thành false và dùng render chính bạn và theo http://phpdebugbar.com/docs/rendering.html

```php
$renderer = Debugbar::getJavascriptRenderer();
```

Chú ý: Không sử dụng auto-inject, sẽ tắt thông tin Request , vì nó được thêm sau response. Bạn có thể thêm default_request datacollector trong cấu hình để thay thế.

## Bật/tắt thời gian chạy
Bạn có thể bật hoặc tắt debugbả trong thời gian chạy.

```php
\Debugbar::enable();
\Debugbar::disable();
```

NB. Mỗi lần được bật, collector được thêm( và có thể mất thêm chi phí), nên nếu bạn muốn dùng debugbar trong môi trường sản phẩm, tắt cấu hình và chỉ bật khi cần thiết.


## Tích hợp nhánh

Laravel Debugbar đi kèm với hai Twig Extensions. Chúng được kiểm tra với [rcrowe/TwigBridge](https://github.com/rcrowe/TwigBridge) 0.6.x

Thêm extensions sau vào TwigBridge config/extensions.php (hoặc đăng ký extensions bằng tay)

```php
'Barryvdh\Debugbar\Twig\Extension\Debug',
'Barryvdh\Debugbar\Twig\Extension\Dump',
'Barryvdh\Debugbar\Twig\Extension\Stopwatch',
```

The Dump extension sẽ thay thế [dump function](http://twig.sensiolabs.org/doc/functions/dump.html) để biến đầu ra sử dụng DataFormatter. Debug extension thêm một hàm `debug()` đưa các biến vào Message Collector, thay vì hiển thị nó trực tiếp trong giao diện. Nó dump các đối hoặc khi rỗng, tất cả các biến ngữ cảnh.

```twig
{{ debug() }}
{{ debug(user, categories) }}
```

Stopwatch extension thêm một [stopwatch tag](http://symfony.com/blog/new-in-symfony-2-4-a-stopwatch-tag-for-twig)  gioongs như một Symfony/Silex Twigbridge.

```twig
{% stopwatch "foo" %}
    …some things that gets timed
{% endstopwatch %}
```
