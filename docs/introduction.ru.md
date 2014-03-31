**Pherlin**
===========

Что такое Pherlin?
------------------

Pherlin это продукт с открытым исходным кодом, который является оберткой для быстрого старта разработки приложений на фрэймворке Phalcon. Части Pherlin слабо связаны между собой, что позволяет использовать их в качестве независимых компанентов для любого вашего приложения.


Что дает Pherlin?
-----------------

Pherlin дает возможность гибко управлять вашим приложением по средствам конфигурационных файлов: 
- возможность организовать разное поведение программы в зависимости от окружения;
- орагизовывать инициализацию контейнера зависимостей через конфигурационные файлы с поддержкой всех нативных возможностей;
- организовать единообразное хранение и подключения модулей, в том числе и с подгрузкой их через composer.


Установка
---------
Для того, чтобы использовать Pherlin, у вас должен быть установлен php версии не ниже 5.4, расширение Phalcon версии не ниже 1.2.4, а также должен быть установлен composer, для установки зависимостей.

Сам Pherlin можно скачать двумя способами:
- используя composer, выполнив команду ```composer create-project getsky/pherlin -s dev```;

- либо скачав архив проекта с сайта github.

После установки проекта в папку назначения, необходимо установить пакеты зависимостей, выполнив команду ```composer update ```.

Ознакомление с содержимым
-------------------------
Так же как и Phalcon, Pherlin не требует использовать определенную структуру каталогов, вы можете использовать любую удобную для вас структуру. Но для быстрого начала работы, вполне может сгодиться структура по умолчанию:
```
app/
.   config/
.   .   config.ini
.   .   config_dev.ini
.   .   config_prod.ini
.   .   services.ini
.   environment/
.   .   dev/
.   .   .   cache/
.   .   .   .   frontend/
.   .   .   .   volt/
.   .   prod/
.   .   .   cache/
.   .   .   .   frontend/
.   .   .   .   volt/
.   Providers/
.   Services/
public/
.   img/
.   css/
.   js/
.   .htaccess
.   index.php  
src/
.   FrontendModule/
.   .   Controllers/
.   .   .   indexController.php
.   .   Providers/
.   .   .   DispatcherProvider.php
.   .   .   MySqlProvider.php
.   .   .   ViewCacheProvider.php
.   .   .   ViewProvider.php
.   .   Resources/
.   .   .   config/
.   .   .   views/
.   .   .       index/
.   .   .   .   .   about.volt
.   .   .   .   .   error404.volt
.   .   .   .   .   index.volt
.   .   Module.php 
.gitignore
.htaccess
LICENSE
README.md
composer.json
```

Давайте разберем подробнее всю структуру каталогов. 

Каталог ```app/``` служит нам для хранения всех конфигурационных файлов (каталог ```app/config/```), которые касаются всего приложения (всех модулей). Каталог ```app/environment ``` используется для хранения кэша различными сервисами нашего приложения. Также, по умолчанию, в системе зарезервированы два каталога для пользовательских сервисов и провайдеров, - ```app/Providers/``` и ```app/Services/``` соответственно. 

Каталог ```public/``` содержит все ресурсы приложения (картинки, css-стили, js-скрипты и др.), а также php файл, на который будут перенаправляться все запросы - ```public/index.php```.

Каталог ```src/``` содержит файлы с логикой вашего приложения, а именно модули. Из коробки уже есть один предустановленный модуль - ```FrondendModule```. Вы можите использовать его, либо создать свой модуль, а этот удалить. Как создавать новые модули и удалить предустановленный будет написано позже. Сам каталог модуля содержит папку ```Controllers``` с контроллерами модуля, ```Providers``` с провайдерами сервисов модуля, ```Resources``` с ресурсами приложения - конфигурационные файлы и шаблонами.

Смена окружения
---------------
Окружением по умолчанию является ```dev```. Для смены окружения необходимо при создании объекта класса ```Bootstap``` вторым аргументом передавать название необходимого окружения:

```php
#/public/index.php
$app = new Bootstrap(new FactoryDefault(),'prod');

```

Создание и управление модулями
------------------------------

Для создания модуля можно использовать два разных подхода: создание модуля внутри Pherlin (для этого необходимо создать каталог с названием модуля ```ModuleNameModule``` в каталоге ```src/```), либо создать отдельный проект с модулем в своей IDE, и подключать этот модуль уже через composer. Второй метод более сложен, но при этом дает больше гибкости в дальнейшем использовании этого модуля, а также обновлении Pherlin. Ниже приведена пошаговая инструкция для создания своего модуля:

1. Создаём каталог с названием модуля ```ModuleNameModule``` в каталоге ```src/```. А также создаем каталог для контроллеров, провайдеров и ресурсов модуля. В итоге должна получиться такая структура: 

    ```
src/
.   ModuleNameModule/
.   .   Controllers/
.   .   Providers/
.   .   Resources/
```

2. Теперь необходимо создать главный класс модуля (```MOdule.php```) в корне каталога ```ModuleNameModule``` со следующим содержанием:
    ```php
    <?php
    namespace GetSky\ModuleNameModule;
    
    use GetSky\Phalcon\Bootstrap\Module as ModuleBootstrap;
    
    class Module extends ModuleBootstrap
    {   
        const DIR = __DIR__;
        //const CONFIG = '/Resources/options.ini';
        //const SERVICES = '/Resources/services.ini';
    }
    ```
Сам класс очень прост: в нем необходимо переопределить всего одну константу - ```DIR```, которая указывает на путь к каталогу модуля. Также вы можете задать свои пути для хранения конфигурации модуля (константа ```CONFIG```) и списка подключаемых сервисов (константа ```SERVICES```).

3. Теперь в папке ```Resources``` создадим каталог ```config``` и разместим в ней конфигурационный файл модуля ```config.ini``` следующего содержания:
    ```ini
    [volt]
    path = "../app/environment/%environment%/cache/volt/"
    extension = ".volt"
    stat = 1
    debug = 1
    
    [cache]
    path = "../app/environment/%environment%/cache/ModuleName/"
    host = "localhost"
    port = "11211"
    
    [mysql]
    host = "localhost"
    username = "root"
    password = ""
    name = "mydb"
    persistent = "true"   
    ```
    В этом файле мы прописали настройки для шаблонизатора, кэшера и данные для подключения к базе данных.
    
4. В той же папке ```Resources/config/``` создаем файл ```services.ini```, который будет содержать информацию о том, какие сервисы требуются для данного модуля:
    ```ini
    [dispatcher]
    provider = "GetSky\Phalcon\Provider\DispatcherProvider"
    arg.0.service = "config"
    arg.1.var = "GetSky\FrontendModule\Controllers"
    
    [view]
    provider = "GetSky\FrontendModule\Providers\ViewProvider"
    arg.0.service = "config"

    ```
    Более подробно о том, как создавать сервисы вы сможете прочитать в главе посвящённой phalcon-autoload-services.

5. Теперь нам нужно создать один провайдер для сервиса ```view```. который позволяет нам в модуле использовать шаблонизатор volt или php для формирования страниц. Для этого в папке ```Providers``` создаем файл ```ViewProvider.php```:
    ```php
    <?php
    namespace GetSky\ModuleNameModule\Providers;
    
    use GetSky\FrontendModule\Module;
    use GetSky\Phalcon\AutoloadServices\Provider;
    use Phalcon\Config;
    use Phalcon\Mvc\View;
    use Phalcon\Mvc\View\Engine\Volt;
    
    class ViewProvider implements Provider
    {
        /**
         * @var Config
         */
        private $options;

        public function __construct(Config $options)
        {
            $this->options = $options;
        }
    
        /**
         * @return callable
         */
        public function getServices()
        {
            /**
             * @var Config $config
             */
            $config = $this->options
                ->get('module-options')
                ->get(Module::NAME)
                ->get('volt');
    
            return function () use ($config) {
                $view = new View();
                $view->setViewsDir(Module::DIR . '/Resources/views/');
    
                $view->registerEngines(
                    [
                        '.volt' => function ($view) use ($config) {
                                $volt = new Volt($view);
    
                                $options = [
                                    'compiledPath' => $config->get('path'),
                                    'compiledSeparator' => '_',
                                ];
    
                                if ($config->debug != 1) {
                                    $options['compileAlways'] = true;
                                }
    
                                $volt->setOptions($options);
    
                                return $volt;
                            },
                        '.phtml' => 'Phalcon\Mvc\View\Engine\Php'
                    ]
                );
    
                return $view;
            };
        }
    } 
    ```
6. Теперь создаем каталог ```Resources/views/``` для хранения шаблонов используемых сервисом ```view```.

7. Создаем первый контроллер, к примеру ```IndexController.php``` в каталоге ```Controllers```:
    ```php
    <?php
    namespace GetSky\FrontendModule\Controllers;
    
    use Phalcon\Mvc\Controller;
    
    class IndexController extends Controller
    {
        public function indexAction()
        {
        }

        public function aboutAction()
        {
        }

        public function error404Action()
        {
        }
    }
    ```
    И создадим, для примера, один шаблон ```Resources/views/index/index.volt```:
    ```html
    <h1>Hi Phalcon!</h1>
    ```

8. После всех этих действий мы должны получить такую структуру файлов в нашем модуле:
    ```
src/
.   ModuleNameModule/
.   .   Controllers/
.   .   .   IndexController.php
.   .   Providers/
.   .   .   ViewProvider.php
.   .   Resources/
.   .   .   config/
.   .   .   .   config.ini
.   .   .   .   services.ini
.   .   .   views/
.   .   .   .    index/
.   .   .   .    .    index.volt
Module.php
```

9. Последний шаг заключается в том, что мы свяжем модуль с приложением. Для этого нам необходимо внести одну запись в файл конфигурации приложения. По умолчанию это файл ```app/config/config.ini```:
    ```ini
    [modules]
    frontend = "GetSky\FrontendModule"
    modulename = "GetSky\ModuleNameModule"
    ```
    И чтобы сделать наш модуль для роутинга модулем по умочанию:
    ```ini
    [app]
    #def_module = "frontend"
    def_module = "modulename"
    ```
    Без этой правки наш модуль доступен будет только по ссылке ```module/index/action```, если же мы сделаем его по умолчанию то по "index/action".
    

Загрузчик конфигурационных файлов
---------------------------------
В Pherlin используется универсальный загрузчик конфигурационных файлов, который позволяет:
- загружать конфигурации в ini, json и yaml форматах;
- склеивать их между собой и подгружать их друг в друга;
- подменять переменную ```{environment}``` на текущее окружение приложения.

Стоит заметить, что использование yaml без установленного расширения PECL - yaml (http://pecl.php.net/package/yaml) может заметно замедлить приложение из-за того, что в случаи отсутствия расширения используется пакет ```symfony/yaml```.

Загрузчик представляет собой отдельный сервис (```getsky/phalcon-config-loader```), который подгружается с помощью composer. Для того, чтобы начать им пользоваться необходимо создать экземпляр класса ```ConfigLoader``` с передачей в его конструктор переменной с текущим окружением.

```php
$configLoader = new ConfigLoader($this->environment);
$config = $configLoader->create('config.ini');
```

Если вы не хотите, чтобы происходил импорт ресурсов (подгрузка других конфигурационных файлов в эту конфигурацию), то вторым параметром необходимо передать булево значение ```false```:

```php
$config = $configLoader->create('config.ini', false);
```

В Pherlin загрузчик конфигурационных файлов помещается в DI под названием ```config-loader``` после выполнения метода ```run()``` класса ```Bootstrap``` в файле ```/public/index.php```, что позволяет с лёгкостью воспользоваться им в любой части вашего приложения где есть доступ к DI.

**Склейка конфигурационных файлов выглядит следующим образом:**

```ini
#config.ini
[test]
test = true
%res% = import.ini

```
```ini
#import.ini
import = "test"
```


При загрузке конфигурационного файла ```config.ini```:
```php
[                               
    'test' => [                 
        'test' => true,                              
        '%res%' => 'import.ini',
        'import' => true,       
        'env' => 'dev'          
    ]                           
]                               
```

**Подгрузка конфигурационных файлов выглядит следующим образом:**

```ini
#config.ini
[test]
test = true
exp = %res:import.ini

```
```ini
#import.ini
import = "test"
```

При загрузке конфигурационного файла ```config.ini```:
```php
[
    'test' => [
        'test' => true,
        'exp' => [
            'import' => true,
            'env' => 'dev'
        ]
    ]
]                                                        
```

**Подменять переменную {environment} на текущее окружение приложения:**

```ini
#config.ini
[test]
test = true
exp = "app/config/%environment%/cache/"
```

При загрузке конфигурационного файла ```config.ini```, если активно окружение dev:
```php
[                               
    'test' => [                 
        'test' => true,     
        'exp' = "app/config/dev/cache/"                                 
    ]                           
]
```

Вы также можете добавлять свои адаптеры конфигурационных файлов. Для этого необходимо вызвать метод ```add()``` с передачей расширения, которое будет обрабатывать данный адаптер и класс адаптера, который должен наследовать класс ```Phalcon\Config```.

```php
$config = $configLoader->add('xml', 'MyNamespace/XmlConfig');
```

Автозагрузчик сервисов
----------------------

Главной фишкой Pherlin является автозагрузка сервисов в DI. Для это используются конфигурационные файлы. Ниже приведен один из таких файлов в формате ini, но вы можете использовать также и yaml и json, либо любой другой, если для него вы задали адаптер в загрузчике конфигурационных файлов (сервис ```config-loader```):

```ini
[router]
provider = "GetSky\Phalcon\Provider\RouterProvider"
arg.0.service = "config"

[callsample]
object = "CallService"
call.0.method = "run"
call.0.arg.0.var = "24"

[session]
string = "GetSky\Phalcon\Provider\SessionProvider"
shared = true
```
Возможность инициализации сервисов через конфигурационные файлы в Pherlin обеспечивается по средствам компонента ```phalcon-autoload-services```. 

Зарегистрировать сервисы можно тремя способами:

1. По названию класса. Такой способ не позволяет передавать аргументы для конструктора класса или настраивать параметры.
    
    ```ini
    ...
    [response]
    string = "Phalcon\Http\Response"
    ...  
    ```
    
2. Регистрация экземпляра напрямую. При использовании этого способа в контейнер зависимостей помещается уже готовый объект.

    ```php
    ...
    [request]
    object = "Phalcon\Http\Response"
    ...
    ```

3. Через провайдера сервисов. Который должен реализовывать интерфейс ```GetSky\Phalcon\AutoloadServices```. По замыслу, провайдеры являются посредниками для регистрации анонимных функций в контейнере зависимостей, но при этом имеют возможность реализовать любой другой способ, который поддерживает Phalcon. 
    
    ```ini
    ...
    [route]
    provider = "RouteProvider"
    ...    
    ```
    
Для второго и третьего способа возможно указать какие аргументы будут переданы в конструктор и вызывать методы после его создания и до помещения в DI. Ниже приведен пример, как это можно реализовать на ini:

```ini
[ferst-service]
provider = "SomeNamespace\FerstClass"
arg.0.service = "config"
arg.1.var = "24"
arg.2.di = 1
arg.3.s-service = "shared-service"
arg.4.object.object = "SoeNamespace\SecondClass"
arg.4.object.arg.0.var = "42"
arg.4.object.call.0.method = "run"
```

В приведенном выше примере, мы регистрируем сервис ```SomeNamespace\FerstClass``` под именем ```fest-service``` и передаем 5 аргументов: сервис ```config```, переменную ```24```, контейнер зависимостей (объект реализующий интерфейс ```DiInterface```, который был передан в конструктор ```Botstrap``` в файле ```/public/index.php```), сервис ```shared-services``` вызванный через метод ```getShared``` и экземпляр класса ```SomeNamespace\SecondClass```, который сначала создается с передачей в конструктор аргумента ```42```, и вызовом метода ```run```, а уже затем передается в конструктор нашего сервиса пятым параметром.