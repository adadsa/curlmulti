[中文文档](README_CN.md "中文文档")

[![build status](https://travis-ci.org/sabberworm/PHP-CSS-Parser.png)](https://github.com/ares333/curlmulti)

About
-----

This is undoubtedly the best php curl library.It is widely used by many developers.The library is a wrapper of curl_multi_* functions with best performance,maximum flexibility,maximum ease of use and negligible performance consumption.All in all it's a very very powerful library.

Requirement
-----------
PHP 5.1.0 +

Contact Us
----------
Email: admin@phpdr.com<br>
QQ Group:215348766

Feature
-------
1. Extremely low cpu and memory usage.
1. Best program performance(tested spider 2000+ html pages per second and 1000MBps pic download speed).
1. Internal download support(use curl download callback,best performance).
1. Support global parallel and seperate parallel for defferent task type.
1. Support running info callback.All info you need is returned, include overall and every task infomation.
1. Support adding task in task callback.
1. Support user callback.You can do anything in that.
1. Support process callback backoff.Used to satisfy prerequists.
1. Support global error callback and task error callback.All error info is returned.
1. Support internal max try for tasks.
1. Support user variable flow arbitrarily.
1. Support global CURLOPT_* and task CURLOPT_*.
1. Powerfull cache.Global and task cache config supported.
1. All public property config can be changed on the fly!
1. You can develop amazing curl application based on the library.

Mechanism
---------

Without pthreads php is single-threaded language,so the library widely use callbacks.There are only two common functions CurlMulti_Core::add() and CurlMulti_Core::start().add() just add a task to internal taskpool.start() starts callback cycle with the concurrent number of CurlMulti_Core::$maxThread and is blocked until all added tasks(a typical task is a url) are finished.If you have huge number of tasks you will use CurlMulti_Core::$cbTask to specify a callback function to add() urls,this callback is called when the number of running concurrent is less than CurlMulti_Core::$maxThread and internal taskpool is empty.When a task finished the 'process callback' specified in add() is immediately called,and then fetch a task from internal taskpool,and then add the task to the running concurrent.When all added tasks finished the start() finished.

Files
-----
**CurlMulti/Core.php**<br>
Kernel class

**CurlMulti/Base.php**<br>
A wraper of CurlMulti_Core.Very usefull tools and convention is included.It's very easy to use.All spider shoud inherent this class.

**CurlMulti/Exception.php**<br>
CurlMulti_Exception

**CurlMulti/Base/Clone.php**<br>
A powerfull site clone tool.It's a perfect tool.

<sub>**Feature：**

1. <sub>It's a work of art on software engineer and programming technique.
1. <sub>Easy to use, has only one public method start(void).
1. <sub>Low coupling,easy to extend.Copying a site with CurlMulti is very fast.
1. <sub>All duplicate url in all pages will be processed only once.
1. <sub>All url and uri in pages will be accurately processed automaticly!
1. <sub>@import in css and images in css can be downloaded automaticly,ignore @import depth!
1. <sub>Can process multi url prefix and config the url individually.
1. <sub>Subprefix for url can be specified and config for the subprefix can be specified.
1. <sub>Process 3xx redirect automaticly.
1. <sub>Resources cross site will be shared.For example,site A use js and css of B,when clone B this css and js will not be processed again.
1. <sub>In one dir arbitray number site can be located and no file will conflict.
1. <sub>Download option support multitype control.

<sub>**issue:**

<sub>1. Css annotation for IE will not be processed,because a standard way is not founded by now.

<sub>Clone of site: http://manual.phpdr.net/


**phpQuery.php**<br>
[https://code.google.com/p/phpquery/](https://code.google.com/p/phpquery/ "Official Website")

API(CurlMulti_Core)
-------------------
```PHP
public $maxThread = 10
```
Max concurrence num, can be changed in the fly.<br>
The limit may be associated with OS or libcurl,but not the library.

```PHP
public $maxThreadType = array ()
```
Set maxThread for specified task type.Key is type(specified in add()).Value is parallel.The sum of values can exceed $maxThread.Parallel of notype task is value of $maxThread minus the sum.Parallel of notype less than zero will be set to zero.Zero represent no type task will never be excuted except the config changed in the fly.

```PHP
public $maxTry = 3
```
Trigger curl error or user error before max try times reached.If reached $cbFail will be called.

```PHP
public $opt = array ()
```
Global CURLOPT_* for all tasks.Overrided by CURLOPT_* in add().

```PHP
public $cache = array ('enable' => false, 'enableDownload'=> false, 'compress' => false, 'dir' => null, 'expire' =>86400, 'dirLevel' => 1)
```
The options is very easy to understand.Cache is identified by url.If cache finded,the class will not access the network,but return the cache directly.

```PHP
public $taskPoolType = 'stack'
```
Values are 'stack' or 'queue'.This option decide depth-first or width-first.Default value is 'stack' depth-first.

```PHP
public $cbTask = array(0=>'callback',1=>'callback param')
```
When the parallel is less than $maxThread and taskpool is empty the class will try to call callback function specified by $cbTask.$cbTask[0] is callback itself.$cbTask[1] is parameters for the callback.

```PHP
public $cbInfo = null
```
Callback for running info.Use print_r() to check the info in callback.The speed is limited once per second.

```PHP
public $cbUser = null
```
Callback for user operations very frequently.You can do anything there.

```PHP
public $cbFail = null
```
Callback for failed tasks.Lower priority than 'fail callback' specified than add().

```PHP
public function __construct()
```
Musted be called in subclass.

```PHP
public function add(array $item, $process = null, $fail = null)
```
Add a task to taskpool.<br>
**$item['url']** Must not be emtpy.<br>
**$item['file']** If is setted the content of the url will be saved.Should be absolute path.The last level directory will be created automaticly.<br>
**$item['opt']=array()** CURLOPT_* for current task.Override the global $this->opt and merged.<br>
**$item['args']** Second parameter for callbacks.Include $this->cbFail and $fail and $process.<br>
**$item['ctl']=array()** do some additional control.type，cache，ahead。<br />
*$item['ctl']['type']* Task type use for $this->maxThreadType。<br />
*$item['ctl']['cache']=array('enable'=>null,'expire'=>null)* Task cache.Override $this->cache and merged.<br />
*$item['ctl']['close']* close ch automaticly or not.<br />
*$item['ctl']['ahead']* Regardless of $this->taskPoolType.The task will be allways add to parallel prioritized.<br />
**$process** Called if task is success.The first parameter for the callback is array('info'=>array(),'content'=>'','ext'=>array()) and the second parameter is $item['args'] specified in first parameter of add().First callback parameter's info key is http info,content key is url content,ext key has some extended info.If return false in callback,the task will be backoffed to the tail of the taskpool that it will be called again later with same state of current.Returning false is risky,because you must guarantee stop returning false yourself to avoid endless loop.<br />
**$fail** Task fail callback.The first parameter has two keys of info and error.Info key is http info.The error key is full error infomation.The second parameter is $item['args'].

```PHP
public function error($msg)
```
A powerfull method.If you think current task is fail in $process(second parameter of $this->add()) callback,you can call this method to make the task go $this->maxTry loop.<br />
Download task is not affected.Cache write will be ignored.<br>
Must be called in $process.

```PHP
public function start($persist=null)
```
Start the loop.This is a blocked method.
Param $persist is a callback,if true returned and all tasks finished start() will still block.Sleep must be set in callback if needed.

```PHP
public function getch($url = null)
```
Get a curl resource with global $this->opt.

API(CurlMulti_Base)
-----------------
```PHP
function __construct($curlmulti = null)
```
Set up use default CurlMulti_Core or your own instance.

```PHP
function hashpath($name, $level = 2)
```
Get hashed path.Every directory has max 4096 files.

```PHP
function substr($str, $start, $end = null, $mode = 'g')
```
Get substring between start string and end string.Start and end string are excluded.

```PHP
function cbCurlFail($error, $args)
```
Default fail callback.

```PHP
function cbCurlInfo($info)
```
Default CurlMulti_Core::$cbInfo

```PHP
protected function curlInfoString($info)
```
Get info string

```PHP
function hasHttpError($info)
```
If http code is 200.

```PHP
function encoding($html, $in = null, $out = 'UTF-8', $mode = 'auto')
```
Powerfull function to convert html encoding and set \<head\>\</head\> in html.$in can be get from \<head\>\</head\>.

```PHP
function isUrl($str)
```
If is a full url.

```PHP
function uri2url($uri, $urlCurrent)
```
Get full url of $uri used in the $urlCurrent html page.

```PHP
function url2uri($url, $urlCurrent)
```
get relative uri of the current page.

```PHP
function urlDir($url)
```
url should be redirected final url.Final url normally has '/' suffix.

```PHP
function getCurl()
```
Return CurlMulti_Core instance.
