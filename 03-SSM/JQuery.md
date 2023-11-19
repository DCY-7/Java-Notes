## 1. 关于jQuery

### 1.1 什么是jQuery

> ​	**jQuery**是一套跨[浏览器](https://zh.wikipedia.org/wiki/瀏覽器)的[JavaScript](https://zh.wikipedia.org/wiki/JavaScript)[库](https://zh.wikipedia.org/wiki/函式庫)，简化[HTML](https://zh.wikipedia.org/wiki/HTML)与JavaScript之间的操作。jQuery的语法设计使得许多操作变得容易，如操作文档对象（document）、选择[文档对象模型](https://zh.wikipedia.org/wiki/文档对象模型)（DOM）元素、创建[动画](https://zh.wikipedia.org/wiki/动画)效果、处理[事件](https://zh.wikipedia.org/w/index.php?title=事件_(计算机)&action=edit&redlink=1)、以及开发[Ajax](https://zh.wikipedia.org/wiki/AJAX)程序。

### 1.2 jQuery的设计思路

​	write Less，Do More

### 1.3 jQuery的应用

- jQuery-ui
- Layui
- jQuery-mini-ui(风格和ExtjJS)

### 1.4 其他前端框架

- BootStrap
- Vue
- ExtjJS

### 1.5 jQuery能干什么

​	jQuery能做大多数Javascript能做的功能，jQuery能实现的javasc都能实现，而JavaScript有的内容，jQuery无法实现。

- 访问和操作DOM元素
- 控制页面样式
- 可以对页面样式进行处理
- 扩展的插件（轮播图插件）
- 与Ajax完美的结合

### 1.6 jQuery的优势

- 体积小，jquery-3.5.0.min.js只有88kb
- 强大的选择器
- 出色的DOM封装
- 可靠的事件处理机制
- 有很强的浏览器兼容性
- 使用隐式迭代简化JS编程
- 丰富的插件支持

## 2. jQuery的使用

### 2.1 加载jQuery

​	jQuery库是包含所有公共DOM、事件、效果和Ajax函数的一个JavaScript文件。可以通过链接到**本地副本**或**公共服务器提供的许多副本之一**把jQuery包含在网页中。

```javascript
<script src="jquery.js"></script>
```

也可以直接从**CDN**（内容分发网络）中加载jQuery：

```javascript
<script
  src="https://code.jquery.com/jquery-3.3.1.min.js"
  integrity="sha256-FgpCb/KJQlLNfOu91ta32o/NMZxltwRo8QtmkMRdAu8="
  crossorigin="anonymous"></script>
```



### 2.2 与JS书写的比较

​	需求：登录界面，点击登录，判断用户名和密码不能为空。

- 原生态JS

```html
<!DOCTYPE html>
<html>
    <body>
        <form>
            用户名：<input type="text" id="username"/><br>
            密码;<input type="password" id="pwd"/><br>
            <input type="button" value="登录" onclick="checkLogin();"/>
        </form>
    </body>
    <script>
        function checkLogin(){
            var username = document.getElementById("username").value;
            var pwd = document.getElementById("pwd").value;
            if(username.length==0){ 
                alert('用户名不能为空！'); 
                return;
            }if(pwd.length==0){ 
                alert('密码不能为空') 
                return;
            }
        }
    </script>
</html>
```

- jQuery写法

```html
<!DOCTYPE html>
<html>
    <body>
        <form>
            用户名：<input type="text" id="username"/><br>
            密码;<input type="password" id="pwd"/><br>
            <input type="button" value="登录" onclick="checkLogin();"/>
        </form>
    </body>
    <!--导入jquery核心库-->
    <script type="text/javascript" src="jquery-3.5.0.min.js"></script>
    <script>
        function checkLogin(){
            var username = $("#uername").val;
            var pwd = $("#pwd").val;
            if(username.length==0){ 
                alert('用户名不能为空！');
                return;
            }if(pwd.length==0){ 
                alert('密码不能为空') 
                return;
            }
        }
    </script>
</html>
```

## 3.jQuery的语法结构

> $(selector).action   或   jQuery(selector).action

工厂函数：$()

选择器：selector

方法：action	jQuery提供的方法

> 在jQuery中访问和操作多个DOM节点通常从用CSS选择器字符串调用`$`函数开始。这会返回一个引用[HTML](https://zh.wikipedia.org/wiki/HTML)页面中所有匹配元素的jQuery对象。比如`$("div.test")`，会返回一个拥有class `test`的所有`div`元素的jQuery对象。可以通过调用返回的jQuery对象或节点本身的方法来操作这个节点集。

- $冲突

  将$使用权，让出来

  ```javascript
  var $j=jQuery.noconfilict();//将$的使用权让出，并定义新的使用方式
  ```

- 异常

  ```
  login2.html:19 Uncaught TypeError: $ is not a function at 
  		checkLogin (login2.html:19) at 
  		HTMLInputElement.onclick (login2.html:11)
  ```

  该异常出现的原因：

  1. 没有导入jQuery核心库文件
  2. 没有将$使用权让出来



## 4. jQuery中ready() 事件和JavaScript中onload()事件比较

### 4.1 DOM文档加载步骤

1. 解析HTML结构
2. 加载外部脚本和样式文件
3. 解析并执行脚本代码（按文档顺序加载解析）
4. 构造HTML DOM模型，由抽象语法树构造成的DOM树    //$().ready
5. 加载图片等外部文件
6. 页面加载完毕    //$().onload

### 4.2 ready()事件的写法

- 写法1

```javascript
$(document).ready(function(){
    //ready事件被触发时需要执行的业务逻辑
});
```

- 写法2（推荐使用）

```javascript
$(function(){
    //ready事件被触发时需要执行的业务逻辑
});
```

### 4.3 实例

```html
<!DOCTYPE html>
<html>
    <body onload="init();">
        <!--假设图片大小为1G-->
        <img src="" />
    </body>
    <!--导入jquery核心库-->
    <script type="text/javascript" src="jquery-3.5.0.min.js"></script>
    <script>
        function init(){
            alert('加载完成 1');
        };
        $(function(){
        	alert('加载完成 2');
        });
        $(document).ready(function(){
            alert('加载完成 3');
        });
    </script>
</html>
```

> ready事件在所有的组件全部绘制完成，后马上执行。ready事件可以在同页面中多次出现

> load事件是在所有的组件绘制完成，并且加载完成后执行。只能出现一次



## 5. 选择器

### 5.1 基本选择器（重要）

```javascript
$("#id")
$("标签名称")
$(".类名")
$("selector1seletor2")
$("*")
```

- id选择器
- 标签选择器-------jQuery对象的集合，如果选择结果只有一个，那么是当前对象
- 类选择器----------jQuery对象的集合，类名是在css中定义
- 并集选择器-------满足selector1选择器，并且也满足selector2选择器的元素的集合
- 全局选择器-------选择所有元素的jQuery对象的集合,包含html,head,body,style,form...

### 5.2 层次选择器（重要）

```javascript
$("selector1 selector2")
$("selector1>selector2")
$("selector1+selector2")
$("selector1~selector2")
```

- 后代选择器----------可以选择该元素的子代+孙代+...

- 子代选择器----------选择selector1中满足selector2选择器的元素，只获取子代元素（扔掉:孙代...）

- 相邻元素选择器----选择满足selector1元素后面的满足selector2的元素

  **注意：只能选择相邻后面的一个**

- 同辈元素选择器----选择selector1元素之后的同辈元素，并且同辈元素满足selector2的要求

  **注意：前面的同辈元素是无法选择的**

### 5.3 属性过滤选择器（重要）

```javascript
$(selector1[attributeName^|$|*=''])
```

> 在selector1选择器的基础上，对属性进行选择=代表属性值必须和'指定值'一致； ^= 属性以‘指定值’开
>
> 头； $= 属性以'指定值'结尾； *= 属性值中包含有'指定值'

- [attributeName='指定值']
- [attributeName^='指定值']        like ‘a%’
- [attributeName$='指定值']        like '%a'
- [attributeName*='指定值']         like '%a%'

### 5.4 基本过滤选择器

```javascript
$("li:first")  $("li:eq(2)")  $("li:even") $("li:gt(1)")
```

> 都是以:开头

- :eq(index):选择索引等于index的元素
- :gt(index)选取索引大于index的元素
- :lt(index)选择索引小于index的元素
- :header选取的(h1-h6)的元素
- :focus获取所有获取焦点的元素
- :animated选择所有的动画
- :first 选择第一个元素
- :last选择最后一个元素
- :not(selector)：选择不包含selector选择器的元素
- :even：选取索引为偶数的元素
- :odd:选取所以为奇数的元素

实例：

```html
<!DOCTYPE html>
<html>
    <head>
        <title>基本过滤选择器</title>
        <style>
            .a{ 
                height:100px; width:100px; 
            }
        </style>
    </head>
    <body>
        <h1>标题1</h1>
        <h2>标题2</h2>
        <h3>标题3</h3>
        <h4>标题4</h4>
        <input type="text" value="1"/>
        <input type="text" value="2"/>
        <input type="text" value="3" class="a"/>
        <input type="text" value="4"/>
        <input type="text" value="5" class="a"/><br>
        <input type="button" onclick="click1()" value="eq"/>
        <input type="button" onclick="click2()" value="gt"/>
        <input type="button" onclick="click3()" value="lt"/>
        <input type="button" onclick="click4()" value="header"/>
        <input type="button" onclick="click6()" value="获取第一个元素"/>
        <input type="button" onclick="click7()" value="获取最后一个元素"/>
        <input type="button" onclick="click8()" value="不包含class=a"/>
        <input type="button" onclick="click9()" value="获取偶数位元素"/>
        <input type="button" onclick="click10()" value="获取奇数位元素"/>
        <input type="button" onclick="click11()" value="手写偶数位元素"/>
    </body>
    <!--导入jquery核心库-->
    <script type="text/javascript" src="jquery-3.5.0.min.js"></script>
    <script>
        function click1(){
            alert($("input:eq(1)").val());
        }
        function click2(){
            $("input[type='text']:gt(1)").each(function(){
                alert($(this).val());
            });
        }
        function click3(){
            $("input[type='text']:lt(1)").each(function(){
                alert($(this).val());
            });
        }
        function click4(){
            $(":header").each(function(){
                alert($(this).html());
            });
        }
        function click5(){
            $("input:eq(1)").focus();
            $(":focus").each(function(){
                alert(this.value);
            });
        }
        function click6(){
            alert($("input:first").val());
        }
        function click7(){
            var a = 'a'; var b="b";
            alert($("input[type='text']:last").val());
        }
        function click8(){
            $("input[type='text']:not(.a)").each(function(){
                alert($(this).val());
            });
        }
        function click9(){
            $("input[type='text']:even").each(function(){
                alert($(this).val());
            });
        }
        function click10(){
            $("input[type='text']:odd").each(function(){
                alert($(this).val());
            });
        }
        function click11(){
            $("input[type='text']").each(function(index){
                if(index%2==0){
                    alert($(this).val());
                }
            });
        }
    </script>
</html>
```



### 5.5 可见性过滤选择器

- :visible

> 选取所有可见元素（占据文档流的位置，例如:visibility:hidden,opacity:0,width:0px也是可见的)

- :hidden

> 选取所有隐藏元素(display:none,type="hidden)

### 5.6 表单过滤选择器

- ：input---------匹配所有的input元素
- ：text-----------匹配所有的单行文本框
- :password------匹配所有密码框
- :image-----------匹配所有图像域
- :file---------------匹配所有文件域
- :radio------------匹配所有单选按钮
- :checkbox------匹配所有复选框
- :button----------匹配所有按钮
- :submit----------匹配所有提交按钮
- :reset-------------匹配所有重置按钮

### 5.7 内容过滤选择器

- :contains("text")----------匹配包含给定文本的元素
- :empty()---------------------匹配所有不包含子元素或者文本的空元素
- :has(selector)--------------匹配含有选择器所匹配的元素的元素
- :parent-----------------------匹配含有子元素或者文本的元素

### 5.8 子元素过滤选择器

- :first-child-------------匹配所给选择器( :之前的选择器)的第一个子元素

- :last-child--------------匹配最后一个子元素

- :nth-child(index)-----匹配其父元素下的第N个子或奇偶元素

  > index值从1开始，可以是数字
  >
  > 可以是even偶数，odd奇数，可以使用表达式2n,3n,3n+1...

- :only-child--------------如果某个元素是父元素中唯一的子元素，那将会被匹配




## 6.jQuery对象和JavaScript对象

- JavaScript对象

  ```javascript
  var obj = document.getElementById("id");//js对象
  //可以调用js相关的属性和方法
  var value = obj.value;//获取到文本框的值
  var html = obj.innerHTML;//获取到HTML的内容，包含HTML标签
  var text = obj.text;//获取到text内容，不包含HTML标签
  ```

- jQuery对象

  使用jquery方式获取的对象，都是jquery对象，只能使用jquery提供的属性和方法

  **注意**：jquery对象的命名方式，以$开头

- JavaScript对象和jQuery对象的相互转换

  - JavaScript对象转为jQuery对象

    ```javascript
    //将js对象直接放入到工厂函数中，js对象就转为jquery对象了
    var user = document.getElementById("username");//js对象
    var $username = $(username);//$username就变为jQuery对象了
    ```

  - jQuery对象转为JavaScript对象

    ```javascript
    //jquery对象是一个伪数组，我们可以使用数组操作的相关方式，并且0标位放置的就是js对象
    var $username = $("#username");//使用id选择器获取的Jquery对象
    var username = $username.get(0);//获取0标位的值，也就==获取到js对象
    var username2 = $username[0];//也可以获取js对象
    ```



## 7. jQuery操作DOM

- CORE DOM：任何一种支持DOM操作的语言都可以操作的对象

  ```javascript
  document.getElementById("id")
  ```

- HTML DOM：用于处理HTML文档

  ```javascript
  document.forms;//获取所有的form表单
  ```

- CSS DOM：   用于 操作css样式的

  ```javascript
  element.style.color=red;//将elment元素的color设置为red
  ```



### 7.1 样式操作

- css('styleName','styleVaule')----------------------------------------------------修改行内样式
- css({'styleName1':'styleVaule1','styleName2':'styleVaule2'...})---------隐式迭代
- addClass('className')-----------------------------------------------------------添加单个class样式（不是行内样式）
- addClass(className1 className2 className3...)-----------------------添加多个class样式
- removeClass(className)--------------------------------------------------------单个样式移除
- removeClass(className1 className2 className3 ...)-----------------移除多个样式
- hasClass(className)----------------------------------返回boolean ，如果元素中有指定的样式，返回true,否则返回false
- toggleClass(className)-------样式切换（当当前元素有className样式时，调用removeClass,否则调用addClass）

```html
<!DOCTYPE html>
<html>
    <head>
        <title>修改样式</title>
        <style>
            div{ height:100px; width:100px; border:1px solid yellow; }
            .a{ font-size:100px; }
            .b{ color:blue; }
        </style>
    </head>
    <body>
        <div id="div1">div1</div>
        <div id="div2">div2</div>
        <div>div3</div><br>
        <input type="button" value="使用js将div1的高度调整为50px" onclick="jsHeight();"/>
        <input type="button" value="使用jquery将div1的高度调整为50px" onclick="jqueryHeight();"/>
        <input type="button" value="css隐式迭代" onclick="ysdd();"/>
        <input type="button" value="addClass" onclick="addClass();"/>
        <input type="button" value="addClasses" onclick="addClasses();"/>
    </body>
    <!--导入jquery核心库-->
    <script type="text/javascript" src="jquery-3.5.0.min.js"></script>
    <script>
        function jsHeight(){ 
            document.getElementById("div1").style.height='50px';
        }
        function jqueryHeight(){ 
            $("#div1").css('height','50px'); 
        }
        function ysdd(){
            $("#div1").css({"height":'50px','color':'green','font-size':'20px'});
        }
        function addClass(){ 
            $("#div2").addClass("a");
        }
        function addClasses(){
            $("#div2").addClass("a b");
        }
    </script>
</html>
```



### 7.2 内容及值操作

**注意**：对比JavaScript，JavaScript中使用的获取属性和为属性设置的方式，jQuery中使用的是方法

- val

  - val() ：用于获取单行文本框的值

    ```js
    var $obj = $("#username");
    var obj = document.getElementById("username");
    $obj.val()==obj.value;
    ```

  - val('')：用于设置单行文本框的值

    ```javascript
    var $obj = $("#username");
    var obj = document.getElementById("username");
    $obj.val('helloWord');
    obj.value='helloWorld';//两种方式等价
    ```

- text

  - text()：用于获取多行文本值（textarea,其他标签中的去除html代码的文本）

    ```javascript
    var $obj = $("#username");
    var obj = document.getElementById("username");
    $obj.text();
    obj.innerText;//两种方式等价
    ```

  - text("paramter")：用于设置多行文本的值（textarea,其他标签中的文本信息）

    ```javascript
    var $obj = $("#username");
    var obj = document.getElementById("username");
    $obj.text("hellowWorld");
    obj.innerText="HelloWorld";//两种方式等价
    ```

- html

  - html()：用于获取标签中的html代码（html标签+文本）

    ```javascript
    var $obj = $("#username");
    var obj = document.getElementById("username");
    $obj.html();
    obj.innerHTML;//两种方式等价
    ```

  - html("parameter")：用于设置标签中的html代码

    ```javascript
    var $obj = $("#username");
    var obj = document.getElementById("username");
    $obj.html("<div>HelloWorld</div>");
    obj.innerHTML="<div>HelloWorld</div>";//两种方式等价
    ```



### 7.3 节点操作

- 创建节点$(HTML代码)后返回当前创建节点的对象

  ```javascript
  document.createElement("li");
  var $li = $("<li></li>");//两种方式等价
  ```



#### 7.3.1 内部插入（子节点）

- 在内部最后一个元素后插入节点
  - selector1.append(节点对象)：在selector1元素内部的最后插入"节点对象"
  - 节点对象.appendTo(selector1)：将"节点对象"插入到selector1内部的最后
- 在内部第一个元素前插入节点
  - selector1.prepend(节点对象)：在selector1的内部的第一个元素之前插入"节点对象"
  - 节点对象.prependTo(selector1)：将"节点对象"插入到selector1内部的第一个



#### 7.3.2 外部插入（同辈节点）

- 在指定节点后面插入节点
  - selector1.after(节点对象)：在selector1后插入"节点对象"
  - 节点对象.insertAfter(selector1)：将"节点对象"出入到selector1后面
- 在指定节点前面插入节点
  - selector1.before(节点对象)：在selector1前面插入"节点对象"
  - 节点对象.insertBefore(selector1)：将"节点对象"插入到selector1前面



#### 7.3.3 节点删除

- remove()：从DOM中删除所有匹配的元素。

  > 这个方法不会把匹配的元素从jQuery对象中删除，因而可以在将来再使用这些匹配的元素。但除了这个元素本身得以保留之外，其他的比如绑定的事件，附加的数据等都会被移除。

- empty()：删除匹配的元素集合中所有的**子节点**

- detach()：从DOM中删除所有匹配的元素

  > 这个方法不会把匹配的元素从jQuery对象中删除，因而可以在将来再使用这些匹配的元素。与
  >
  > remove()不同的是，所有绑定的事件、附加的数据等都会保留下来。



#### 7.3.4 节点替换

- selector1.replaceWith(selector)：将所有匹配的元素替换成selector元素。

- selector2.replaceAll(selector)：用匹配的元素（selector2）替换掉所有 selector匹配到的元素。

  

#### 7.3.5 节点复制

- selector.clone(true|false)：复制当前节点,参数为true时，表示复制节点包含节点的事件，false

  表示不包含事件
  
  

#### 7.3.6 节点查找

- **查找子元素：**取得一个包含匹配的元素集合中每一个元素的所有子元素的元素集合。

  ```javascript
  //$().children();
  ```

- **查找后代元素（包含子元素）**：搜索所有与指定表达式匹配的元素。这个函数是找出正在处理的元素的后代元素的好方法。

  ```javascript
  //$().find(selector)
  ```

- **查找同辈元素**

  - 取得一个包含匹配的元素集合中每一个元素紧邻的后面同辈元素的元素集合。

    每个选择器匹配的结果元素只会获取到一个相邻之后的同辈元素

    ```javascript
    //$().next(express);
    ```

  - 取得一个包含匹配的元素集合中每一个元素紧邻的前一个同辈元素的元素集合。

    查找目标元素之前和之后所有同辈元素

    ```javascript
    //$().prev(express);
    ```

  - 查找父元素

    ```javascript
    //$().parent(selector);
    ```

  - 查找所有祖先元素

    ```javascript
    //$().parents(selector);
    ```

    

#### 7.3.7 元素的遍历

```javascript
each(function(index,element){
    //index代表当前变量的索引 
    //当前遍历的节点对象 
    //this可以获取当前遍历节点的js对象
});

end();//回到最近的一个"破坏性"操作之前。即，将匹配的元素列表变为前一次的状态。

```



#### 7.3.8 节点属性操作

​	$().attr(attrName,attrValue):给选择的元素添加attrName，值为attrValue

- 单个属性的设置

```javascript
$("#img1").attr("src","https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=2167576379,3073240836&fm=15&gp=0.jpg");
```

- 多个属性的设置

```javascript
//$(selector).attr({attrName1:attrVaule1,attrName2:attrValue2....});
```

- 获取属性值

```javascript
//$(selector).sttr(attrName);
```

- 删除属性

```javascript
//$(selector).removeAttr(attrName)
```



#### 7.3.9 CSS-DOM操作

- $(selector).css("")：返回或设置匹配元素的样式属性。

- $(selector).height( [value] )：返回或设置元素的高度（设置的是height、widith属性，不能添加

  单位）

- $(selector).width( [value] )：返回或设置元素的宽度

- $(selector).offffset([value])：获取匹配元素在当前视口的相对偏移。返回的对象包含两个整型属性：top 和 left，以像素计。此方法只对可见元素有效。

- $(selecotr).offffsetParent([VALUE])：返回第一个匹配元素用于定位的父节点。这返回父元素中第一个其position设为relative或者absolute的元素。此方法仅对可见元素有效。
- $(selector).position()：获取匹配元素相对父元素的偏移。返回的对象包含两个整型属性：top 和 left。为精确计算结果，请在补白、边框和填充属性上使用像素单位。此方法只对可见元素有效。
- $(selector).scrollLeft()：获取匹配元素相对滚动条左侧的偏移。此方法对可见和隐藏元素均有效。
- *$(selecotr).scrollTop()*：获取匹配元素相对滚动条顶部的偏移。此方法对可见和隐藏元素均有效。

## 8.事件和动画

### 8.1 基础事件

- click()：对应 onclick 鼠标单击事件
- dbclick()：对应 ondbclick 鼠标双击事件
- mouseover()：对应 onmouseover 鼠标移入事件
- mouseout()：对应 onmouseout 鼠标移出事件
- mouseneter()：对应 onmouseenter 鼠标进入事件
- mouseleave()：对应 onmouseleave 鼠标离开事件

> **注意：**mourseover和mourseenter都是鼠标移入元素时触发，不同点：mourseover无论鼠标移入被选元素
>
> 还是被选元素的子元素都会触发。mourseenter只有移入被选元素才会触发
>
> mourseout和mourseleave都是鼠标移除元素时触发。不同点：mourseout在移除被选元素的子元素
>
> 时也会被触发。mourseleave值有移出被选元素才会被触发。

- keyup 对应onkeyup 键盘弹起
- keydown 对应onkeydown 键盘按下触发
- keypress 对应onkeypress 鼠标产生可打印的字符时触发
- $(window).resize() 窗口大小调整时触发的事件



### 8.2 复合事件

#### 8.2.1 显示和隐藏

- show(speed|function)：

  > speed定义显示的速度,slow,normal,fast。function是回调函数，当目标
  >
  > 元素全部显示完成后触发。作用：将隐藏元素变为可见的(将display:none-->display:block),从左上
  >
  > 角开始显示。

- hide(speed|function)：将元素隐藏

- fadein(speed|function)：

  > 定义显示的速度,slow,normal,fast。function是回调函数，当目标元素全
  >
  > 部显示完成后触发。作用：将隐藏元素变为可见的(将display:none-->display:block),通过调整透明
  >
  > 度来显示

- fadeOut(speed|function)：通过调整透明度，隐藏元素

- slideDown(speed|function)：

  > 定义显示的速度,slow,normal,fast。function是回调函数，当目标
  >
  > 元素全部显示完成后触发。作用：将隐藏元素变为可见的(将display:none-->display:block),通过调
  >
  > 整高度来显示

- slideUp(speed|function)：通过调整高度来隐藏元素



#### 8.2.2 事件切换

- Hover(over,out)：

> 一个模仿悬停事件（鼠标移动到一个对象上面及移出这个对象）的方法。这是一
>
> 个自定义的方法，它为频繁使用的任务提供了一种“保持在其中”的状态。
>
> 当鼠标移动到一个匹配的元素上面时，会触发指定的第一个函数。当鼠标移出这个元素时，会触发
>
> 指定的第二个函数。而且，会伴随着对鼠标是否仍然处在特定元素中的检测（例如，处在div中的
>
> 图像），如果是，则会继续保持“悬停”状态，而不触发移出事件（修正了使用mouseout事件的一
>
> 个常见错误）。

```javascript
$(function(){ $("#div0").hover(function(){ alert('over'); },function(){ alert('out'); }); });
```

- toggle()：无参写法是对show()和hide()的切换

### 8.3 事件的绑定和移除

- 在js中的函数调用的方式

  - 事件调用(onclick="")

  - 注解在js中调用

    ```javascript
    <script>
        a(); 
    </script>
    ```

  - 函数调用

    ```javascript
    <script> 
        function a(){ }
    	function b(){ 
            a(); 
        }
    </script>
    ```

- 使用匿名函数来表示事件

  ```javascript
  //onload body 
  <script> 
      window.onload=function(){ 
      	//所写的业务逻辑会在window.load事件被触发时调用 
  	}
  <!--jquery中也可以加载window load事件(jquery3.x没有效果)--> 
      $(window).load=function(){ 
      	alert('$window.load') 
      }
  </script>
  ```

  

- jquery新增事件（将新增事件放置在ready事件中）

  保证你在添加事件时能够选择到元素

  ```javascript
  $(function(){ 
      $(selector).event(function(){ 
          //event是jquery的基础事件click,dbclick 
          //this可以获取到当前元素的js对象 
      })
  });
  ```

  

- 动态绑定事件（不一定放置在ready事件中）

  ```javascript
  $(selector).bind('event',function(){ 
      //event是js的基本时间 
      //this可以获取当前元素的js对象 
  });
  //移除绑定事件 
  $(selector).unbind('event'); 
  //将event:click等基础事件
  ```

  

> **注意**：jQuery大多数元素的事件都会使用新增事件或动态绑定的方式添加。



### 8.4 动画

- $(selector).animate(params,speed,callback);
- params:动画的目标样式
- speed:从现在样式过渡到目标样式需要的时间
- callback:动画执行完成后的回调函数。



> **注意**：可以停止show(),hide(),fadeIn(),fadeOut,slideUp(),slideDown();





# 课后作业：

- 实现ready事件和onload事件的执行顺序（使用一张大图片）
- 1.练习所有选择器
- 2.对表格实行各行变色，使用jquery控制每行的颜色
- 棍子英雄

