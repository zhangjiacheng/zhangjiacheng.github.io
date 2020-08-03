---
layout: post
title: "javascript常见问题和编程规范"
date: 2018-10-04 
description: "FAQ and conventions of javascript"
tag: javascript
---   

*注：本文主要针对Weex开发中的javascript*

## 使用ES5还是ES6?

答：ES6, 具体来讲我们尽量使用ES6的语法及特性。

*1.* 使用let和const分别定义变量和常量（能够使用const就用const，否则使用let），统一不再使用var。

    //var是函数作用域(function scope),let 是块作用域(block scope).
    //---猜猜下面这段代码输出什么？---
    var x = 0;
    function testFunc(isTrue) {
        if(isTrue) {
            var x = 1;
            return x;
        }
        return x;
    }
    testFunc(false);

*2.* 使用关键字class而非构造函数来定义类，class只是在写法上带来了许多便利(syntax sugar),本质上javascript的类机制没有变化，即javascript类还是prototype based。  

    //下面分别用ES5和ES6来实现类的继承

    //----ES5----
    function A(propertyA) {
       this.propertyA = propertyA;
    }
    A.prototype.getPropertyA = function() {
       return this.propertyA;
    };
    A.prototype.setPropertyA = function(pa) {
       this.propertyA = pa;
    }

    function B(propertyA, propertyB) {
        A.call(this, propertyA);
        this.propertyB = propertyB;
    }
    B.prototype.getPropertyB = function(){return this.propertyB;};
    B.prototype = Object.create(A.prototype);
    B.prototype.constructor = B;

    //----ES6----
    class A {
       constructor(propertyA) {
          this.propertyA = propertyA;
       }
    
       getPropertyA() {
          return this.propertyA;
       }
       setPropertyA(pa) {
          this.propertyA = pa;
       }
    } 

    class B extends A {
       constructor(propertyA, propertyB) {
          super(propertyA);
          this.propertyB = propertyB;
        }
        getPropertyB() {
           return this.propertyB;
        }
    }

    //使用上是一致的，结果页是一样的，但很显然ES6的写法要简单很多。
    var b = new B('propertyA', 'propertyB');
    console.log('B.propertyB = ', b.propertyB);
    console.log("b.hasOwnProperty('propertyB')", b.hasOwnProperty('propertyB'));

    console.log('B.propertyA = ', b.propertyA);
    console.log("b.hasOwnProperty('propertyA')", b.hasOwnProperty('propertyA'));
    console.log('B.pPrototypeA = ', b.pPrototypeA);
    console.log("b.hasOwnProperty('pPrototypeA')", b.hasOwnProperty('pPrototypeA'));

    console.log('Object.getPrototypeOf(b)  = ', (Object.getPrototypeOf(b) === B.prototype) );
    console.log('b instanceof B = ',b instanceof B);
    console.log('b instanceof A = ',b instanceof A);
    b.setPropertyA('setA');
    console.log('b.getPropertyA() = ',b.getPropertyA());
    console.log("b.hasOwnProperty('getPropertyA')", b.hasOwnProperty('getPropertyA'));

*3.* 使用string template拼接字符串。

*4.* 使用箭头函数。

*5.* ES6的其他新语法和特性。

## 函数命名及变量命名规范

javascript中函数(function)有三种角色，分别为：

1. 类构造函数
2. 类的成员方法
3. 一般的普通的函数

我们编程中出了类构造函数的命名使用大写开头，其他两种使用小写开头。
*注：我们项目中插件的暴露函数是普通函数，应该小写开头。但考虑到我们现有的插件都使用了大写开头，所以我们还是继续使用大写开头命名插件函数。*

函数和变量命名：
-  使用驼峰法命名
-  使用英文
-  达意，可以适当长一点
-  不能直接看出命名意思的，写好注释

## javascript的异步编程规范

javascript是单线程的，为了不被执行时间长的任务卡死，javascript提供了几种异步编程的模式（也就把执行时间长的任务异步执行，执行完成后告知结果）。

*1.* 回调函数(callBack)

*2.* 注册事件(Event)

    <!----下面这个网页演示了不使用异步等待，UI将会卡死---->
    <html>
    <body>
    <a id="block" href="">Block for 5 seconds</a>
    <button>This is a button</button>
    <div id="statusMessage"></div>
    <script>
      document.getElementById('block').addEventListener('click',onClick);
      function onClick(event) {
         event.preventDefault();
         setStatusMessage('Blocking...');
         // Call setTimeout(), so that browser has time to display
         // status message
         setTimeout(function () {
            sleep(5000);
            setStatusMessage('Done');
         }, 0);
       }
       function setStatusMessage(msg) {
          document.getElementById('statusMessage').textContent = msg;
       }
       function sleep(milliseconds) {
          let start = Date.now();
          while ((Date.now() - start) < milliseconds);
       }
    </script>
    </body>
    </html>

    //--- javascript异步编程：回调函数(callBack)例子----
    setTimeout( () => {console.log("异步等待结束");}, 500);

    //--- javascript异步编程：注册事件(Event)例子----
    let req = new XMLHttpRequest();
    req.open("GET", "https://exeutest.blob.core.chinacloudapi.cn/app/dtsversion.json");
    req.onload = function(){
       if(req.status === 200) {
          console.log(`request success, result = ${req.response}`);
        }
       else {
          console.log(`Error : ${req.statusText}`);
       }
    };
    req.onerror = function(){
       console.log("request onerror networkError");
    }
    req.send();

javascript异步编程我们项目中实际使用最多的是回调函数，但回调函数有下面几个缺点：

- 多个异步任务嵌套容易陷入回调地狱(callback hell)，也即一层套一层的回调。
- 回调函数状态不能保持，回调一次后再不能回调了。

ES6把异步编程的Promise纳入规范，解决了回调函数的几个缺点。Promise有下面几个特点：

- 回调函数和注册事件的异步函数都可以改造成Promise。
- Promise其实是回调函数的包装，Promise里的异步任务也只会执行一次。
- Promise有三种状态：
  1. 未开始(pending)
  2. 已完成(fulfilled)
  3. 已拒绝(rejected)
- Promise通过扁平的方式实现依次逐个执行异步任务，解决回调函数的回调地狱的问题。
- Promise一旦完成，它的状态就保持不动，即：Promise是一个装异步任务执行结果的容器。 

Promise的使用例子见，项目中的distanceUtil.js。
这个例子演示了回调函数和Promise的混用

    import { YtQuerySQL } from "../plugins/DbPlugins"
    import { YtGetUserInfo } from "../plugins/UserInfoPlugins"
    export class YtDistanceUtil {
        constructor() {
            this.userId = undefined;
            this.profileId = undefined;
            this.orgId = undefined;
            this.distance = 150; //the ditance we want
            this.getDistance();
        }
        getDistance() {
            //console.log(`getDistance -> this.userId -> ${this.userId}`);
            YtGetUserInfo( (err, ret) => {
			    if(err === null || err === undefined) { 
				    //console.log(`get userid=${ret.userId} ; userName=${ret.userName} ; orgId=${ret.orgId}; profileId=${ret.profileId}`);
				    this.userId = ret.userId;
				    this.orgId = ret.orgId;
                    this.profileId = ret.profileId;
                    console.log(`getDistance -> this.userId -> ${this.userId}`);
                
                    step1t2(this.userId)
                    .then(result => {

                        if(result !== 0) {
                            console.log(`then0 -> result -> ${result}`);
                            //this.distance = result;
                            return result;
                        }
                        else {
                            console.log(`then0 -> this.then1`);
                            return step1t2(this.profileId);
                        }
                    
                     } )
                     .then(result => {
                        //resultFinal = result;
                        if(result !== 0) {
                            console.log(`then1 -> result -> ${result}`);
                            //this.distance = result;
                            return result;
                        }
                        else {
                            console.log(`then1 -> this.then2`);
                            return step3(this.orgId);
                        }
                    })
                    .then(result => {
                        //resultFinal = result;
                        if(result !== 0) {
                            console.log(`then2 -> result -> ${result}`);
                            this.distance = result;
                            console.log(`then2 -> this.distance -> ${this.distance}`);
                        }
                        else {
                            console.log(`then2 -> 0 -> 150`);
                            this.distance = 150;
                        }

                    });
			    }
		    });
        
        }
    }

    function step1t2(param) {
    
        return new Promise( (resolve, reject) => {
        
            console.log(`step1t2 -> param -> ${param}`);
            let opt={
                isUserDB:"1",
                tableName:"ONTAP__OnTapSettings__c",
                keyFiled:["ONTAP__checkindistance_threshold__c"],
                whereKey:["SetupOwnerId"],
                whereValue:[param]
            };
            YtQuerySQL(opt, function (ret) {
		    	ret = ret || {};
		    	if (ret.error) {
                    console.log(`step1t2 -> ret.error -> ${ret.error}`);
                    resolve(0);
                } 
                else {
                    console.log(`step1t2 -> ret.success -> ${ret.success}`);
                    if( (ret.success !== undefined) && (ret.success.length>0) )  {
                        let item= ret.success[0];
                        resolve(parseInt(item.ONTAP__checkindistance_threshold__c))
                    }
                    else {
                        resolve(0);
                    }
                }
            }); 
        });
    }

    function step3(param) {
    
        return new Promise( (resolve, reject) => {

            console.log(`step3 -> param -> ${param}`);
            let opt={
                isUserDB:"1",
                tableName:"ONTAP__OnTapSettings__c",
                keyFiled:["ONTAP__checkindistance_threshold__c"],
                whereKey:["SetupOwnerId"],
                whereValue:[param]
            };
            YtQuerySQL(opt, function (ret) {
			    ret = ret || {};
			    if (ret.error) {
                    resolve(0);
                } 
                else {
                    if( (ret.success !== undefined) && (ret.success.length>0) ) {
                        let item= ret.success[0];
                        resolve(parseInt(item.ONTAP__checkindistance_threshold__c))
                    }
                    else {
                        resolve(150);
                    }
                }
            }); 
        });
    }



## vue文件里面javascript逻辑代码编程规范

1. vue文件里面的javascript代码应该力求简洁。
2. vue对象的变量力求精简（越少越好，变量要写好注释）, 没有初始值的统统赋undefined。
3. 复杂的javascript尽量封装出去，不要全写在一个vue文件里面。
4. 需要写成全局的javascript，请提交申请说明到开发组；否则写在子功能模块所在目录。

## javascript数据类型

javascript有两类数据类型

1. 基本数据类型(primitive), 包括：booleans, numbers, strings, null, and undefined.
2. 类对象(objects), 除去基本数据类型其他的是objects

primitive和objects很大的不同在于比较的不同，primitive比较是用值来比较，而objects是用引用来比较。

## enum的使用

javascript没有内建的enum数据类型，但可以使用第三方包[Enumify](https://github.com/rauschma/enumify)

    # --本地安装
    npm install enumify

    //--导入
    import {Enum} from "enumify";
    //--使用
    class Color extends Enum {}
    Color.initEnum(["RED", "GREEN", "BLUE"]);
    let color = Color.GREEN;
    if(color === Color.GREEN) {
        console.log(`color = ${this.color}  color等于Color.GREEN = ${this.color === Color.GREEN}`);
    }

## javascript的空数据类型

由于历史的原因，javasript有两种代表空的数据类型：
1. undefined
2. null

所以我们判断一个数据变量是否为空，我们统一使用下面这句话判断：

    if (x === undefined || x === null) {
        //x为空
    }

## javascript的逻辑等于或逻辑不等于

javascript逻辑等于和逻辑不等于都有两种写法：

1. == 或 ===
2. != 或 !==

其中 == 和 != 为宽松等于和宽松不等于；
其中 === 和 !== 为严格等于和严格不等于。
*为了统一我们都使用严格等于和严格不等于。*
碰到忽略大小写的比较时，我们做如下处理：
    
    //忽略大小写的比较
    if(weex.config.env.platform.toLowerCase() === "ios") {
        //当前系统时iOS。。。
    }

## string的单引号还是双引号？

答：我们统一使用双引号。

javascript中有单引号和双引号括起来的string基本没有任何区别。
但鉴于json里面的key必须是双引号括起来的string，为了不出问题我们统一使用双引号。

## 再来谈谈this

ES6的箭头函数（arrow function）与javascript一般的函数（function）最大区别在于：
前者没有自带的this，而后者自带。

所以箭头函数作为类的构造函数，即不能new；而javascript一般的函数可以new。

*我们说javascript一般的函数（function）都带一个this，但是this如果离开类对象基本没有用。*
我们说一般的函数（function）有三种角色：1.构造函数；2.类的成员方法；3.一般的函数。

1. 构造函数里的this指向自身
2. 类的成员方法一般是由类对象调用的，this指向它所在的类对象
3. 一般的函数基本不会用到this。因为它不是一个类，也不是一个类对象的成员方法，所以压根就不要用this。

[vue特别声明某些生命周期钩子函数不能使用箭头函数](https://vuejs.org/v2/guide/instance.html#Data-and-Methods)

*箭头函数的this来自于它所在的父作用域，一旦绑定就固化了. 而function的this有可能变化。这也是我们利用箭头函数的一个优点。见下面例子*

    //--- demo of using this---
    let otherClient = {
        x: 100
    };

    function AModule(){
       this.x = 42;
       this.getX = function(){
       return this.x;
       },
       this.arrowFunc = () => this.x; 
    }

    let a = new AModule();
    let unboundGetX = a.getX;
    console.log('unbound getX x = ', unboundGetX());
    console.log('normal invoking x = ', a.getX());
    console.log('apply:a getX x = ', a.getX.apply(a));
    console.log('apply:apply getX x = ', a.getX.apply(otherClient));

    console.log('-------arrowFunc------------');
    console.log('arrowFunc invoking x = ', a.arrowFunc());
    console.log('arrowFunc:apply:a getX x = ', a.arrowFunc.apply(a));
    console.log('arrowFunc:apply:otherClient getX x = ', a.arrowFunc.apply(otherClient));

    //---- use apply to bind this, and callback can leverage 'this' to get some specific variables from the caller (following code demostrades the node.js sqlite3 library) 
    
    const sqlite3 = require('sqlite3');
    const Promise = require('bluebird');

    class AppDAO {

        constructor(dbFilePath) {
            this.db = new sqlite3.Database(dbFilePath, (err) => {
                if(err) {
                    console.log('ZJC could not connect to database');
                }
                else {
                    console.log('ZJC Connected to database');
                }
            });
        }

        run(sql, params = []) {
            return new Promise((resolve, reject) => {
                this.db.run(sql, params, function(err) {
                    if(err) {
                        console.log('ZJC Error running sql : ' + sql + ' Error :' + err );
                        reject(err);
                    }
                    else {
                        resolve({id: this.lastID});
                    }
                })
            })
        }

    }

## import的路径

    //导入vue文件，.vue后缀禁止省略。 
    import titlec from "../../../tools/titlec.vue";
    
    #如果导入时vue后缀省略了，weex打包／压缩打包命令将失败
    weex compile -m src detinationFolder

    //导入js文件，.js缀可以省略。如:
    import { YtQuerySQL } from "../../plugins/DbPlugins";    


