# Pagination

- [介绍](#introduction)
- [使用](#basic-usage)
	- [分页查询构造器数据](#paginating-query-builder-results)
	- [分页 Eloquent 数据](#paginating-eloquent-results)
	- [创建自定义分页器](#manually-creating-a-paginator)
- [视图中显示数据](#displaying-results-in-a-view)
- [转换至 JSON](#converting-results-to-json)

<a name="introduction"></a>
## 介绍

在其他框架中，实现分页是令人感到苦恼的事，但 Laravel 令它变得轻松。Laravel 可以产生基于当前页面的智能「范围」链接，所产生的 HTML 兼容 [Bootstrap CSS 框架](http://getbootstrap.com/)。

<a name="basic-usage"></a>
## 使用

<a name="paginating-query-builder-results"></a>
### 分页查询构造器数据

有若干方法实现分页。最简单的方法是在 [查询构建器](/docs/{{version}}/queries) 或 [Eloquent 查询](/docs/{{version}}/eloquent) 对象上调用 `paginate` 方法。Laravel 框架提供的 `paginate` 方法会自动基于当前用户浏览的页码处理数据项的偏移和数目。HTTP请求中的查询字符串 `?page` 默认作为判断当前页码的依据。当然，这个查询字符串是 Laravel 框架自动插入分页链接的。

首先，我们一起了解下查询实例的 `paginate` 方法。例子中，每页显示的记录数是唯一传给 `paginate` 方法的参数。下面这个例子，让我们每页显示 15 条记录：

	<?php

	namespace App\Http\Controllers;

	use DB;
	use App\Http\Controllers\Controller;

	class UserController extends Controller
	{
		/**
		 * Show all of the users for the application.
		 *
		 * @return Response
		 */
		public function index()
		{
			$users = DB::table('users')->paginate(15);

			return view('user.index', ['users' => $users]);
		}
	}

> **注意:** 现在，使用 `groupBy` 语句的分页还不能被 Laravel 有效率的执行。如果你需要对结果集使用 `groupBy` ，建议创建一个自定义分页器。

#### "简单分页"

如果只在页面上显示“下一页”和“上一页”，可以使用 `simplePaginate` 方法执行一个更有效率的查询。这在处理一个巨大的数据集，且不需要显示每页的页码的情况下非常有效：

	$users = DB::table('users')->simplePaginate(15);

<a name="paginating-eloquent-results"></a>
### 分页 Eloquent 数据

你也可以使用分页 [Eloquent](/docs/{{version}}/eloquent) 查询。例子中，我们将每页显示15条 `User` 模型的数据。正如你所看到的，语法几乎与分页查询构造器一样：

	$users = App\User::paginate(15);

当然，你也可以在设置其他查询条件后调用 `paginate` 方法：

	$users = User::where('votes', '>', 100)->paginate(15);

你还可以调用 `simplePaginate` 方法：

	$users = User::where('votes', '>', 100)->simplePaginate(15);

<a name="manually-creating-a-paginator"></a>
### 手动创建分页器

有的时候你可能会想要从数组中对象手动建立分页实体， 你可以根据需要通过 `Illuminate\Pagination\Paginator` 或 `Illuminate\Pagination\LengthAwarePaginator` 实体来建立。

`Paginator` 类不需要知道数据集的总记录数，因此，类没有获取最后一页索引的方法。`LengthAwarePaginator` 的传入参数几乎与 `Paginator` 一样；但是，它需要数据集的总记录数.

换句话说， `Paginator` 与 `simplePaginate` 方法对应， `LengthAwarePaginator` 与 `paginate` 方法对应.

自定义分页器时，你需要手动对传给分页器的数据集的数组分组。如果你不知道如何分组，查看 [array_slice](http://php.net/manual/en/function.array-slice.php) PHP 函数.

<a name="displaying-results-in-a-view"></a>
## 在视图中显示数据

当你在查询构造器或 Eloquent 查询上调用 `paginate` 或 `simplePaginate` 方法，你将获得一个分页器实例。 当调用 `paginate` 方法，将获得 `Illuminate\Pagination\LengthAwarePaginator` 实例。当调用 `simplePaginate` 方法， 将获得 `Illuminate\Pagination\Paginator` 实例。这些实例对象提供了若干描述数据集的方法。 除了这些辅助方法，分页器实例还是迭代器，可以被当做数组一样迭代。

因此，一旦你获得数据集，你就可以使用 [Blade](/docs/{{version}}/blade) 显示数据和渲染视图：

	<div class="container">
		@foreach ($users as $user)
			{{ $user->name }}
		@endforeach
	</div>

	{!! $users->render() !!}

`render` 方法将在视图中渲染链接。每个链接将包含合适的 `?page` 查询字符串变量。记住，`render` 方法渲染的HTML兼容 [Bootstrap CSS 框架](https://getbootstrap.com).

> **注意：** 当在 Blade 模板中调用 `render` 方法，一定要使用 `{!! !!}` 语法，从而避免转义。

#### 自定义分页器 URI

`setPath` 方法允许你自定义分页器生成的 URI。例如，如果你想让分页器生成这样的链接 `http://example.com/custom/url?page=N`，你需要给 `setPath` 传参数 `custom/url`：

	Route::get('users', function () {
		$users = App\User::paginate(15);

		$users->setPath('custom/url');

		//
	});

#### 分页链接追加查询字符串

你可以使用 `appends` 方法给分页链接追加查询字符串。例如，给每个分页链接追加 `&sort=votes` ，你需要下面这样调用 `appends`：

	{!! $users->appends(['sort' => 'votes'])->render() !!}

如果你想在分页链接上追加 "hash fragment" ，你可以使用 `fragment` 方法。例如，添加 `#foo` 到每个分页链接后面，需要下面这样调用 `fragment` 方法：

	{!! $users->fragment('foo')->render() !!}

#### 额外的辅助方法

你还可以调用下面的方法来追加分页信息：

- `$results->count()`
- `$results->currentPage()`
- `$results->hasMorePages()`
- `$results->lastPage() (Not available when using simplePaginate)`
- `$results->nextPageUrl()`
- `$results->perPage()`
- `$results->total() (Not available when using simplePaginate)`
- `$results->url($page)`

<a name="converting-results-to-json"></a>
## 转换至JSON

Laravel 分页器的结果集类实现了约定 `Illuminate\Contracts\Support\JsonableInterface` ，并且公开了 `toJson` 方法，因此很容易将分页数据转换为JSON。

你可以直接在路由或控制器中返回分页器实例来转换为JSON：

	Route::get('users', function () {
		return App\User::paginate();
	});

分页器返回的 JSON 包含一些元信息，例如 `total`、 `current_page`、 `last_page` 等等。 实际的数据可以通过 JSON 数组中的 `data` 键获取。下面就是一个路由器中分页器返回的 JSON 的例子：

#### JSON分页例子

	{
	   "total": 50,
	   "per_page": 15,
	   "current_page": 1,
	   "last_page": 4,
	   "next_page_url": "http://laravel.app?page=2",
	   "prev_page_url": null,
	   "from": 1,
	   "to": 15,
	   "data":[
			{
				// Result Object
			},
			{
				// Result Object
			}
	   ]
	}
