# deeper-redux
 1.React 只是 DOM 的一个抽象层，并不是 Web 应用的完整解决方案。有两个方面，它没涉及:

 - 代码结构
 - 组件之间的通信

 2.**react**在大型项目中就显得很乏力，所以2015年时候发布啦 <em>redux</em>,不一定什么时候都用上redux，阮一峰说了“如果你在考虑要不要用redux，那你就不要用啦”。
    什么时候用恩？
    通俗一点的是：<strong>多交互，多数据源的时候。</strong>

    专业的恩：
     - 某个组件的状态，需要共享
     - 某个状态需要在任何地方都可以拿到
     - 一个组件需要改变全局状态
     - 一个组件需要改变另一个组件的状态

 3.首先先知道他的运行机制  

 ![运行机制](./src/redux.jpg)

1.页面发出个事件，比如说一个点击事件，或者表单提交的事件，发出一个类似axios请求，就是store.dispatch（ 商店，仓库发出，派遣 ），传入一个对象，里面<br>
必有的一个type参数，type的值是为了在store那里匹配相应的选项，然后在做相应的修改。后面是多个参数，如果没有可不添加，redux管后面可选的参数叫做<em>action</em>。
 ---
```ruby
    handleSubmit(e){
        e.preventDefault()
        const value = this.input.value
        store.dispatch({ type: 'ADD_Listnews', news: value ,index:index})
        this.form.reset()
    }



    <form onSubmit={this.handleSubmit.bind(this} ref={form => this.form=form}>
        <input type="text" ref={input => this.input=input} />
        <button>添加</button><br />
    </form>

```
2.好了请求发完啦，开始接收做处理啦。就这到了reducer啦，reducer是用来接收参数和处理参数，再返回修改后的树形结构。这里需要注意的是不能修改state，不能
<br>返回修改后的state，返回啦也没效果。（好几次都是这里出毛病）用了switch 语句来匹配请求的type值，修改后再返回，强调不能返回修改后的state。有两种办法
- copy 对象或者数组,返回copy之后的。
- 先把state放前面，之后在替代前面的值或者增加值（如下面这样）

    这里要引入createStore，让reducer 放入 store 里，传出去。 其他页面可以通过 store.getState() 获得。
```ruby
        import { createStore } from 'redux'

        let Listnews={
            1:"遛狗",
            2:"跟小朋友吃饭",
            3:"找顾客谈合作"
        }

        function ListnewsReducer(state = Listnews, action) {
           switch (action.type) {
               case 'ADD_Listnews':
               let newobj = Object.assign(state);
                   return {...newobj,[action.index]:action.news }
               default:
                   return state
           }
        }

        let store = createStore(ListnewsReducer)

        export default store
```
如果数据多，或者复杂，我们可以定义多个reducer，然后通过 combineReducers 来包装成一个rootReducer来传递，因为let store = createStore(ListnewsReducer)，这里只能放一个reudcer。

```ruby

    import { combineReducers }from 'redux'

    let rootReducer = combineReducers({
       Listnews: ListnewsReducer,
       checked : checkedReducer,
       url: urlReducer
   })

   let store = createStore(rootReducer)
```
3.返回了新的树形结构，但是redux 不会自动渲染，所以就要借助 react-redux ，来协助redux来重新渲染react页面或者组件。（重新渲染react只有state 和
<br>props，所以 react-redux 就模拟props变化来刷新页面）

    ```ruby
    import { Provider } from 'react-redux'
    import store from './store'

                <Provider store = {store}>
                    <div>
                        <Listnews></Listnews>
                        <Swith></Swith>
                    </div>
                </Provider>

    ```
    Provider：接收store，并将store传到子组件中，当store发生变化时，更新store；

    ```ruby
    import { connect } from 'react-redux'

    this.props.url

    const mapStateToProps = (state) => (
         {
             Listnews:state.Listnews,
             checked:state.checked,
             url:state.url
         }
     )

     export default connect(mapStateToProps)(Listnews)

    ```
    这里的state就是由 Provider 传过来的state。页面就会发生相应的变化。
