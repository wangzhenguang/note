[TOC]

### redux中文文档
---
[redux中文文档](http://cn.redux.js.org/docs/recipes/ReducingBoilerplate.html)

### 部署Redux架构
---
- npm install --save redux
- npm install --save react-redux
- npm install --save-dev redux-devtools


**要点**
> 应用中所有的state都一一个对象的形式储存在一个单一的store中。
唯一改变state的办法就是出发action，一个描述发生什么的对象。
为了描述action如何改变state树，你需要编写reducers


### 使用redux架构的步骤 
---
#### 新建action 描述用户的意图 
```
//action creator 创建一个action
export const action = (value) =>({
    type : 'action_type',

    value : value

})
//action(1)
// {type: 'action_type',number:1}
```
#### 创建reducer
---
```
const initialState = {c : 0 };
export const reducer = (state = initialState, action) = > {
    switch(action.type){

        case 'action_type':

           return {c : action.number};
        default:

           return state;

    }

}
```
### 创建store
---
```
const rootReduce = combineReduces({
    reducer,
});
let store = createStore(reducer);
//导出store
export const getStore = () =>{return store}
```


#### 使用connect将数据模型链接
---
```
const mapStateToProps = state = > {
    return{ reducer:state.reducer ;}

}
connect(mapStateToProps)(`要链接的组件`)
```

#### 分发action
this.props.dispatch(action(11));

### Provider
---
```
//使用redux必须要将组建用Provider包裹
render (){
    return (
        <Provider store={store} >
            <View />
        </Provider>
    )
}
```


### import * as



#### 自己简单实现redux

- 事件处理
```

    let events = {
    };
    
    exprot const addEvent = (eventName,handler) =>{
        
        // 获取对应的实例
        const listeners = events[name];
        if(!listeners){
            events[name] = [];
        }
        
        // 将值存储
        events[name].push[handler];
    }


    //清除事件
    export const remove = (eventName,handler ) =>{
        const listeners = events[eventName];
        
        event[name] = listeners.find(v = v!== handler);
    }

```

- 连接组件
```

 export const connector = (name,_Component) =>{
     
     class SimpleEventConnector extends Component{
         constructor(props){
             super(props);
             this.state ={
                 data:0,
             };
         }
      
        componentDidMount(){
          on(name,this.handler);
        }
      
        componentWillUnmount(){
          remove(name,this.handler);
        }
      
         render(){
           const {data} = this.state;
           return <_Component data={data} />
        }
    
     }
     
     return SimpleEventConnector;
 }


```