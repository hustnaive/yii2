应用组件
======================

应用是 [服务定位器](concept-service-locator.md)。
它包含一系列被称之为 *应用组件* 的服务用来处理请求。
例如，`urlManager` 组件负责将网页请求路由到相应的控制器。
`db` 组件提供数据库相关服务等等。

在同一个应用中，每个应用组件都有一个独一无二的 ID 以区分其他应用组件。
你可以通过如下语句访问应用组件。

```php
\Yii::$app->componentID
```

例如，你可以使用 `\Yii::$app->db` 来获取到已注册到应用的 [[yii\db\Connection|DB 连接]]，
使用 `\Yii::$app->cache` 来获取到已注册到应用的 [[yii\caching\Cache|主缓存]]。

应用组件实例在第一次以上述语句访问的时候创建，
后续（同一个应用生命周期内）再访问时会返回相同组件实例。

应用组件可以是任意对象。
你可以参考 [应用配置](structure-applications.md#application-configurations) 一节来配置 [[yii\base\Application::components]] 属性以注册应用组件。
例如：

```php
[
    'components' => [
        // 使用类名注册 "cache" 组件
        'cache' => 'yii\caching\ApcCache',

        // 使用配置数组注册 "db" 组件
        'db' => [
            'class' => 'yii\db\Connection',
            'dsn' => 'mysql:host=localhost;dbname=demo',
            'username' => 'root',
            'password' => '',
        ],

        // 使用函数注册"search" 组件
        'search' => function () {
            return new app\components\SolrService;
        },
    ],
]
```

> 补充：请谨慎注册过多的应用组件，
> 应用组件就像全局变量，
> 使用太多可能加大测试和维护的难度。
> 一般情况下建议在需要时创建本地组件。


## 启动引导组件 <span id="bootstrapping-components"></span>

上面提到，一个应用组件只会在第一次访问时被实例化。
如果一个请求过程，某个组件压根没有被访问的话，那么它就不会被实例化。
然而，有时你想在每个请求处理过程都实例化某个组件，即便它不会被访问。
那么，你可以将该组件ID加入到应用主体的 [[yii\base\Application::bootstrap|bootstrap]] 属性中。

例如, 如下的应用配置保证了 `log` 组件一直被加载：

```php
[
    'bootstrap' => [
        'log',
    ],
    'components' => [
        'log' => [
            // "log" 组件的配置
        ],
    ],
]
```


## 核心应用组件 <span id="core-application-components"></span>

Yii 定义了一组固定ID和默认配置的 *核心* 组件。
例如 [[yii\web\Application::request|request]] 组件
用来收集用户请求并解析为一个 [路由](runtime-routing.md)；
[[yii\base\Application::db|db]] 代表一个可以执行数据库操作的数据库连接。
通过这些组件，Yii 应用才能处理用户请求。

下面是预定义的核心应用组件列表。
你可以和普通应用组件一样配置和自定义它们。
当你配置一个核心组件而不指定它的类名的话，就会使用 Yii 默认指定的类。

* [[yii\web\AssetManager|assetManager]]: 管理资源包和资源发布，
  详情请参考 [管理资源](output-assets.md) 一节。
* [[yii\db\Connection|db]]: 代表一个可以执行数据库操作的数据库连接，
  注意配置该组件时必须指定组件类名和其他相关组件属性，
  如[[yii\db\Connection::dsn]]。
  详情请参考 [数据访问对象](db-dao.md) 一节。
* [[yii\base\Application::errorHandler|errorHandler]]: 处理 PHP 错误和异常，
  详情请参考 [错误处理](tutorial-handling-errors.md) 一节。
* [[yii\i18n\Formatter|formatter]]: 格式化输出显示给终端用户的数据，
  例如数字可能要带分隔符，
  日期使用长格式。详情请参考 [格式化输出数据](output-formatting.md) 一节。
* [[yii\i18n\I18N|i18n]]: 支持信息翻译和格式化。
  详情请参考 [国际化](tutorial-i18n.md) 一节。
* [[yii\log\Dispatcher|log]]: 管理日志对象。
  详情请参考 [日志](tutorial-logging.md) 一节。
* [[yii\swiftmailer\Mailer|mail]]: 支持生成邮件结构并发送，
  详情请参考 [邮件](tutorial-mailing.md) 一节。
* [[yii\base\Application::response|response]]: 代表发送给用户的响应，
  详情请参考 [响应](runtime-responses.md) 一节。
* [[yii\base\Application::request|request]]: 代表从终端用户处接收到的请求，
  详情请参考 [请求](runtime-requests.md) 一节。
* [[yii\web\Session|session]]: 代表会话信息，
  仅在[[yii\web\Application|Web applications]] 网页应用中可用，
  详情请参考 [Sessions (会话) and Cookies](runtime-sessions-cookies.md) 一节。
* [[yii\web\UrlManager|urlManager]]: 支持URL地址解析和创建，
  详情请参考 [URL 解析和生成](runtime-url-handling.md) 一节。
* [[yii\web\User|user]]: 代表认证登录用户信息，
  仅在[[yii\web\Application|Web applications]] 网页应用中可用，
  详情请参考 [认证](security-authentication.md) 一节。
* [[yii\web\View|view]]: 支持渲染视图，
  详情请参考 [Views](structure-views.md) 一节。
