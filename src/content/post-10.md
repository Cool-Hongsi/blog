---
title: "Redux Middleware"
date: "2019-09-02"
draft: false
path: "/blog/redux-middleware"
---

### Redux Middleware
- Definition : Middleware is implemented by pre-specified tasks before the action is dispatched and it is processed by the reducer.
- Process : Action - Middleware - Reducer - Store
- Role of Middleware : Logging, Reporting Errors, Asynchronous
- redux-logger / redux-thunk / redux-promise-middleware / redux-saga ...

---

#### redux-logger
- Generate readable log
```js
import { createStore, applyMiddleware } from 'redux';
import modules from './modules';
import { createLogger } from 'redux-logger';
const logger = createLogger();
const store = createStore(modules, applyMiddleware(logger));
export default store;
action INCREMENT @ 19:17:06.765
redux-logger.js:1  prev state {counter: 0}
redux-logger.js:1  action     {type: "INCREMENT", payload: Proxy}payload: Proxy {dispatchConfig: null, _targetInst: null, isDefaultPrevented: null, isPropagationStopped: null, _dispatchListeners: null, …}type: "INCREMENT"__proto__: Object
redux-logger.js:1  next state {counter: 1}counter: 1__proto__: Object
```
---

#### redux-thunk
- Async (Surrounded function type in order to implement specified work later)
- This middleware allows you to create an action-generating function that generates a function instead of an object.
- If you want a particular action to run in a few seconds, or if you want the action to be ignored according to its current state, you can't do it as a regular action creator. However, redux-thunk makes it possible.

---
##### Async without Promise
```js
function printLater(number, fn) {
    setTimeout(
        function() { console.log(number); fn(); },
        1000
    );
}

printLater(1, function() {
    printLater(2, function() {
        printLater(3, function() {
            printLater(4);
        })
    })
})
```
---
##### Async with Promise
```js
function printLater(number) {
    return new Promise(
        resolve => {
            setTimeout(
                () => {
                    console.log(number);
                    resolve(); // Inform promise is done
                },
                1000
            )
        }
    )
}

printLater(1)
.then(() => printLater(2))
.then(() => printLater(3))
.then(() => printLater(4))
.then(() => printLater(5))
.then(() => printLater(6))

```

---
[Redux Application with Thunk Github](https://github.com/Cool-Hongsi/Redux-Application-Thunk)

---

#### index.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

import { createStore, applyMiddleware } from 'redux';
import rootReducer from './store/modules/index';
import { Provider } from 'react-redux';
import ReduxThunk from 'redux-thunk'; // Make possible to Async (axios)

const store = createStore(rootReducer, applyMiddleware(ReduxThunk));

ReactDOM.render(
<Provider store={store}>
    <App />    
</Provider>, document.getElementById('root'));
```
---

#### App.js
```js
import React, { Component } from 'react';
import CounterContainer from './containers/CounterContainer';
import './App.css';

export default class App extends Component{
  render(){
    return(
      <CounterContainer />
    )
  }
}
```
---

#### store/modules/index.js
```js
import { combineReducers } from 'redux';
import counter from './counter';
import post from './post';

// Root Reducer
export default combineReducers({
    counter,
    post,
    // other reducers
});
```
---

#### store/modules/counter.js
```js
const CHANGE_COLOR = 'CHANGE_COLOR';
const INCREMENT = 'INCREMENT';
const DECREMENT = 'DECREMENT';

export const changeColor = (color) => {
    return { type : CHANGE_COLOR, color }
};

export const increment = () => {
    return { type : INCREMENT }
};

export const decrement = () => {
    return { type : DECREMENT }
};

const initialState = {
    color : "red",
    number : 1
};

export default function counter(state = initialState, action){
    switch(action.type){
        case CHANGE_COLOR:
            return { ...state, color : action.color }
        case INCREMENT:
            return { ...state, number : state.number + 1 }
        case DECREMENT:
            return { ...state, number : state.number - 1}
        default:
            return state
    }
};
```
---

#### store/modules/post.js
```js
import axios from 'axios';

const GET_POST_PENDING = 'GET_POST_PENDING';
const GET_POST_SUCCESS = 'GET_POST_SUCCESS';
const GET_POST_FAILURE = 'GET_POST_FAILURE';

export const getPostAPI = (postId) => {
    return axios.get(`https://jsonplaceholder.typicode.com/posts/${postId}`);
};

export const getPost = (postId) => (dispatch) => {
    dispatch( {type : GET_POST_PENDING} ); // Set the state as pending

    return getPostAPI(postId).then((res) => { // If axios (getPostAPI func) is success
        dispatch( {type : GET_POST_SUCCESS, payload : res} );
    }).catch((err) => { // If axios (getPostAPI func) is fail
        dispatch( {type : GET_POST_FAILURE} );
    })
};

/*
https://jsonplaceholder.typicode.com/posts/1
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
}
*/

const initialState = {
    pending : false, // Represent pending statement
    error : false, // Represent error statement
    data : { // Store JSON API data
        title : '',
        body : ''
    }
};

export default function post(state = initialState, action){
    switch(action.type){
        case GET_POST_PENDING:
            return { ...state, pending : true, error : false }
        case GET_POST_SUCCESS:
            return { ...state, pending : false, error : false, data : { title : action.payload.data.title, body : action.payload.data.body } }
        case GET_POST_FAILURE:
            return { ...state, pending : false, error : true }
        default:
            return state
    }
};
```
---

#### CounterContainer.js
```js
import React, { Component } from 'react';
import { connect } from 'react-redux';
import Counter from '../components/Counter';
import { changeColor, increment, decrement } from '../store/modules/counter';
import { getPost } from '../store/modules/post';

class CounterContainer extends Component{
    
    // In order to show first data on page
    componentDidMount = () => {
        const { number, getPost } = this.props; // number from counter reducer , getPost from post reducer
        getPost(number);
    };

     // Call this lifecycle method every the number value is different with present number value
    componentWillReceiveProps = (nextProps) => {
        const { number, getPost } = this.props;
        if(number !== nextProps.number){
            getPost(nextProps.number);
        };
    };

    onSelectedColor = (color) => {
        const { changeColor } = this.props;
        changeColor(color);
    };

    onSelectedIncrement = () => {
        const { increment } = this.props;
        increment();
    };

    onSelectedDecrement = () => {
        const { decrement } = this.props;
        decrement();
    };

    render(){

        const { color, number, pending, error, data } = this.props;

        return(
            <Counter
                color={color}
                number={number}
                pending={pending}
                error={error}
                data={data}
                onSelectedColor={this.onSelectedColor}
                onSelectedIncrement={this.onSelectedIncrement}
                onSelectedDecrement={this.onSelectedDecrement}
            />
        )
    }
};

const mapStateToProps = (state) => {
    // state means current state
    // Whenever the state value is changed, it is called and set the new value into state
    return {
        color : state.counter.color,
        number : state.counter.number,
        pending : state.post.pending,
        error : state.post.error,
        data : state.post.data
    }
};

const mapDispatchToProps = (dispatch) => {
    // set each function in reducers and call the dispatch with the return value
    return {
        changeColor : (color) => { dispatch(changeColor(color)) },
        increment : () => { dispatch(increment()) },
        decrement : () => { dispatch(decrement()) },
        getPost : (postId) => { dispatch(getPost(postId)) }
    }
};

// CounterContainer component can use mapStateToProps and mapDispatchToProps
export default connect(
    mapStateToProps,
    mapDispatchToProps
)(CounterContainer);
```
---

#### Counter.js
```js
import React from 'react';

const colorList = ["red", "black", "green", "blue", "yellow", "violet"];

const Counter = ( {color, number, pending, error, data, onSelectedColor, onSelectedIncrement, onSelectedDecrement} ) => { // set the props
    return(
        <div>
            {colorList.map((el, index) => {
                return(
                    <button key={index} style={{backgroundColor:`${el}`, outline:"none"}} onClick={() => onSelectedColor(el)}>{el}</button>
                )
            })}
            
            <br/>

            <h2 style={{color}}>{number}</h2>
            <button onClick={onSelectedIncrement}>+</button>
            <button onClick={onSelectedDecrement}>-</button>

            <br/>

            {pending ? <h3>Loading...</h3> : null}
            {error ? <h3>Error...</h3> : (<div> <h4 style={{color}}>Title : {data.title}</h4> <h4 style={{color}}> Body : {data.body}</h4> </div>)}
        </div>
    )
}

export default Counter;
```
---
