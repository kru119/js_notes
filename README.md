# JavaScript作用域
简单的说，作用域就是变量与函数的可访问范围，即作用域控制着变量与函数的可见性和生命周期。在JavaScript中，变量的作用域有全局作用域和局部作用域两种。
1.  全局作用域（Global Scope）
在代码中任何地方都能访问到的对象拥有全局作用域，一般来说以下几种情形拥有全局作用域：
* 最外层函数和在最外层函数外面定义的变量拥有全局作用域，例如：
```
var authorName="山边小溪";
function doSomething(){
    var blogName="梦想天空";
    function innerSay(){
        alert(blogName);
    }
    innerSay();
}
alert(authorName); //山边小溪
alert(blogName); //脚本错误
doSomething(); //梦想天空
innerSay() //脚本错误
```
（2）所有末定义直接赋值的变量自动声明为拥有全局作用域，例如：
```
function doSomething(){
    var authorName="山边小溪";
    blogName="梦想天空";
    alert(authorName);
}
doSomething(); //山边小溪
alert(blogName); //梦想天空
alert(authorName); //脚本错误
```
3）所有window对象的属性拥有全局作用域一般情况下，window对象的内置属性都拥有全局作用域，例如window.name、window.location、window.top等等。
### 作用域链（Scope Chain）
在JavaScript中，函数也是对象，实际上，JavaScript里一切都是对象。函数对象和其它对象一样，拥有可以通过代码访问的属性和一系列仅供JavaScript引擎访问的内部属性。其中一个内部属性是[[Scope]]，由ECMA-262标准第三版定义，该内部属性包含了函数被创建的作用域中对象的集合，这个集合被称为函数的作用域链，它决定了哪些数据能被函数访问。当一个函数创建后，它的作用域链会被创建此函数的作用域中可访问的数据对象填充。例如定义下面这样一个函数：
```
function add(num1,num2) {
    var sum = num1 + num2;
    return sum;
}
```
在函数add创建时，它的作用域链中会填入一个全局对象，该全局对象包含了所有全局变量，如下图所示（注意：图片只例举了全部变量中的一部分）：
![](https://pic002.cnblogs.com/images/2011/36987/2011090414435366.jpg)
函数add的作用域将会在执行时用到。例如执行如下代码：
```
var total = add(5,10);
```
执行此函数时会创建一个称为“运行期上下文(execution context)”的内部对象，运行期上下文定义了函数执行时的环境。每个运行期上下文都有自己的作用域链，用于标识符解析，当运行期上下文被创建时，而它的作用域链初始化为当前运行函数的[[Scope]]所包含的对象。

　　这些值按照它们出现在函数中的顺序被复制到运行期上下文的作用域链中。它们共同组成了一个新的对象，叫“活动对象(activation object)”，该对象包含了函数的所有局部变量、命名参数、参数集合以及this，然后此对象会被推入作用域链的前端，当运行期上下文被销毁，活动对象也随之销毁。新的作用域链如下图所示：
 ![](https://pic002.cnblogs.com/images/2011/36987/2011090414444843.jpg)
 　在函数执行过程中，没遇到一个变量，都会经历一次标识符解析过程以决定从哪里获取和存储数据。该过程从作用域链头部，也就是从活动对象开始搜索，查找同名的标识符，如果找到了就使用这个标识符对应的变量，如果没找到继续搜索作用域链中的下一个对象，如果搜索完所有对象都未找到，则认为该标识符未定义。函数执行过程中，每个标识符都要经历这样的搜索过程。
  ### 作用域链和代码优化
  从作用域链的结构可以看出，在运行期上下文的作用域链中，标识符所在的位置越深，读写速度就会越慢。如上图所示，因为全局变量总是存在于运行期上下文作用域链的最末端，因此在标识符解析的时候，查找全局变量是最慢的。所以，在编写代码的时候应尽量少使用全局变量，尽可能使用局部变量。一个好的经验法则是：如果一个跨作用域的对象被引用了一次以上，则先把它存储到局部变量里再使用。例如下面的代码：
  ```
  function changeColor(){
    document.getElementById("btnChange").onclick=function(){
        document.getElementById("targetCanvas").style.backgroundColor="red";
    };
}
```
这个函数引用了两次全局变量document，查找该变量必须遍历整个作用域链，直到最后在全局对象中才能找到。这段代码可以重写如下：

```
function changeColor(){
    var doc=document;
    doc.getElementById("btnChange").onclick=function(){
        doc.getElementById("targetCanvas").style.backgroundColor="red";
    };
}
```
### 改变作用域链

