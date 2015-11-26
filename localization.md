# 本地化

- [介绍](#introduction)
- [基本用法](#basic-usage)
    - [复数形式](#pluralization)
- [覆写扩展包的语言文件](#overriding-vendor-language-files)

<a name="introduction"></a>
## 介绍

Laravel的本地化功能提供了一种便利的获取多种语言字串的方法，让你轻松地在应用中支持多语言。

语言字串保存在目录 `resources/lang` 下的文件中。应用支持的每种语言都对应该目录下的一个子目录：

    /resources
        /lang
            /en
                messages.php
            /es
                messages.php

所有语言文件都简单返回一个带有键值的数组。例如：

    <?php

    return [
        'welcome' => 'Welcome to our application'
    ];

#### 配置语言环境

你的应用的默认语言保存在 `config/app.php` 配置文件中。当然，你可以根据需要修改该值。你也可以使用  `App` facade 的 `setLocale` 方法在运行时修改当前语言。

    Route::get('welcome/{locale}', function ($locale) {
        App::setLocale($locale);

        //
    });

你也可以配置一个 "备用语言", 它会在当前语言不含有给定的语句时被使用。 和默认语言一样，备用语言也在 `config/app.php` 中设置:

    'fallback_locale' => 'en',

<a name="basic-usage"></a>
## 基本用法

你可以通过 `trans` 方法从语言文件中获取各行字串。`trans` 方法接受文件名和键值作为第一个参数。比如获取  `resources/lang/messages.php` 文件中键值为 `welcome` 的字串：

    echo trans('messages.welcome');

如果你在使用 [Blade templating engine](/docs/{{version}}/blade) 模版引擎，你可以使用 `{{ }}` 语法输出行字串：

    {{ trans('messages.welcome') }}

如果该指定的行字串不存在，`trans` 函数只简单返回该行字串的键值。所以在上面的例子中，`trans` 将返回 `messages.welcome`如果不存在该行字串。

#### 在语言文件中替换参数

你还可以给语言文件中的各行定义占位符。所有的占位符都以 `:` 为前缀。比如你可以定义一个带有占位符 name 的欢迎信息：

    'welcome' => 'Welcome, :name',

要替换占位符，只需要将一个数组作为第二个参数传入 `trans` 函数：

    echo trans('messages.welcome', ['name' => 'Dayle']);

<a name="pluralization"></a>
### 复数形式

复数是个复杂的问题，不同语言对复数有各自不同的规则。通过使用 “|” 标记，你可以区分一个字符串的单数和复数形式：

    'apples' => 'There is one apple|There are many apples',

接下来你可以使用 `trans_choice` 方法来根据给定的”个数“取得字串。在下面的例子中，因为个数大于1，所以返回该行的复数形式的字串：

    echo trans_choice('messages.apples', 10);

因为 Laravel 的翻译器由 Symfony 翻译组件提供，你可以创建更加复杂的复数规则：

    'apples' => '{0} There are none|[1,19] There are some|[20,Inf] There are many',

<a name="overriding-vendor-language-files"></a>
## 覆写扩展包的语言文件

有些扩展包自带语言文件。你可以将语言文件放置在 `resources/lang/vendor/{package}/{locale}` 目录来覆写它们，而不是去修改扩展包的核心文件。

所以，如果你需要覆写 `skyrim/hearthfire` 扩展包的 `messages.php` 中的英文语句，你可以将一个语言文件放置在 `resources/lang/vendor/hearthfire/en/messages.php`。在该文件中只定义需要覆写的语句，没有被覆写的语句将依然从扩展包的语言文件中加载。
