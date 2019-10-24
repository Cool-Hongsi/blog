---
title: "Immutability"
date: "2019-10-24"
draft: false
path: "/blog/immutability"
---

### Introduction
Likewise immutable.js, immer.js enable to keep immutability for state value. Immutability is important in ReactJS for keeping performance and optimization. The value of state should be immutable and never attempt to modify it directly. It should keep immutable by creating new object and update it to original state. There are some ways to deal with it (Spread Operator, assign method and so on). However, in case of complicated structure object, then immer.js or immutable.js would be good way to keep it immutable. For studying, I've created very tiny application with immer.js below (Todo & Github API).

---

### Immer.js (Refer from immerjs.github.io/immer)
The basic idea is that you will apply all your changes to a temporary draftState, which is a proxy of the currentState. Once all your mutations are completed, Immer will produce the nextState based on the mutations to the draft state. This means that you can interact with your data by simply modifying it while keeping all the benefits of immutable data.
Current -> Draft -> Next

#### Quick Example
```js
import produce from "immer"

const baseState = [
    {
        todo: "Learn typescript",
        done: true
    },
    {
        todo: "Try immer",
        done: false
    }
]

const nextState = produce(baseState, draftState => {
    draftState.push({todo: "Tweet about it"})
    draftState[1].done = true
})
```

---

[Officiai-Site](https://github.com/immerjs/immer)
[Mine](https://github.com/Cool-Hongsi/immer)

---

#### Dependencies
```js
    "axios": "^0.19.0",
    "immer": "^4.0.2",
    "react": "^16.11.0",
    "react-dom": "^16.11.0",
    "react-redux": "^7.1.1",
    "react-scripts": "3.2.0",
    "redux": "^4.0.4",
    "redux-thunk": "^2.3.0"
```

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
import ReduxThunk from 'redux-thunk';

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
import TodoContainer from './container/TodoContainer';
import GithubContainer from './container/GithubContainer';

export default class App extends Component{
  render(){
    return(
      <div>
        <TodoContainer />
        <GithubContainer />
      </div>
    )
  }
}
```
---

#### store/modules/index.js
```js
import { combineReducers } from 'redux';
import todoReducer from './todo';
import githubReducer from './github';

export default combineReducers({
    todoReducer,
    githubReducer,
    // Other Reducers...
});
```
---

#### store/modules/todo.js
```js
import produce from 'immer';

export const ADD_TODO = 'ADD_TODO';
export const REMOVE_TODO = 'REMOVE_TODO';

let initialId = 0;

export const addTodo = (todoData) => {
    return { type : ADD_TODO, id : initialId++, data : todoData, completed : false };
};

export const removeTodo = (todoDataId) => {
    return { type : REMOVE_TODO, id : todoDataId };
};

export default function todoReducer(state = [], action){
    switch(action.type){
        case ADD_TODO:
            return produce(state, draftState => {
                draftState.push({
                    id : action.id,
                    data : action.data,
                    completed : action.completed
                });
            });
        case REMOVE_TODO:
            return produce(state, draftState => {
                draftState[action.id].completed = !draftState[action.id].completed;
            });
        default:
            return state;
    }
};
```

---

#### store/modules/github.js
```js
import axios from 'axios';
import produce from 'immer';

export const GET_POST_GITHUB = 'GET_POST_GITHUB';

export const getPostAPI = (postId) => {
    return axios.get(`https://api.github.com/users/${postId}`);
};

export const getPost = (postId) => (dispatch) => {
    return getPostAPI(postId).then((res) => {
        dispatch( { type : GET_POST_GITHUB, payload : res.data } );
    }).catch((err) => {
        console.log(err);
    })
};

const initialState = {
    data : [
        {
            login : 'Cool-Hongsi',
            id : '39789641',
            avatar_url : 'https://avatars3.githubusercontent.com/u/39789641?v=4'
        }
    ]
};

export default function githubReducer(state = initialState, action){
    switch(action.type){
        case GET_POST_GITHUB:
            return produce(state, draftState => {
                let validation = state.data.find(item => item.id === action.payload.id);

                if(validation === undefined){ // no repeat
                    draftState.data.unshift({
                        login : action.payload.login,
                        id : action.payload.id,
                        avatar_url : action.payload.avatar_url
                    });
                }
                else{
                    return state;
                }
            })
        default:
            return state;
    }
};
```
---

#### containers/TodoContainer.js
```js
import React, { Component } from 'react';
import { connect } from 'react-redux';
import Todo from '../component/Todo';
import { addTodo, removeTodo } from '../store/modules/todo';

class TodoContainer extends Component{

    // When the value of state is changed, it will call the render function.
    // There will be unnecessary rendering along with degrade performance and difficult optimization
    // To deal with, use shouldComponentUpdate API to check the validation both current and next value
    // Depending on returning boolean value, able to control rendering and it could improve the performace

    shouldComponentUpdate = (nextProps, nextState) => {
        const { todoList } = this.props;

        if(todoList !== nextProps.todoList){
            return true; // rendering
        }
        else{
            return false; // no rendering
        }
    }

    onSelectedAdd = (todoData) => {
        const { addTodo } = this.props;
        addTodo(todoData);
    };

    onSelectedRemove = (todoDataId) => {
        const { removeTodo } = this.props;
        removeTodo(todoDataId);
    };

    render(){

        const { todoList } = this.props;

        return(
            <Todo
                todoList={todoList}
                onSelectedAdd={this.onSelectedAdd}
                onSelectedRemove={this.onSelectedRemove}
            />
        )

    }
};

const mapStateToProps = (state) => {
    return {
        todoList : state.todoReducer
    };
};

const mapDispatchToProps = (dispatch) => {
    return {
        addTodo : (todoData) => { dispatch(addTodo(todoData)) },
        removeTodo : (todoDataId) => { dispatch(removeTodo(todoDataId)) }
    };
};

export default connect(
    mapStateToProps,
    mapDispatchToProps
)(TodoContainer);
```
---

#### containers/GithubContainer.js
```js
import React, { Component } from 'react';
import { connect } from 'react-redux';
import Github from '../component/Github';
import { getPost } from '../store/modules/github';

class GithubContainer extends Component{

    // When the value of state is changed, it will call the render function.
    // There will be unnecessary rendering along with degrade performance and difficult optimization
    // To deal with, use shouldComponentUpdate API to check the validation both current and next value
    // Depending on returning boolean value, able to control rendering and it could improve the performace

    shouldComponentUpdate = (nextProps, nextState) => {
        const { githubList } = this.props;

        if(githubList !== nextProps.githubList){
            return true; // rendering
        }
        else{
            return false; // no rendering
        }
    }

    onSelectedGithub = (postId) => {
        const { getPost } = this.props;
        getPost(postId);
    };

    render(){

        const { githubList } = this.props;

        return(
            <Github
                githubList={githubList}
                onSelectedGithub={this.onSelectedGithub}
            />
        )

    }
};

const mapStateToProps = (state) => {
    return {
        githubList : state.githubReducer.data
    };
};

const mapDispatchToProps = (dispatch) => {
    return {
        getPost : (postId) => { dispatch(getPost(postId)) }
    };
};

export default connect(
    mapStateToProps,
    mapDispatchToProps
)(GithubContainer);
```
---

#### components/Todo.js
```js
import React, { Component } from 'react';

export default class Todo extends Component{
    constructor(props){
        super(props);

        this.state = {
            todoInput : ''
        };

        this.setTodo = this.setTodo.bind(this);
    }

    setTodo = (e) => {
        this.setState({
            todoInput : e.target.value
        })
    };

    render(){

        const { todoList, onSelectedAdd, onSelectedRemove } = this.props;

        return(
            <div>
                <input type="text" placeholder="ToDo" onChange={this.setTodo} />
                <button onClick={() => onSelectedAdd(this.state.todoInput)}>SEND</button>
                {todoList.map((el, index) => {
                    return(
                        <div key={index} onClick={() => onSelectedRemove(el.id)} style={{textDecoration : el.completed ? "line-through" : "none"}}>
                            {el.id} / {el.data}
                        </div>
                    )
                })}
            </div>
        )
    }
}

```
---

#### components/Github.js
```js
import React, { Component } from 'react';

export default class Github extends Component{
    constructor(props){
        super(props);

        this.state = {
            githubInput : ''
        };

        this.setGithub = this.setGithub.bind(this);
    }

    setGithub = (e) => {
        this.setState({
            githubInput : e.target.value
        })
    };

    render(){

        const { githubList, onSelectedGithub } = this.props;

        return(
            <div>
                <input type="text" placeholder="Github ID" onChange={this.setGithub} />
                <button onClick={() => onSelectedGithub(this.state.githubInput)}>SEND</button>
                {githubList.map((el, index) => {
                    return(
                        <div key={index}>
                            {el.login} / {el.id} / <img src={el.avatar_url} alt="" style={{width: "25px", height: "20px"}} />
                        </div>
                    )
                })}
            </div>
        )
    }
}

```
---