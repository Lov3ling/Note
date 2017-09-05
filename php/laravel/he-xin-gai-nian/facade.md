\*\*Facade\*\*

View,Scheam,Route这些类的方法可以随处使用,实际上这些类都是实现了facades接口

Laravel facades 实际上是服务容器中那些底层类的「静态代理」，相比于传统的静态方法， facades 在提供了简洁且丰富的语法同时，还带来了更好的可测试性和扩展性。

在config/app.php中的aliases数组可以找到这些可以直接使用的类

\*\*自定义Facade\*\*

在app目录下创建Facades文件夹存放自己的Facades,

Facades的getFacadeAccessor方法返回一个字符串,

调用在服务提供者中注册的服务

app/facades/TestFacade.php

\`\`\`

&lt;?php

namespace App\Facades;

use Illuminate\Support\Facades\Facade;

class TestFacades extends Facade{

```
public static function getFacadeAccessor\(\)

{

   return "test";       /\*调用服务提供者绑定的字符串\*/

}
```

}

\`\`\`

然后创建app/Services/文件夹存放自己编写的服务

app/Services/Test.php

\`\`\`

&lt;?php

namespace App\Services;

interface Test{

```
public function run\($string\);
```

}

\`\`\`

app/Services/TestClass.php继承上面定义的接口

\`\`\`

&lt;?php

namespace App\Services;

use App\Services\Test;

class TestClass implements Test{

```
public function run\($string\){

    echo $string;

}
```

}

\`\`\`

编写一个服务提供者

php artisan make:provider TestServiceProvider

在服务提供者的Register方法中注册一个test字符串 返回刚才定义的TestClass类的单例

\`\`\`

&lt;?php

namespace App\Providers;

use App\Services\TestClass;

use App\User;

use Illuminate\Support\ServiceProvider;

class TestServiceProvider extends ServiceProvider

{

```
public function boot\(\)

{

    //



}



public function register\(\)

{

    $this-&gt;app-&gt;singleton\('test',function \($app\){

       return new TestClass\(\);  /\* 当调用test时返回一个TestClass类\*/

    }\);

}
```

}

\`\`\`

在config/app.php的provides数组中注册服务提供者以及在aliases数组中注册Facade

\`\`\`

&lt;?php

'provides'=\[

```
App\Providers\TestServiceProvider::class,

\],
```

'aliasess'=&gt;\[

```
'MyTest'=&gt;App\Facades\TestFacades::class,
```

\]

\`\`\`

最后创建路由测试

\`\`\`

&lt;?php

Route::get\('test',function\(\){

```
MyTest::run\("this is Test"\);
```

}\);

\`\`\`

Facade运行流程

MyTest找到TestFacades,

TestFacades的getFacadeAccessor返回一个字符串

该字符串实际上就是服务提供者中字符串绑定的类

所以MyTest::run\(\)实际上就是\(new TestClass\(\)-&gt;run\)\(\);

相当于MyTest代理了TestClass\*\*Facade\*\*

View,Scheam,Route这些类的方法可以随处使用,实际上这些类都是实现了facades接口

Laravel facades 实际上是服务容器中那些底层类的「静态代理」，相比于传统的静态方法， facades 在提供了简洁且丰富的语法同时，还带来了更好的可测试性和扩展性。

在config/app.php中的aliases数组可以找到这些可以直接使用的类

\*\*自定义Facade\*\*

在app目录下创建Facades文件夹存放自己的Facades,

Facades的getFacadeAccessor方法返回一个字符串,

调用在服务提供者中注册的服务

app/facades/TestFacade.php

    `<?php`

    `namespace App\Facades;`

    `use Illuminate\Support\Facades\Facade;`

    `class TestFacades extends Facade{`

    ```
    public static function getFacadeAccessor\(\)

    {

       return "test";       /\*调用服务提供者绑定的字符串\*/

    }
    ```

    `}`

然后创建app/Services/文件夹存放自己编写的服务

app/Services/Test.php

    `<?php`

    `namespace App\Services;`

    `interface Test{`

    ```
    public function run\($string\);
    ```

    `}`

app/Services/TestClass.php继承上面定义的接口

    `<?php`

    `namespace App\Services;`

    `use App\Services\Test;`

    `class TestClass implements Test{`

    ```
    public function run\($string\){

        echo $string;

    }
    ```

    `}`

编写一个服务提供者

php artisan make:provider TestServiceProvider

在服务提供者的Register方法中注册一个test字符串 返回刚才定义的TestClass类的单例

    `<?php`

    `namespace App\Providers;`

    `use App\Services\TestClass;`

    `use App\User;`

    `use Illuminate\Support\ServiceProvider;`

    `class TestServiceProvider extends ServiceProvider`

    `{`

    ```
    public function boot\(\)

    {

        //



    }



    public function register\(\)

    {

        $this-&gt;app-&gt;singleton\('test',function \($app\){

           return new TestClass\(\);  /\* 当调用test时返回一个TestClass类\*/

        }\);

    }
    ```

    `}`

在config/app.php的provides数组中注册服务提供者以及在aliases数组中注册Facade

\`\`\`

`<?php`

`'provides'=[`

```
App\Providers\TestServiceProvider::class,

\],
```

`'aliasess'=>[`

```
'MyTest'=&gt;App\Facades\TestFacades::class,
```

`]`

```````````

最后创建路由测试

    `<?php`

    `Route::get('test',function(){`

    ```
    MyTest::run\("this is Test"\);
    ```

    `});`

Facade运行流程

MyTest找到TestFacades,

TestFacades的getFacadeAccessor返回一个字符串

该字符串实际上就是服务提供者中字符串绑定的类

所以MyTest::run\(\)实际上就是\(new TestClass\(\)-&gt;run\)\(\);

相当于MyTest代理了TestClass

