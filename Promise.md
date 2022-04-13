## Promise 概述

 - **Promise**是ES6中提供的**优化异步操作**的解决方案。解决了ES6之前异步操作，反复嵌套回调函数所导致的**回调地狱**问题。
 - 从语法上来讲，**Promise**是一个**构造函数**，它可以获取异步操作的请求，且返回一个结果。**Promise**有三种状态：**pending**(等待)，**fulfiled**(成功)，**rejected**(失败)。状态一旦改变，就不会再变。创造**Promise**实例后，它会**立即执行**。

## 什么是回调地狱
在开发中，会遇到一个异步请求后再执行另一个异步请求的情况。例如请求一个列表数据，之后根据列表找到索引，再去请求分类......一直请求，每一次异步请求，都需要反复嵌套回调函数，导致代码可读性大大降低，后期难以维护。

## 创建Promise实例

 - 创建一个Promise对象。
 - 调用`then()`和`catch()`去处理结果。

```javascript
let fullPromise = new Promise((resolve,reject) => {
	if (true) {
		resolve('Success Promise');
	} else {
		reject('Error Promise');
	}
})

console.log(fullPromise);

fullPromise.then((res) => {
        console.log('resolved',res);
    },(err) => {
        console.log('rejected',err);
    }
)

fullPromise.catch((res) => {
	console.log(res);
})
```
创建成功的Promise对象——简写resolve

```javascript
let p = Promise.resolve('Success Promise');
```
创建失败的Promise对象——简写reject

```javascript
let p = Promise.reject('Error Promise');
```

## Resolve()，Reject()

 - Promise对象是由关键字`new`及其构造函数来创建的，构建函数后，会把一个叫做“处理器函数”（executor function）的函数作为它的参数，并且传入两个参数：`resolve`，`reject`，当异步任务顺利完成返回结果时，会调用`resolve`，而当异步任务失败返回结果时（通常是一个错误对象），会调用`reject`。
 - 其实这里用“成功”和“失败”来描述并不准确，按照标准来讲，`resolve`是将Promise的状态置为`fullfiled`，`reject`是将Promise的状态置为`rejected`。

```javascript
let fullPromise = new Promise((resolve,reject) => {
	if (true) {
		resolve('Success Promise');
	} else {
		reject('Error Promise');
	}
})

console.log(fullPromise);
```
这里`if`设置为`true`，也就是输出`resolve('Success Promise')`。

打印结果:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200910205521136.png)
将`if`设置为`false`，也就是输出`reject('Error Promise')`。

```javascript
let fullPromise = new Promise((resolve,reject) => {
	if (false) {
		resolve('Success Promise');
	} else {
		reject('Error Promise');
	}
})

console.log(fullPromise);
```
打印结果:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200910210044752.png)
可以看到，通过调用`resolve()`和`reject()`改变了Promise对象的状态。

## Then()的用法

 - `then()`是定义在原型上的 Promise.prototype 。
 - `then()`之后会返回一个新的promise对象。
 -  `then()`中可以传入两个参数，是函数。第一个对应`resolve`的回调，第二个对应`reject`的回调。

```javascript
let fullPromise = new Promise((resolve,reject) => {
	if (true) {
		resolve('Success Promise');
	} else {
		reject('Error Promise');
	}
})

console.log(fullPromise);

fullPromise.then((res) => {
        console.log('resolved',res);
    },(err) => {
        console.log('rejected',err);
    }
)
```
打印结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200910210549932.png)
可以看到`then()`对应`resolve()`获取到了结果`res`。

将`if`设置为`false`:

```javascript
let fullPromise = new Promise((resolve,reject) => {
	if (false) {
		resolve('Success Promise');
	} else {
		reject('Error Promise');
	}
})

console.log(fullPromise);

fullPromise.then((res) => {
        console.log('resolved',res);
    },(err) => {
        console.log('rejected',err);
    }
)
```
打印结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200910211459238.png)
可以看到`then()`对应`reject()`获取到了结果`err`。

通过`then()`就能把原来的回调写法分离出来，在异步操作执行完后，用**链式调用**的方式执行回调函数。

## 链式操作的用法

 - 从表面上看，Promise只是能够简化层层回调的写法，而实质上，Promise的精髓是“状态”，用维护状态、传递状态的方式来使得回调函数能够及时调用，它比传递callback函数要简单、灵活的多。
 - 可以在`then()`中继续写Promise对象并返回，然后继续调用`then()`来进行回调操作。  

所以使用Promise的正确场景是这样的：

```javascript
function runTimeP(){
	let timeP = new Promise((resolve,reject) => {
		setTimeout(function(){
			resolve('TimeP Success');
		},2000);
	});

	return timeP;
}

runTimeP()
.then((res) => {
        console.log('resolved1',res);
        return runTimeP();
    },(err) => {
        console.log('rejected1',err);
    }
)
.then((res) => {
        console.log('resolved2',res);
        return runTimeP();
    },(err) => {
        console.log('rejected2',err);
    }
)
.then((res) => {
        console.log('resolved3',res);
        return runTimeP();
    },(err) => {
        console.log('rejected3',err);
    }
)
```
打印结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020091110492677.png)

 - 在上面的代码中，执行了一个异步操作`setTimeout()`，2秒后，调用`resolve()`。
 - 在`runTimeP()`中传给`resolve`的数据，通过直接`return`Promise对象，能在接下来的`then()`中拿到。
 - 实现了按顺序每隔两秒输出异步请求返回的结果，而且解决了**回调地狱**的问题。

## Catch()的用法

 - `catch()`和`then()`的第二个参数一样，用来指定`reject`的回调，效果和写在`then()`第二个参数里面一样。
 - `catch()`还有另外一个作用：在执行`resolve`的回调时，还会捕捉`resolved`抛出的异常。

```javascript
function runTimeP(){
	let timeP = new Promise((resolve,reject) => {
		if (true) {
			setTimeout(function(){
				resolve('TimeP Success');
			},2000);
		} else {
			reject('Time Error');
		}
	});

	return timeP;
}

runTimeP()
.then((res) => {
	console.log(message);	//未定义message
	console.log(res);
})
.catch((res) => {
	console.log('rejected');
	console.log(res);
})
```
打印结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200911113639653.png)


 - 上方代码中，在`then()`中打印了一个未声明的变量`message`，本来应该报错，但是Js没有报错，而且还运行了`catch()`中的内容。

## All()的使用

 - Promise的`all()`提供了并行执行异步操作的能力，并且在所有异步操作执行完后才执行回调。
 - `all()`的参数是对象数组。数组内所有的Promise对象状态为`fulfiled`时，该对象就为`fulfiled`。只要数组中有任意一个Promise对象状态为`rejected`，该对象就为`rejected`。

```javascript
let num = 0;
let timer = setInterval(function(){
	num++;
	console.log(num);
},1000)

function runTimeP1(){
	let timeP = new Promise((resolve,reject) => {
		setTimeout(function(){
			resolve('TimeP1 Success');
		},3000);
	})
	return timeP;
}
function runTimeP2(){
	let timeP = new Promise((resolve,reject) => {
		setTimeout(function(){
			resolve('TimeP2 Success');
		},5000);
	})
	return timeP;
}
function runTimeP3(){
	let timeP = new Promise((resolve,reject) => {
		setTimeout(function(){
			resolve('TimeP3 Success');
		},10000);
	})
	return timeP;
}

Promise.all([runTimeP1(),runTimeP2(),runTimeP3()])
.then((res) => {
	clearInterval(timer);
	console.log(res);
})
```
打印结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200911121544326.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xpbkh0X0NTRE4=,size_16,color_FFFFFF,t_70)

 - 三个Promise对象都为`fulfiled`，设置了`setTimeout()`，分别是3秒后，5秒后，10秒后。
 - 可以看到，使用了`all()`之后，所有异步操作执行完后才执行回调。

其中Promise对象存在`rejected`的情况：

```javascript
let num = 0;
let timer = setInterval(function(){
	num++;
	console.log(num);
},1000)

function runTimeP1(){
	let timeP = new Promise((resolve,reject) => {
		setTimeout(function(){
			resolve('TimeP1 Success');
		},3000);
	})
	return timeP;
}
function runTimeP2(){
	let timeP = new Promise((resolve,reject) => {
		setTimeout(function(){
			reject('TimeP2 Error');
		},5000);
	})
	return timeP;
}
function runTimeP3(){
	let timeP = new Promise((resolve,reject) => {
		setTimeout(function(){
			resolve('TimeP3 Success');
		},10000);
	})
	return timeP;
}

Promise.all([runTimeP1(),runTimeP2(),runTimeP3()])
.then((res) => {
	clearInterval(timer);
	console.log(res);
})
.catch((res) => {
	clearInterval(timer);
	console.log(res);
})
```
打印结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200911122628372.png)
 - 因为其中有一个Promise对象的状态为`rejected`，导致`all()`的状态也为`rejected`，结果并没有返回。
 - 有了`all()`，就可以并行执行多个异步操作，并且在一个`then()`中处理所有的返回数据。

## Race()的用法

 - `all()`是在所有异步操作执行完后才执行回调。与`all()`相反，第一个异步操作执行完后，以它为准执行回调，这就是`race()`。
 - `race()`的用法和`all()`一样，`race()`的参数也是数组对象，当数组中所有的Promise对象，有一个先执行了何种状态，该对象就为何种状态，并执行相应函数。

```javascript
function runTimeP1(){
	let timeP = new Promise((resolve,reject) => {
		setTimeout(function(){
			resolve('TimeP1 Success');
		},3000);
	})
	return timeP;
}
function runTimeP2(){
	let timeP = new Promise((resolve,reject) => {
		setTimeout(function(){
			reject('TimeP2 Error');
		},5000);
	})
	return timeP;
}
function runTimeP3(){
	let timeP = new Promise((resolve,reject) => {
		setTimeout(function(){
			resolve('TimeP3 Success');
		},10000);
	})
	return timeP;
}

Promise.race([runTimeP1(),runTimeP2(),runTimeP3()])
.then((res) => {
	clearInterval(timer);
	console.log(res);
})
.catch((res) => {
	console.log(res);
})
```
打印结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200911124757626.png)

 - `race()`执行了最早运行的`runTimeP1()`。
 - `race()`可以用在请求获取上，设置`setTimeout()`，当获取超时，`race()`就会执行`setTimeout()`。
