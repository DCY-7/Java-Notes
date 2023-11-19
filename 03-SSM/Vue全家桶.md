# Vue全家桶

## 1. Vue核心

### 1.1 初识Vue

1. 想让Vue工作，就必须创建一个Vue实例，且传入一个配置对象
2. root容器里的代码符合HTML规范，只不过混入了一些特殊的Vue语法
3. root容器里的代码被称为【Vue模板】
4. Vue实例和容器是一一对应的
5. 真实开发中只有一个Vue实例，并且会配合组件一起使用
6. {{xxx}}中的xxx要写JS表达式，且xxx 可以自动读取到data中的所有属性
7. 一旦data中的数据发生改变，那么模板中用到该数据的地方也会自动更新

### 1.2 Vue模板语法

Vue模板语法有2大类：
1. 插值语法：
功能：用于解析标签体内容
写法：{{xxx}} xxx是JS表达式，字可以直接读取到data中的所有属性
2. 指令语法：
功能：用于解析标签，包括标签属性、标签体内容、绑定事件......
举例：v-bind:href="xxx" 或简写为：:href="xxx"，xxx同样要写JS表达式
备注：Vue中有很多的指令，且形式都是：v-xxxx 此处只是拿v-bind举例

### 1.3 数据绑定

Vue中有2中数据绑定的方式：
1. 单项绑定 v-bind 数据只能从data流向页面
2. 双向绑定 v-model 数据不仅能从data流向页面，还可以从页面流向data

备注：

1. 双向绑定一般都应用在表单类元素上，如：input、select等
2. v-model:value 可以简写为 v-model ，因为v-model默认收集的就是value值

```html
<!--普通写法-->
<!--单项数据绑定：<input type="text" v-bind:value="name"><br>-->
<!--双向数据绑定：<input type="text" v-model:value="name"><br>-->

<!--简写-->
单项数据绑定：<input type="text" :value="name"><br>
双向数据绑定：<input type="text" v-model="name"><br>

<!--如下代码是错误的，因为v-model只能应用在表单类元素（输入类元素）上-->
<!--<h2 v-model:x="name">hello</h2>-->
```

### 1.4 el与data的两种写法

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>el与data的两种写法</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<!--
    el与data的2中写法：
    1. el有2种写法：
        (1)new Vue时配置el属性
        (2)先创建Vue实例，随后再通过vm.$mount('#root')指定el的值
    2. data有2种写法：
        (1)对象式
        (2)函数式
        目前哪种写法都可以，以后学习到组件时，data必须使用函数式，否则会报错
    3. 一个重要的原则：由Vue管理的函数，一定不要写箭头函数，一旦写了箭头函数，this就不再是Vue实例了
-->
<div id="root">

</div>
</body>
<script type="text/javascript">
    Vue.config.productionTip = false

    //el的2种写法
    // const v = new Vue({
    //     //el: '#root', //第一种写法
    //     data: {
    //         name: 'chenyang'
    //     }
    // })
    // console.log(v)
    // v.$mount('#root') //第二种写法

    //data的2种写法
    new Vue({
        el: '#root',
        //data的第一种写法：对象式
        // data: {
        //     name: 'chenyang'
        // }
        //data的第二种写法：函数式
        data() {
            console.log('@@@', this)//此处this指Vue实例对象
            return {
                name: 'chenyang'
            }
        }
    })
</script>
</html>
```

### 1.5 MVVM模型

MVVM模型
1. M：模型(Model)：data中的数据
2. V：视图(View)：模板代码
3. VM：视图模型(ViewModel)：Vue实例

观察发现：

1. data中所有属性，最后都出现在vm身上
2. vm身上所有的属性及Vue原型上所有属性，在Vue模板中都可以直接使用

### 1.6 数据代理

1. Vue中的数据代理：
    通过vm对象来代理data对象中属性的操作（读写）
2. Vue中数据代理的好处：
    更加方便的操作data中的对象
3. 基本原理：
    通过Object.defineProperty()把data对象中所有属性添加到vm上，为每一个添加到vm上的属性，都指定一个getter setter。在getter setter内部去操作（读写）data中的对应的属性

### 1.7 事件处理

事件的基本使用：
1. 使用v-on:xxx 或 @xxx 绑定事件，其中xxx是事件名
2. 事件的回调需要配置在methods对象中，最终会在vm上
3. methods中配置的函数，不要用箭头函数！否则this就不是vm了
4. methods中配置的函数，都是被Vue所管理的函数，this的指向是vm 或 组件实例对象
5. @click="demo" 和 @click="demo($event)" 效果一致，但后者可以传参

Vue中的事件修饰符：

1. prevent：阻止默认事件（常用）

2. stop：阻止事件冒泡（常用）
3. once：事件只触发一次（常用）
4. capture：使用事件的捕获模式
5. self：只有event.target是当前操作的元素时才触发事件
6. passive：事件的默认行为立即执行，无需等待事件回调执行完毕

键盘事件：

1. Vue中常用的按键别名：
        回车 => enter
        删除 => delete （退格或删除键）
        退出 => esc
        空格 => space
        换行 => tab
        上 => up
        下 => down
        左 => left
        右 => right

2. Vue未提供别名的按键，可以使用原始的key值去绑定，但注意要转为kebab-case（短横线命名）
3. 系统修饰键（用法特殊）：ctrl、alt、shift、meta
    - 配合keyup使用：按下修饰键的同时，再按下其他件，随后释放其他键，事件才被触发
    - 配合keydown使用：正常触发事件

4. 也可以使用keyCode去指定具体的按键（不推荐）
5. Vue.config.keyCodes.自定义键名 = 键码，可以去定制按键别名

### 1.8 计算与监视

计算属性：

1. 定义：要用的属性不存在，要通过已有属性计算得来

2. 原理：底层借助了Object.defineproperty方法提供的getter和setter

3. get函数什么时候执行？

    - 初次读取时会执行一次

    - 当依赖的数据发生改变时会被再次调用

4. 优势：与methods实现相比，内部有缓存机制（复用），效率更高，调试方便
5. 备注：
    - 计算属性最终会出现在vm上，直接读取使用即可
    - 如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生改变

计算属性简写：

```js
const vm = new Vue({
    el: '#root',
    data: {
        firstName: '张',
        lastName: '三'
    },
    computed: {
        //完整写法：
        // fullName: {
        //     get() {
        //         return this.firstName + '-' + this.lastName
        //     },
        //     set(vaule) {
        //         const arr = vaule.splice('-')
        //         this.firstName = arr[0]
        //         this.lastName = arr[1]
        //     }
        // }
        //简写：
        fullName() {
            return this.firstName + '-' + this.lastName
        }
    }
})
```

监视属性watch：

1. 当监视的属性改变时，回调函数自动调用，进行相关操作
2. 监视的属性必须存在，才能进行监视
3. 监视的两种写法：
    - new Value时传入watch配置
    - 通过vm.$watch监视

深度监视：

1. Vue中的watch属性默认不监视对象内部值的改变（一层）
2. 配置deep:true可以监视对象内部值的改变（多层）

备注：

- Vue自身可以监视对象内部值的改变，但Vue提供的watch默认不可以
- 使用watch时根据数据的具体结果，决定是否采用深度监视

监视属性简写：

```js
const vm = new Vue({
    el: '#root',
    data: {
        isHot: true,
    },
    computed: {
        info() {
            return this.isHot ? '炎热' : '凉爽'
        }
    },
    methods: {
        changeWeather() {
            this.isHot = !this.isHot
        }
    },
    watch: {
        //正常写法：
        // isHot: {
        //     //immediate: true,
        //     //deep: true,
        //     handler(newValue, oldValue) {
        //         console.log('isHot被修改了', newValue, oldValue)
        //     }
        // },
        //简写：
        // isHot(newValue, oldValue) {
        //     console.log('isHot被修改了', newValue, oldValue)
        // },
    }
})
```

computed与watch之间的区别：

1. computed能完成的功能，watch都能实现
2. watch能完成的功能，computed不一定能完成，例如：watch可以进行异步操作

两个重要的原则：

1. 所被Vue管理的函数，最好写出普通函数，这样this的指向才是vm或组件实例对象
2. 所有不被Vue管理的函数（定时器回调函数、Ajax的回调函数、promise的回调函数等），最好写成箭头函数，这样this的指向才是vm或组件实例对象

### 1.9 class与style绑定



### 1.10 条件渲染

条件渲染：

1. v-if

    ```
    写法：
    v-if="表达式"
    v-else-if="表达式"
    v-else="表达式"
    适用于：切换频率较低的场景
    特点：不展示DOM元素直接被移除
    注意：v-if可以和v-else-if v-else一起使用，但要求结构不能被打断
    ```

2. v-show

    ```
    写法：v-show="表达式"
    适用于：切换频率较高的场景
    特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉
    ```

3. 备注：使用v-if时，元素可能无法获取到，而使用v-show一定可以获取到

### 1.11 列表渲染

v-for指令：

1. 用于展示列表数据
2. 语法：v-for="(item,index) in xxx" :key="yyy"
3. 可遍历：数组、对象、字符串（用到很少）、指定次数（用的很少）

> 面试题：react、Vue中的key有什么作用？（key的内部原理）
>
> 1. 虚拟DOM中key的作用：key是虚拟DOM对象的标识，当状态中的数据发生变化时，Vue会根据【新数据】生成【新的虚拟DOM】，
>     随后Vue进行【新虚拟DOM】与【旧虚拟DOM】的差异比较，比较规则如下：
> 2. 对比规则：
>     1. 旧虚拟DOM中找到了与新虚拟DOM中相同的key：
>         - 若虚拟DOM中内容没变，直接使用之前的真实DOM
>         - 若虚拟DOM中内容遍历，则生成新的真实DOM，随后替换掉页面中之前的真实DOM
>     2. 旧虚拟DOM中未找到与新虚拟DOM相同的key：创建新的真实DOM，随后渲染到页面
> 3. 用index作为key可能会引发的问题：
>     1. 若对数据进行：逆序添加、逆序删除等破坏顺序的操作：会产生没有必要的真实DOM更新=>界面效果没问题，但效率低
>     2. 如果结构中还包含输入类的DOM：会产生错误DOM更新=>界面有问题
> 4. 开发中如何选择key：
>     1. 最好使用每条数据的唯一标识作为key，比如id、手机号、身份证、学号等唯一值
>     2. 如果不存在对每条数据的逆序添加、逆序删除等破坏顺序操作，仅用于渲染列表用于展示，使用index作为key是没有问题的

列表过滤：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>列表过滤</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="root">
    <h2>人员列表</h2>
    <input type="text" v-model="keyWord" placeholder="请输入名字">
    <ul>
        <li v-for="(p,index) in finalPersons" :key="p.id">
            {{p.name}}-{{p.age}}-{{p.sex}}
        </li>
    </ul>
</div>
<script type="text/javascript">
    Vue.config.productionTip = false
    // //用watch实现
    // const vm = new Vue({
    //     el: '#root',
    //     data: {
    //         keyWord: '',
    //         persons: [
    //             {
    //                 id: '001',
    //                 name: '马冬梅',
    //                 age: 18,
    //                 sex: '女'
    //             },
    //             {
    //                 id: '002',
    //                 name: '周冬雨',
    //                 age: 18,
    //                 sex: '女'
    //             },
    //             {
    //                 id: '003',
    //                 name: '周杰伦',
    //                 age: 18,
    //                 sex: '男'
    //             },
    //             {
    //                 id: '004',
    //                 name: '温兆伦',
    //                 age: 18,
    //                 sex: '男'
    //             },
    //         ],
    //         finalPersons: [],
    //     },
    //     watch: {
    //         keyWord(val) {
    //             this.finalPersons = this.persons.filter((p) => {
    //                 return p.name.indexOf(val) !== -1
    //             })
    //         }
    //     }
    // })

    //用computed实现
    const vm = new Vue({
        el: '#root',
        data: {
            keyWord: '',
            persons: [
                {
                    id: '001',
                    name: '马冬梅',
                    age: 18,
                    sex: '女'
                },
                {
                    id: '002',
                    name: '周冬雨',
                    age: 18,
                    sex: '女'
                },
                {
                    id: '003',
                    name: '周杰伦',
                    age: 18,
                    sex: '男'
                },
                {
                    id: '004',
                    name: '温兆伦',
                    age: 18,
                    sex: '男'
                },
            ],
        },
        computed: {
            finalPersons() {
                return this.persons.filter((p) => {
                    return p.name.indexOf(this.keyWord) !== -1
                })
            }
        }
    })

</script>
</body>
</html>
```

列表排序：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>列表排序</title>
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
<div id="root">
    <h2>人员列表</h2>
    <input type="text" v-model="keyWord" placeholder="请输入名字">
    <button @click="sortType = 2">年龄升序</button>
    <button @click="sortType = 1">年龄降序</button>
    <button @click="sortType = 0">原顺序</button>
    <ul>
        <li v-for="(p,index) in finalPersons" :key="p.id">
            {{p.name}}-{{p.age}}-{{p.sex}}
        </li>
    </ul>
</div>
<script type="text/javascript">
    Vue.config.productionTip = false

    //用computed实现
    const vm = new Vue({
        el: '#root',
        data: {
            keyWord: '',
            sortType: 0,//0原顺序 1降序 2升序
            persons: [
                {
                    id: '001',
                    name: '马冬梅',
                    age: 30,
                    sex: '女'
                },
                {
                    id: '002',
                    name: '周冬雨',
                    age: 20,
                    sex: '女'
                },
                {
                    id: '003',
                    name: '周杰伦',
                    age: 23,
                    sex: '男'
                },
                {
                    id: '004',
                    name: '温兆伦',
                    age: 33,
                    sex: '男'
                },
            ],
        },
        computed: {
            finalPersons() {
                const arr = this.persons.filter((p) => {
                    return p.name.indexOf(this.keyWord) !== -1
                });
                if (this.sortType) {
                    arr.sort((p1, p2) => {
                        return this.sortType === 1 ? p2.age - p1.age : p1.age - p2.age
                    })
                }
                return arr;
            }
        }
    })

</script>
</body>
</html>
```

Vue监视数据原理：

1. Vue会监视data中所有层次的数据

2. 如何监测对象中的数据？

    - 通过setter实现监视，且要在new Vue时就传入要监测的数据

    - 对象中后追加的属性，Vue默认不作响应式处理

    - 如需给后添加的属性作响应式，请使用如下API：

        `Vue.set(target,propertyName/index,value)` 或 `vm.$set(target,propertyName/index,value)`

3. 如何监测数组中的数据？

    - 通过包裹数组更新元素的方法实现，本质就是做了两件事：
        1. 调用原生对应的方法对数组进行更新
        2. 重新解析模板，进而更新页面

4. 在Vue修改数组中某个元素一定要用如下方法：

    1. 使用这些API：push() pop() shift() splice() sort() reverse()
    2. Vue.set() 或 vm.$set()

特别注意：Vue.set() 和 vm.$set() 不能给vm 或vm的根数据对象添加属性！！！

### 1.12 收集表单数据

- \<input type="text"/>，则v-model收集的是value值，用户输入的就是value值
- \<input type="radio"/>，则v-model收集的是value值，且要给标签配置value
- \<input type="checkbox"/>
    - 没有配置input的value属性，那么收集的就是checked
    - 配置input的value属性：
        - v-model的初始值是非数组，那么收集的就是checked
        - v-model的初始值是数组，那么收集的就是value组成的数组

备注：v-model的三个修饰符：

- lazy：失去焦点再收集数据
- number：输入字符串转为有效的数字
- trim：输入首尾空格过滤

### 1.13 过滤器

定义：对要显示的数据进行特定格式化后再显示（适用于一些简单逻辑的处理）

语法：

1. 注册过滤器：Vue.filter(name.callback) 或 new Vue(filters:{})
2. 使用过滤器：{{xxx | 过滤器名}} 或 v-bind:属性="xxx | 过滤器名"

备注：

1. 过滤器也可以接收额外参数、多个过滤器也可以串联
2. 并没有改变原本的数据，是产生新的对应的数据

### 1.14 内置指令与自定义指令

```
学过的指令：
v-bind：单向绑定解析表达式，可以简写为 :xxx
v-model：双向数据绑定
v-for：遍历数组、对象、字符串
v-on：绑定事件监听，可以简写为 @
v-if：条件渲染（动态控制节点是否存在）
v-else：条件渲染（动态控制节点是否存在）
v-show：条件渲染（动态控制节点是否展示）
```

```
v-html指令：
1.作用：向指定节点中渲染包含HTML结构的内容
2.与插值语法的区别：
    1.v-html会替换掉节点中所有的内容，{{xxx}}则不会
    2.v-html可以识别html结构
3.严重注意：v-html有安全性问题
    1.在网站上动态渲染任意HTML是非常危险的，容易导致XSS攻击
    2.一定要在可信的内容上使用v-html，永远不要在用户提交的内容上
```

```
v-cloak指令（没有值）：
1.本质是一个特殊属性，Vue实例创建完毕并接管容器后，会删掉v-cloak属性
2.使用css配合v-cloak可以解决网速慢时页面展示出{{xxx}}的问题
```

```
v-once指令：
1.v-once所在节点在初次动态渲染后，就视为静态内容了
2.以后数据的改变不会引起v-once所在结构的更新，可以用于优化性能
```

```
v-pre指令：
1.跳过其所在节点的编译过程
2.可利用它跳过：没有使用指令语法、没有使用插值语法的节点，会加快编译
```

自定义组件：

```
需求1：定义一个v-big指令，和v-text功能类似，但会把绑定的数值放大10倍
需求2：定义一个v-fbind指令，和v-bind功能类似，但可以让其所绑定的input元素默认获取焦点
自定义指令总结：
1.定义语法：
    1.局部指令：
    new Vue({
        directives:{指令名:配置对象}
    })
    或
    new Vue({
        directives(){}
    })
    2.全局指令：
    Vue.directive(指令名,配置对象) 或 Vue.directive(指令名,回调函数)
2.配置对象中常用的3个问题：
    1..bind 指令与元素成功绑定时使用
    2..inserted 指令所在元素被插入页面时调用
    3.。update 指令所在模板结构被重新解析时调用
3.备注：
    1.指令定义时不加v-，但使用时要加v-
    2.指令名如果是多个单词，要是用kebab-case命名方式，不要用camelCase命名
```

### 1.15 Vue实例生命周期

生命周期，又名：生命周期回调函数、生命周期函数、生命周期钩子

- 是什么：Vue在关键时刻帮我们调用的一些特殊名称的函数

- 生命周期函数的名字不可更改，但函数的具体内容是程序员根据需求编写的

- 生命周期函数中的this指向是vm 或 组件实例对象

常用的生命周期钩子：

1. mounted：发送Ajax请求、启动定时器、绑定自定义事件、订阅消息等【初始化操作】
2. beforeDestroy：清除定时器、解绑自定义事件、取消订阅消息等【收尾工作】

关于销毁Vue实例：

1. 销毁后借助Vue开发者工具看不到任何信息
2. 销毁后自定义事件会失败，但原生DOM事件依然有效
3. 一般不会在beforeDestroy操作数据，因为即便操作数据，也不会再触发更新流程了

## 2. Vue组件化编程

基本使用：

几个注意点：

```
1.关于组件名：
    一个单词组成：
    1.首字母小写：school
    2.首字母大写：School
    多个单词组成：
    1.kebab-case命名：my-school
    2.CamelCase命名：MySchool 需要Vue脚手架支持
    备注：
    1.组件名尽可能回避HTML中已有的元素名称，例如：h2 H2都不行
    2.可以使用name配置项指定组件在开发者工具中呈现的名字
2.关于组件标签：
    1.<school></school>
    2.<school/>
3.一个简写方式：
    const school = Vue.extend(options) 可以简写为：const school = options
```

关于VueComponent：

```
1.school组件本质是一个名为VueComponent的构造函数，且不是程序员定义的，是Vue.extend生成的
2.我们只需要写<school/>或<school></school>，Vue解析时会帮我们创建school组件的实例对象，
即Vue帮我们执行的：new VueComponent(options)
3.特别注意：每次调用Vue.extend，返回的都是一个全新的VueComponent
4.关于this指向：
    1.组件配置中：
    data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【VueComponent实例对象】
    2..new Vue(options)配置中：
    data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【Vue实例对象】
5.VueComponent的实例对象，以后简称vc（也可以称之为：组件实例对象）
Vue的实例对象，以后简称vm
```

一个重要的内置关系：`VueComponent.prototype.\_\_proto\_\_ === Vue.prototype`

为什么要有这个关系：让组件实例对象（vc）可以访问到Vue原型上的属性、方法

## 3. 使用Vue脚手架

### 3.1 脚手架文件结构

- node_modules
- public
    - favicon.ico  页签图标
    - **index.html  主页面**
- src
    - asstes  存放静态资源
        - logo.png
    - **components  存放组件**
        - HelloWorld.vue
    - **App.vue  汇总所有组件**
    - **main.js  入口文件**
- .gitignore
- babel.config.js  babel的配置文件
- package.json  应用包配置文件
- package-lock.json  包版本控制文件
- README.md

### 3.2 关于不同版本的Vue

vue.js与vue.runtime.xxx.js的区别：

1. vue.js是完整版的Vue，包含：核心功能+模板解析器
2. vue.runtime.xxx.js是运行版的Vue，只包含核心功能，没有模板解析器

因为vue.runtime.xxx.js没有模板解析器，所以不能使用template配置项，需要使用render函数接收到的createElement函数去指定具体内容

### 3.3 vue.config.js配置文件

1. 使用`vue inspect > output.js` 可以查看到vue脚手架的默认配置
2. 使用vue.config.js可以对脚手架进行个性化定制，详情见：See [Configuration Reference](https://cli.vuejs.org/config/).

### 3.4 ref属性

1. 被用来给元素或子组件注册引用信息，id的替代者
2. 应用在HTML标签上获取的是真实的DOM元素，应用在组件标签上是组件实例对象
3. 使用方式：
    1. 打标识：<h1 ref="xxx">sssss</h1> 或 <School ref="xxx">sssss</School>
    2. 获取：this.$refs.xxx

### 3.5 配置项props

功能：让组件接收外部传过来的数据

1. 传递数据：<Demo name="xxx"></Demo>

2. 接收数据：

    ```javascript
    //1.只接收
    props:  ['name']
    
    //2.限制类型
    props: [
        name: Number
    ]
    
    //3.限制类型、限制必要性、指定默认值
    props: {
        name: {
          type: String,
          require: true
        },
        sex: {
          type: String,
          require: true
        },
        age: {
          type: Number,
          default: 99
        }
    }
    ```

    备注：props是只读的，Vue底层会监测你对props的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制props的内容到data中一份，然后去修改data中的数据。


### 3.6 Mixin（混入）

功能：可以把多个组件共用的配置提取成一个混入对象

使用方式：

```javascript
//1.定义混合
{
    data() {...},
    methods: {...},
    ...
}
//2.使用混入
//全局混入
    Vue.mixin(xxx)
//局部混入
    mixins: ['xxx']
```


### 3.7 插件

功能：用于增强Vue

本质：包含install方法的一个对象，install的第一个参数是Vue，第二个以后的参数是插件使用者传递的数据

定义插件：

```javascript
对象.install = function (Vue,options) {
    //添加全局过滤器
    //添加全局指令
    //配置全局混入
    //添加实例方法
}
```

使用插件：`Vue.use()`

### 3.8 scoped样式

作用：让样式在局部生效，防止冲突

写法：\<style scoped\>

### 3.9 总结TodoList案例

组件化编码流程：

1. 拆分静态组件：组件要按照功能点拆分，命名不要与HTML元素冲突
2. 实现动态组件：考虑好数据的存放位置，数据是一个组件在用，还是一些组件在用。
    1. 一个组件在用：放在自身即可
    2. 一些组件在用：放在他们共同的父组件上（**状态提升**）
3. 实现交互：从绑定事件开始

props适用于：

1. 父组件==>子组件 通信
2. 子组件==>父组件 通信，要求父组件给子组件一个函数

使用v-model时要切记：v-model绑定的值不能是props传过来的值，因为props是不可以修改的。

props传过来的若是对象类型的值，修改对象中的属性时Vue不会报错，但不推荐这样做。

### 3.10 WebStorage

1. 存储内容大小一般支持5MB左右，不同浏览器可能不一样

2. 浏览器端通过windows.sessionStorage 和 Windows.localStorage 属性来实现本地存储机制

3. 相关API

    ```js
    //该方法接受一个键和值作为参数，会把键值对添加到存储中，如果键名存在，则更新其对应值
    xxxxxStorage.setItem('key','value')
    
    //该方法接受一个键名作为参数，返回键名对应的值
    xxxxxStorage.getItem('key')
    
    //该方法接受一个键名作为参数，并把该键名从存储中删除
    xxxxxStorage.removeItem('key')
    
    //该方法会清空存储中所有的数据
    xxxxxStorage.clear()
    
    ```

4. 备注：

    1. SessionStorage存储的内容会随着浏览器窗口的关闭而消失
    2. LocalStorage存储的内容，需要手动清除才会消失
    3. xxxxxStorage.getItem('key')如果获取不到，返回null
    4. JSON.parse(null)的结果依然是null

### 3.11 组件自定义事件

1. 一种组件间通信的方式，适用于：**子组件==>父组件**

2. 使用场景：A是父组件，B是子组件，B想给A传数据，那么就要在A中给B绑定自定义事件（**事件的回调在A中**）

3. 绑定自定义事件：

    1. 方式1：在父组件中： <Demo @atguigu="test" /> 或  <Demo v-on:atguigu="test" />

    2. 方式2：在父组件中

        ```js
        <Demo ref="demo"/>
        ...
        mounted() {
            this.$refs.xxx$on('atguigu',this.test)
        }
        ```

4. 触发自定义事件：this.$emit('atguigu',数据)

5. 解绑自定义事件：this.$off('atguigu')

6. 组件上也可以绑定原生DOM事件，需要使用 native 修饰符

7. 注意：通过this.$refs.xxx$on('atguigu',回调)绑定自定义事件时，回调**要么配置在methods，要么用箭头函数**，否则this指向会出现问题！

### 3.12 全局事件总线（GlobalEventBus）

1. 一种组件间通信的方式，适用于**任意组件间通信**

2. 安装全局事件总线：

    ```js
    new Vue({
        ...
        beforeCreate() {
        	//安装全局事件总线，$bus就是当前应用的vm
        	Vue.prototype.$bus = this
    	}
        ...
    })
    ```

3. 使用事件总线：

    1. 接收数据：A组件想接收数据，则在A组件中给$bus绑定自定义事件，事件的**回调留在A组件自身。**

        ```js
        methods(){
            demo(data){...}
        }
        ...
        mounted(){
            this.$bus.$on('xxxx',this.demo)
        }
        ```

    2. 提供数据：this.$bus.$emit('xxxx',数据)

4. 最好在beforeDestroy钩子中，用$off去解绑**当前组件所用到的**事件

### 3.13 消息订阅与发布（pubsub）

1. 一种组件间通信的方式，适用于**任意组件间通信**

2. 使用步骤：

    1. 安装pubsub：npm i pubsub-js

    2. 引入：import pubsub from 'pubsub-js'

    3. 接收数据：A组件想接收数据，则在A组件中订阅消息，订阅的回调留在A组件自身

        ```js
        methods(){
            demo(data){...}
        }
        ...
        mounted(){
            //订阅消息
        	this.pid = pubsub.subscribe('xxx',this.demo)
        }
        ```

    4. 提供数据：pubsub.publish('xxx',数据)

    5. 最好在beforeDestroy钩子中，用Pubsub.unsubscribe(pid)去**取消订阅**

### 3.14 nextTick

1. 语法：this.$nextTick(回调函数)
2. 作用：在下一次DOM更新结束后执行其指定的回调
3. 什么时候用：当改变数据后，要基于更新后的新的DOM进行某些操作时，要在nextTick所指定的回调函数中执行

### 3.15 Vue封装的过渡和动画

1. 作用：在插入、更新或移除DOM元素时，在合适的时候给元素添加样式类名

2. 图示：

     

3. 写法：

    1. 准备好样式：

        - 元素接入样式：
            - v-enter：进入的起点
            - v-enter-active：进入过程中
            - v-enter-to：进入的终点

        - 元素离开样式：
            - v-leave：离开的起点
            - v-leave-active：离开过程中
            - v-leave-to：离开的终点

    2. 使用\<transition\>包裹要过渡的元素，并配置name属性：

        ```html
        <transition name="hello">
        	<h1 v-show="isShow">hello</h1>
        </transition>
        ```

    3. 备注：若有多个元素需要过渡，则需要使用：<transition-group>，且每个元素都要指定key值

## 4. Vue中的Ajax

### 4.1 Vue脚手架配置代理

方式一：

在vue.config.js中添加如下配置：

```js
devServer: {
	proxy: "http://localhost:5000"
}
```

说明：

1. 优点：配置简单，请求资源时直接发给前端8080即可
2. 缺点：不能配置多个代理，不能灵活的控制请求是否走代理
3. 工作方式：若按照上述配置代理，当请求了前端不存在的资源时，那么该请求会转发给服务器（优先匹配前端资源）

方式二：

编写vue.config.js配置具体代理规则：

```js
module.exports = {
    devServer: {
        proxy: {
            //匹配所有以'/api1'开头的请求路径
            'api1': {
                //代理目标的基础路径
                target: 'http://localhost:5000',
                changeOrigin: true,
                pathRewite: {'^/api1': ''}
            },
            //匹配所有以'/api2'开头的请求路径
            'api2': {
                //代理目标的基础路径
                target: 'http://localhost:5001',
                changeOrigin: true,
                pathRewite: {'^/api2': ''}
            },
        }
    }
}
```

说明：

1. 优点：可以配置多个代理，且可以灵活的控制请求是否走代理
2. 缺点：配置略微繁琐，请求资源时必须加前缀

### 4.2 插槽

作用：让父组件可以向子组件指定位置插入HTML结构，也是一种组件间通信的方式，适用于**父组件==>子组件**

分类：默认插槽、具名插槽、作用域插槽

使用方式：

1. 默认插槽

    ```vue
    //父组件中
    <Category>
    	<div>
            html结构
        </div>
    </Category>
    //子组件中
    <template>
    	<div>
            //定义插槽内容
            <slot>插槽默认内容</slot>
        </div>
    </template>
    ```

2. 具名插槽

    ```vue
    //父组件中
    <Category>
        <template slot="center">
            <div>
                html结构
            </div>
        </template>
        <template slot:footer>
            <div>
                html结构
            </div>
        </template>
    </Category>
    //子组件中
    <template>
    	<div>
            //定义插槽内容
            <slot name="center">插槽默认内容</slot>
            <slot name="footer">插槽默认内容</slot>
        </div>
    </template>
    ```

3. 作用域插槽

    **数据在组件的自身，但根据数据生成的结构需要组件的使用者决定**

    ```vue
    //父组件中
    <Category>
        <template scope="scopeData">
            <ul>
                <li v-for="g in scopeDate.games">{{g}}</li>
            </ul>
        </template>
        <template scope="scopeData">
            <h4 v-for="g in scopeDate.games">{{g}}</h4>
        </template>
    </Category>
    //子组件中
    <template>
    	<div>
            <slot :games="games"></slot>
        </div>
    </template>
    <script>
    	export default {
            name: 'Category',
            props: ['title'],
            //数据在子组件自身
            data() {
                return {
                    games: ['实况足球','荒野乱斗']
                }
            }
        }
    </script>
    ```


## 5. vuex

### 5.1 概念

在Vue中实现集中式状态（数据）管理的一个Vue插件，对Vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信。

何时使用

多个组件需要共享数据时

搭建vuex环境

1. 创建文件： `src/store/index.js`

    ```js
    //引入Vue核心库
    import Vue from 'vue'
    //引入Vuex
    import Vuex from 'vuex'
    //应用Vuex插件
    Vue.use(Vuex)
    
    //准备actions对象 响应组件中用户的动作
    const actions = {}
    //准备mutations对象 修改state中的数据
    const mutaions = {}
    //准备state对象 保存具体的数据
    const state = {}
    
    //创建并暴露store
    export default new Vuex.Store({
        actions,
        mutations,
        state
    })
    ```

2. 在 `main.js` 中创建vm 时传入`store`配置项

    ```js
    ...
    //引入store
    import store from './store'
    ...
    
    //创建vm
    new Vue({
    	el: '#app',
    	render: h => h(App),
    	store
    })
    ```


### 5.2 基本使用

1. 初始化数据、配置actions、配置mutations、操作文件store.js

    ```js
    //引入Vue核心库
    import Vue from 'vue'
    //引入Vuex
    import Vuex from 'vuex'
    //应用Vuex插件
    Vue.use(Vuex)
    
    //准备actions对象 响应组件中用户的动作
    const actions = {
        jia(context,vaule) {
            context.commit('JIA',value)
        }
    }
    //准备mutations对象 修改state中的数据
    const mutaions = {
        JIA(state,vaule) {
            state.sum += value
        }
    }
    //准备state对象 保存具体的数据
    const state = {
        sum: 0
    }
    
    //创建并暴露store
    export default new Vuex.Store({
        actions,
        mutations,
        state
    })
    ```

2. 组件中读取vuex中的数据：`$store.state.sum`

3. 组件中修改vuex中的数据：`$store.dispatch('action中的方法名',数据)`或`$store.commit('mutaions中的方法名',数据)`

> 备注：若没有网络请求或其他业务逻辑，组件中也可以越过actions，即不写dispatch，直接写commit

### 5.3 getters的使用

概念：当state中数据需要经过加工后再使用时，可以使用getters加工

使用：

1. 在store.js中追加getters配置：

    ```js
    ...
    const getters = {
    	bigSum(state) {
    		return state.sum * 10
    	}
    }
    
    //创建并暴露store
    export default new Vuex.Store({
        ...
        getters
    })
    ```

2. 组件中读取数据：`$store.getters.bigSum`

### 5.4 四个map方法的使用

1. mapState方法：用于帮助我们映射state中的数据为计算属性

    ```js
    computed: {
        //借助mapState生成计算属性：sum、school、subject（对象写法）
        ...mapState({sum:'sum',school:'school',subject:'subject'}),
            
        //借助mapState生成计算属性：sum、school、subject（数组写法）
        ...mapState(['sum','school','subject']),
    }
    ```

2. mapGetters方法：用于帮助我们映射getters中的数据为计算属性

    ```js
    computed: {
        //借助mapGetters生成计算属性：bigSum（对象写法）
        ...mapGetters({bigSum:'bigSum'}),
            
        //借助mapGetters生成计算属性：sum、school、subject（数组写法）
        ...mapGetters(['bigSum']),
    }
    ```

3. mapActions方法：用于帮助我们生成与actions对话的方法，即包含`$store.dispatch(xxx)`的函数

    ```js
    computed: {
        //靠mapActions生成：incrementOdd、incrementWait（对象写法）
        ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'}),
            
        //靠mapActions生成：incrementOdd、incrementWait（数组写法）
        ...mapActions(['jiaOdd','jiaWait']),
    }
    ```

4. mapMutations方法：用于帮助我们生成与mutations对话的方法，即包含`$store.commit(xxx)`的函数

    ```js
    computed: {
        //靠mapMutations生成：incrementOdd、incrementWait（对象写法）
        ...mapMutations({increment:'JIA',decrement:'JIAN'}),
            
        //靠mapMutations生成：JIA、JIAN（数组写法）
        ...mapMutations(['JIA','JIAN']),
    }
    ```

> 备注：mapActions与mapMutations使用时，若需要传递参数需要：在模板中绑定事件时传递好参数，否则参数是事件对象

### 5.5. 模块化+命名空间

目的：让代码更好维护，让多种数据分类更加明确

1. 修改`store.js`

```js
const countAbout = {
    //开启命名空间
	namespaced: true,
    state:{x:1},
    mutations: {},
    actions: {},
    getters:{
        bigSum(state){
            return state.sum*10
        }
    }
}

const personAbout = {
    //开启命名空间
	namespaced: true,
    state:{x:1},
    mutations: {},
    actions: {},
}

const store = nre Vuex.Store({
    modules: {
        countAbout,
        personAbout
	}
})
```

2. 开启命名空间后，组件中读取state数据：

    ```js
    //方式一：自己直接读取
    this.$store.state.personAbout.list
    //方式二：借助mapState读取
    ...mapState('countAbout',['sum','school','subject'])
    ```

3. 开启命名空间后，组件中读取getters数据：

    ```js
    //方式一：自己直接读取
    this.$store.getters['personAbout/firstPersonName']
    //方式二：借助mapState读取
    ...mapGetters('countAbout',['bigSum'])
    ```

4. 开启命名空间后，组件中调用dispatch：

    ```js
    //方式一：自己直接dispatch
    this.$store.dispatch('personAbout/addPersonWang',person)
    //方式二：借助mapActions读取
    ...mapActions('countAbout',{incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
    ```

5. 开启命名空间后，组件中调用commit：

    ```js
    //方式一：自己直接commit
    this.$store.dispatch('personAbout/ADD_PERSON',person)
    //方式二：借助mapMutations读取
    ...mapMutations('countAbout',{increment:'JIA',decrement:'JIAN'})
    ```



## 6. vue-router

### 6.1 概念

1. 一个路由（route）就是一组映射关系（key-value），多个路由需要路由器（router）进行管理

2. 前端路由：key是路径，value是组件

### 6.2 基本使用

1. 安装vue-router，命令：`npm i vue-router`

2. 应用插件：`Vue.use(VueRouter)`

3. 编写router配置项

    ```js
    //引入VueRouter
    import VueRouter from 'vue-router'
    //引入路由组件
    import About from '../components/About'
    import Home from '../components/Home'
    
    //创建router实例对象，去管理一组一组的路由规则
    const router = new VueRouter({
        routes:[
            {
                path: '/about',
                component:About
            },
            {
                path: '/home',
                component:Home
            }
        ]
    })
    
    //暴露router
    export default router
    ```

4. 实现切换

    ```html
    <router-link active-class="active" to="/about">About</router-link>
    ```

5. 指定展示位置

    ```html
    <router-view></router-view>
    ```


### 6.3 几个注意点

1. 路由组件通常存放在`pages` 文件夹，一般杜建放在`component`文件夹
2. 通过切换，“隐藏”了的路由组件，默认是被销毁的，需要的时候再去挂载
3. 每个组件都有自己的`$route`属性，里面存储着自己的路由信息
4. 整个应用只有一个`router`，可以通过组件的`$router`属性获取到

### 6.4 多级路由

1. 配置路由规则，使用children配置项：

    ```js
    const router = new VueRouter({
        routes:[
            {
                path: '/about',
                component:About
            },
            {
                path: '/home',
                component:Home,
                children:[
                    {
                        path:'news',
                        component:News
                    },
                    {
                        path:'message',
                        component:Message
                    }
                ]
            }
        ]
    })
    ```

    

2. 跳转，要写完整路径

    ```html
    <router-link to="/hone/news"></router-link>
    ```

### 6.5 路由的query参数

1. 传递参数

    ```html
    <!-- 跳转并携带query参数，to的字符串写法 -->
    <router-link to="/hone/message/detail?id=666&title=你好">跳转</router-link>
    
    <!-- 跳转并携带query参数，to的对象写法 -->
    <router-link :to="{
                      path:'home/message/detail',
                      query:{
                      id:666,
                      title:'你好'
                      }
                      }">跳转</router-link>
    ```

2. 接收参数

    ```js
    $route.query.id
    $route.query.title
    ```

### 6.6 命名路由

作用：可以简化路由的跳转

使用流程：

1. 给路由命名

    ```js
    const router = new VueRouter({
        routes:[
            {
                path: '/demo',
                component:Demo
            },
            {
                path: '/test',
                component:Test,
                children:[
                    {
                        //给路由命名
                        name:'hello',
                        path:'welcom',
                        component:Hello
                    }
                ]
            }
        ]
    })
    ```

2. 简化跳转

    ```html
    <!-- 简化前，需要写完整路径 -->
    <router-link to="/demo/test/welcome">跳转</router-link>
    
    <!-- 简化后，直接通过名字跳转 -->
    <router-link to="{name:'hello'}">跳转</router-link>
    
    <!-- 简写写法配合传递参数 -->
    <router-link :to="{
                      name:'hello',
                      query:{
                      id:666,
                      title:'你好'
                      }
                      }">跳转</router-link>
    ```

### 6.7 路由的params参数

1. 配置路由，声明接收params参数

    ```js
    const router = new VueRouter({
        routes:[
            {
                path: '/home',
                component:Home
            },
            {
                path: '/news',
                component:News,
                children:[
                    {
                        name:'xiaogqing',
                        //使用占位符声明接收params参数
                        path:'detail/:id/:title',
                        component:Detail
                    }
                ]
            }
        ]
    })
    ```

2. 传递参数

    ```html
    <!-- 跳转并携带params参数，to的字符串写法 -->
    <router-link to="/hone/message/detail/666/你好">跳转</router-link>
    
    <!-- 跳转并携带params参数，to的对象写法 -->
    <router-link :to="{
                      name:'xiangqing',
                      params:{
                      id:666,
                      title:'你好'
                      }
                      }">跳转</router-link>
    ```

    > 注意：路由携带params参数时，若使用to的对象写法，则不能使用path配置项，必须使用name配置

3. 接收参数

    ```
    $route.params.id
    $route.params.title
    ```

### 6.8 路由的props配置

作用：让路由组件更方便的收到参数

```js
{
    name:'xiangqing',
    path:'detail/:id',
    component:Detail,
        
    //第一种写法：props值为对象，该对象中所有的key-value的组合最终都会通过props传给Detail组件
    //props:{a:900}
    
    //第一种写法：props值为布尔值，为true时则把路由收到的所有params参数通过props传给Detail组件
    //props:true
        
    //第三种写法：props值为函数，该函数返回的对象中每一组key-value都会通过props传给Detail组件
        
    props(route){
        return {
            id:route.query.id,
            title:route.query.title
        }
    }
}
```

### 6.9 \<router-link\>的replace属性

作用：控制路由跳转时操作浏览器历史记录的模式

- 浏览器的历史记录有两种写入方式：分别是push和replace，push是追加历史记录，replace是替换当前记录，路由跳转时候默认为push

- 如何开启replace模式：`<router-link replace ...>News</router-link>`

### 6.10 编程式路由导航

作用：不借助`<router-link>`实现路由跳转，让路由跳转更加灵活

具体编码：

```js
//$router的两个API
this.$router.push({
    name:'xiangqing',
    params:{
        id:xxx,
        title:xxx
    }
})

this.$router.replace({
    name:'xiangqing',
    params:{
        id:xxx,
        title:xxx
    }
})
```

### 6.11 缓存路由组件

作用：让不展示的路由组件保持挂载，不被销毁

具体编码：

```html
<keep-alive include="News">
	<router-view></router-view>
</keep-alive>
```

### 6.12 两个新的生命周期钩子

作用：路由组件所独有的两个钩子，用于捕获路由组件的激活状态

具体：

1. `activated` 路由组件被激活时触发
2. `deactivated` 路由组件失活时触发

### 6.13 路由守卫

作用：对路由进行权限控制

分类：全局守卫、独享守卫、组件内守卫

全局守卫：

```js
//全局前置守卫，初始化时执行，每次路由切换前执行
router.beforeEach((to,from,next)=>{
    //判断当前路由是否需要进行权限控制
    if(to.meta.isAuth){
        //权限控制的具体规则
        if(localStorage.getItem('school')==='atguigu'){
            //放行
            next()
        }else{
            alert('暂无权限查看')
        }
    }else {
        //放行
        next()
    }
})

//全局后置守卫，初始化时执行，每次路由切换后执行
router.afterEach((to,from)=>{
    if(to.meta.title){
        //修改网页的title
        document.title = to.meta.title
    }else {
        document.title = 'vue_test'
    }
})
```

独享守卫：

```js
beforeEnter((to,from,next)=>{
    if(to.meta.isAuth){
        if(localStorage.getItem('school')==='atguigu'){
            next()
        }else{
            alert('暂无权限查看')
        }
    }else {
        next()
    }
})
```

组件内守卫：

```js
//进入守卫：通过路由规则，进入该组件时被调用
beforeRouteEnter(to,from,next){},
//离开守卫：通过路由规则，离开该组件时被调用
beforeRouteLeave(to,from,next){},
```

### 6.14 路由器的两种工作模式

1. 对于一个URL来说，什么是hash值？

    #及其后面的内容就是hash值

2. hash值不会包含在HTTP请求中，即hash值不会带给服务器

3. hash模式：

    1. 地址中永远带着#号，不美观
    2. 若以后将地址通过第三方收集APP分享，若APP校验严格，则地址会被标记为不合法
    3. 兼容性较好

4. history模式：

    1. 地址干净、美观
    2. 兼容性和hash相比略差
    3. 应用部署上线时需要后端人员支持，解决刷新页面服务器404的问题

## 7. Vue UI 组件库

移动端常用 UI 组件库

1. Vant https://youzan.github.io/vant
2. Cube UI https://didi.github.io/cube-ui
3. Mint UI http://mint-ui.github.io

PC 端常用 UI 组件库

1. Element UI https://element.eleme.cn
2. IView UI https://www.iviewui.com