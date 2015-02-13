# HTTP Requests

- [获取一个request实例](#obtaining-a-request-instance)
- [接收 Input](#retrieving-input)
- [旧输入数据](#old-input)
- [Cookies](#cookies)
- [文件](#files)
- [其他请求信息](#other-request-information)

<a name="obtaining-a-request-instance"></a>
## 获取一个request实例

### 通过 Facade

`request Facade`会授权你访问当前reqeust的容器的封装.
下面是一个例子

    $name = Request::input('name');

要记住一点,如果你是在一个命名空间内,你需要加上 `use Request;`语句在你的类文件顶部.

### 通过依赖注入 DI

要通过DI获取当前的request对象,你需要在你的controller的构造方法或方法上构造类型提示注释,然后request instance 会自动通过service container注入进来.下面是一个例子
	
    <?php namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use Illuminate\Routing\Controller;

	class UserController extends Controller {

		/**
		 * Store a new user.
		 *
		 * @param  Request  $request
		 * @return Response
		 */
		public function store(Request $request)
		{
			$name = $request->input('name');

			//
		}

	}

如果你的controller 方法也需要从route 参数中获取input,仅仅需要在你的其他依赖中列出你的router参数(注意看注释的区别)

	<?php namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use Illuminate\Routing\Controller;

	class UserController extends Controller {

		/**
		 * Store a new user.
		 *
		 * @param  Request  $request
		 * @param  int  $id
		 * @return Response
		 */
		public function update(Request $request, $id)
		{
			//
		}

	}

<a name="retrieving-input"></a>
## 接收 Input

#### 接收一个Input的值

使用一个很简单的方法,
通过 `Illuminate\Http\Request`的简单方法就可以获取所有的用户输入,
你不需要再为HTTP的request操心了

	$name = Request::input('name');

#### 为空时接收一个默认值

	$name = Request::input('name', 'Sally');

####判断一个请求参数是否存在

	if (Request::has('name'))
	{
		//
	}

####获得所有request参数

	$input = Request::all();

####获取其中的一部分

	$input = Request::only('username', 'password');

	$input = Request::except('credit_card');

####如果是通过form表单提交的数组形式的输入数据,可以使用点号取得索引

	$input = Request::input('products.0.name');

<a name="old-input"></a>
## 旧数据输入

Laravel允许你在下一次请求时获得这一次的请求数据.举个例子,你可能需要在表单验证失败时重填表单数据

#### 将Input数据缓存到Session

`flash` 方法 将把本次请求的参数缓存到 [session](/docs/5.0/session) 所以可以再下一次请求中获取:

	Request::flash();

#### 仅将部分请求参数缓存到 Session

	Request::flashOnly('username', 'email');

	Request::flashExcept('password');

#### 缓存 & 跳转


因为你经常会跳转到前一页的时候闪存输入信息,你可以简单的把闪存操作链接到跳转操作上.

	return redirect('form')->withInput();

	return redirect('form')->withInput(Request::except('password'));

#### 接收旧数据

要接收上一次请求中闪存的请求参数,使用`Request`实例中的`old`方法

	$username = Request::old('username');

如果你要在Blade模板中打印旧请求,在`helper`中有非常方便的方法
	{{ old('username') }}

<a name="cookies"></a>
## Cookies

所有Laravel创建的 cookie 会加密并且加上认证签名，这意味着如果cookie被客户端擅自改动，会导致 cookie 失效。

#### 取得 Cookie 值

	$value = Request::cookie('name');

#### 在Response中增加新的Cookie值

`cookie`助手服务作为一个简单的工厂是一个`Symfony\Component\HttpFoundation\Cookie`实例. 使用 `withCookie`方法,将会在相应头中附上:

	$response = new Illuminate\Http\Response('Hello World');

	$response->withCookie(cookie('name', 'value', $minutes));

#### 建立永久有效的 Cookie*

_这里的永久,其实是5年时间._

	$response->withCookie(cookie()->forever('name', 'value'));

<a name="files"></a>
## 上传文件

#### 取得上传文件

	$file = Request::file('photo');

#### 确认文件是否上传成功

	if (Request::hasFile('photo'))
	{
		//
	}

file 方法回传的对象是 Symfony\Component\HttpFoundation\File\UploadedFile 的实例， UploadedFile 继承了 PHP 的 SplFileInfo 类并且提供了很多方法和文件互动。
#### 确认上传的文件是否有效

	if (Request::file('photo')->isValid())
	{
		//
	}

#### 移动上传文件

	Request::file('photo')->move($destinationPath);

	Request::file('photo')->move($destinationPath, $fileName);

### 其他的 文件 方法

在`UploadedFile`实例中有多重文件方法. 查看[API documentation for the class](http://api.symfony.com/2.5/Symfony/Component/HttpFoundation/File/UploadedFile.html)获取更多信息.

<a name="other-request-information"></a>
## 其他请求信息

`Request` 类提供很多检查HTTP请求的方法, 并继承于 `Symfony\Component\HttpFoundation\Request`类. 这里有一些典型的例子.

#### 取得Request URI

	$uri = Request::path();

#### 取得Request Method

	$method = Request::method();

	if (Request::isMethod('post'))
	{
		//
	}

#### 确认请求路径是否符合特定格式

	if (Request::is('admin/*'))
	{
		//
	}

#### 取得当前请求 的URL

	$url = Request::url();