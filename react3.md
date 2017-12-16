## http-网络请求

安装`axios`

`cnpm install axios --save`

使用：

```javascript
import http from 'axios'

http.get("http://h5.yztctech.net/api/axf/apihomehot.php")
	.then(function (data){
		console.log(data)
	})
```



## react-router

安装：

`cnpm install react-router-dom --save`

引入模块：

```react
import {
  BrowserRouter as Router,
  Route,
  Link
} from 'react-router-dom'
```

编写3个组件并引入：

```javascript
import Home from './components/home'
import Cart from './components/cart'
import Mine from './components/mine'
```

设置路由：

```react
export default class App extends React.Component{
	
	render(){
		
		return (
			<Router>
				<div>
					<ul>
						<li>
							<Link to="/">Home</Link>
						</li>
						<li>
							<Link to="/cart">Cart</Link>
						</li>
						<li>
							<Link to="/mine">Mine</Link>
						</li>
					</ul>
					<Route exact path="/" component={Home}/>
        			<Route path="/cart" component={Cart}/>
        			<Route path="/mine" component={Mine}/>
				</div>
			</Router>
			)
	}
}
```

> 在上面的基础示例中，子组件均使用了extends 去继承React.Component，如果只是简单的视图内容，可以使用简单的函数去书写

```react
// import Mine from './components/mine'
// 简写
const Mine = ()=>(
  <div>
  	<h1>我的页面</h1>
  </div>
)
```

**代码解析**

1. `<Link/> `     配置路由访问 ，将被渲染为a标签
2. to 点击链接要跳转到的路由路径
3. path   匹配的路径  与 link to 匹配
4. component    当匹配规划成立时，要渲染的组件
5. exact     *Boolean*   是否完全匹配（参照下文中isExact）注意：当没有设置具体路径名称时，比如上面示例中的  /   ，exact必须添加，否则会发生模糊匹配



### 嵌套路由

在基础示例中的app.js中补充一个items路由配置:

```javascript
...
import Items from "./Items"
...
...
<li>
  <Link to="/items">Items</Link>
</li>
 ...
 ...
 <Route path="/items" component={Items}/>
 ...
```

Items.js

```javascript
import React from 'react'
import {BrowserRouter as Router, Link, Route} from "react-router-dom"
import Item from './item'

export default class ComponentItems extends React.Component{

	render(){
		return (
			<div>
	            <h1>商品</h1>
	            <Link to={`${this.props.match.url}/TV`}>电视</Link>
	            <Route path={`${this.props.match.url}/TV`} component={Item}></Route>
	            <Route exact path={this.props.match.url} render={() => (
	                <h3>请选择一类商品</h3>
	            )}/>
	        </div>
			)
	}
}
```

item.js

```javascript
import React from 'react'

export default class ComponentTv extends React.Component{

	render(){
		return (
			<div>
				<h1>详情商品</h1>
			</div>
			)
	}
}
```



**代码解析**

1. match 匹配对象

   - params 动态设置的路由参数

   - isExact 是否完全匹配（没有后面的斜线）

     设置匹配路径               实际地址路径                              exact                                 是否匹配                      

   | `/one` | `/one/two` | `true`  | no   |
   | ------ | ---------- | ------- | ---- |
   | `/one` | `/one/two` | `false` | yes  |

   - path 匹配路径（Route中设置的path）

   - url 路径（Link中设置的to）

   - 可在如下场景中获取match对象(最常用的两种)

     - 当使用Component编写时 通过 this.props.match获取（上面的示例中）

     - 当使用普通的render函数

       ```javascript
       const Com = ({match})=>(
           <div>
               <h1>{mathc.url}</h1>
           </div>
       )
       ```

2. render 配合在当前示例中时，表示未匹配子路由时，渲染一个静态内容

### 路由参数

对Items.js进行修改如下：

```javascript
import React from 'react'
import {BrowserRouter as Router, Link, Route} from "react-router-dom"
import Item from './item'

export default class ComponentItems extends React.Component{
	constructor({match}){
		super()
		this.match = match
	}
	render(){
		return (
			<div>
	            <h1>商品</h1>
	            <Link to={`${this.match.url}/TV`}>电视</Link><br/>
	            <Link to={`${this.match.url}/phone`}>手机</Link><br/>
            	<Link to={`${this.match.url}/compute`}>电脑</Link>
	            <Route path={`${this.match.url}/:type`} component={Item}></Route>
	            <Route exact path={this.match.url} render={() => (
	                <h3>请选择一类商品</h3>
	            )}/>
	        </div>
			)
	}
}
```

对item.js进行修改如下:

```javascript
import React from 'react'

export default class ComponentItem extends React.Component{

	render(){
		return (
			<div>
				<h1>
					具体详情
					{this.props.match.params.type}
				</h1>
			</div>
			)
	}
}
```

## redux

[中文文档地址](http://cn.redux.js.org/index.html)

[官方文档](http://redux.js.org/)

Redux 是 JavaScript 状态容器，提供可预测化的状态管理。

不要为了使用`redux`而去使用redux

redux作者文章：

[也许你不必使用 Redux](http://qianduan.guru/2016/09/25/you-might-not-need-redux/)

[原文地址](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)

redux运行流程：

<img src="http://pic.w2bc.com/upload/201705/19/201705191136191899.jpg" width="500"/>

main.js:

```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import { Provider } from 'react-redux'
import store from './redux'
// 引入app组件
import App from './app'

ReactDOM.render(
	<Provider store={store}>
		<App/>
	</Provider>,
	document.getElementById('app')
	)
```

app.js:

```javascript
import React from 'react'
import {connect} from 'react-redux'
import Banana from './components/banana'

class App extends React.Component{
	
	// 在组件加载完成后
	componentDidMount(){

		// console.log(this.context)
		// 把reducer的处理传递给createStore方法
		// 相当于创建了store->用来管理在reducer定义的state
		// 初始化返回的是counter为0
		// var store = createStore(reducer)

		// 获取所有的state
		// console.log(store.getState())

		// 触发action方法->跟vuex的是一样的
		// store.dispatch(actions.inc())
		// console.log(store.getState())
	}

	render(){
		return (
			<div>
				数量为：{this.props.value}
				<Banana/>
			</div>
			)
	}
}
function mapStateToProps(state) {

  return {
    value: state.counter
  }
}
export default connect(mapStateToProps)(App)

```

reducers.js:

```javascript
export default (state=0,action) => {

	// 第一次初始化
	// counter->计数器加减
	state = state || {counter:0}

	// 判断action传过来的是什么，并执行对应的处理
	switch (action.type){
		case 'ADD':
			return {counter: state.counter + 1}
		case 'SUB':
			return {counter: state.counter - 1}
		default:
			return state
	}
}
```

actions:

```javascript
// 定义一些action的方法
export default {
	inc(){
		return {type: 'ADD'}
	},
	dec(){
		return {type: 'SUB'}
	}
}

```

redux/index.js:

```javascript
import {createStore} from 'redux'
import reducer from './reducers'

export default createStore(reducer)

```

banana:

```javascript
import React from 'react'
import {connect} from 'react-redux'
import actions from '../redux/actions'
// console.log(connect)
class Banana extends React.Component{
	constructor(props){
		super()
		this.dispatch = props.dispatch
	}
	componentDidMount() {
		
	}
	addOne(){
	 	this.dispatch(actions.inc())
	}
	render(){
		return (
			<div>
				<h1>香蕉</h1>
				<button onClick={this.addOne.bind(this)}>买一个</button>
			</div>
			)
	}
}
function mapStateToProps(state) {
  return {
    value: state.counter
  }
}

export default connect(
	mapStateToProps)(Banana)

```

