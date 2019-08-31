---
title: "Redux"
date: "2019-08-30"
draft: false
path: "/blog/redux"
---

### Redux is State Management Library.
- Easily have access to state and modify the value of state without passing other components by using reducer in store.
- Redux Alternative : React Context API
- Reducer : Generate new state by using action parameter with logic.
- Generate New State : var newState = Object.assign({}, state, {color:action.color}); or spread operator (not directly modify)
- Retrieve the state value : getState();
- Store : The space including state, reducer, dispatch, subscribe ... 
- Dispatch : implement action (dispatch parameter will go to action parameter in reducer)
- Subscribe : call the function after implemting dispatch (parameter should be function)
- Redux Middleware : Provide some features like logging, reporting error, asynchronous (Redux Thunk, Redux Saga)

---

### Vanila Javascript Redux tutorial
```js
<!DOCTYPE html>
<html>
    <head>
        <!-- Redux CDN -->
        <script src="https://cdnjs.cloudflare.com/ajax/libs/redux/4.0.1/redux.js"></script>
    </head>
    <body>
        <div id="subject"></div>
        <div id="toc"></div>
        <div id="control"></div>
        <div id="content"></div>    
        <script>

            subject = () => {
                document.querySelector('#subject').innerHTML = `
                <header>
                    <h1>WEB</h1>
                    Hello, WEB!
                </header>
                `
            }

            TOC = () => {
                var state = store.getState();
                var i = 0;
                var liTags = '';
                while(i<state.contents.length){
                    liTags = liTags + `
                    <li>
                        <a onclick="
                            event.preventDefault();
                            var action = {type:'SELECT', id:${state.contents[i].id}}
                            store.dispatch(action);
                        " href="${state.contents[i].id}">
                            ${state.contents[i].title}
                        </a>
                    </li>`;
                    i = i + 1;
                }

                document.querySelector('#toc').innerHTML = `
                <nav>
                    <ol>${liTags}</ ol>
                </nav>
                `;
            }

            control = () => {
                document.querySelector('#control').innerHTML = `
                <ul>
                    <li><a onclick="
                        event.preventDefault();
                        store.dispatch({
                            type:'CHANGE_MODE',
                            mode:'create'
                        })
                    " href="/create">create</a></li>
                    <li><input onclick="
                        store.dispatch({
                            type:'DELETE'
                        });
                    " type="button" value="delete"></li>
                </ul>
                `;
            }

            article = () => {
                var state = store.getState();
                if(state.mode === 'create'){
                    document.querySelector('#content').innerHTML = `
                    <article>
                        <form onsubmit="
                            event.preventDefault();
                            var _title = this.title.value;
                            var _desc = this.desc.value;
                            store.dispatch({
                                type:'CREATE',
                                title:_title,
                                desc:_desc
                            })
                        ">
                            <p>
                                <input type="text" name="title" placeholder="title">
                            </p>
                            <p>
                                <textarea name="desc" placeholder="description"></textarea>
                            </p>
                            <p>
                                <input type="submit">
                            </p>
                        </form>
                    </article>
                    `
                } else if(state.mode === 'read'){
                    var i = 0;
                    var aTitle, aDesc;
                    while(i < state.contents.length){
                        if(state.contents[i].id === state.selcted_id) {
                            aTitle = state.contents[i].title;
                            aDesc = state.contents[i].desc;
                            break;
                        }
                        i = i + 1;
                    }
                    document.querySelector('#content').innerHTML = `
                    <article>
                        <h2>${aTitle}</h2>
                        ${aDesc}
                    </article>
                    `
                } else if(state.mode === 'welcome'){
                    document.querySelector('#content').innerHTML = `
                    <article>
                        <h2>Welcome</h2>
                        Hello, Redux!!!
                    </article>
                    `
                }
            }

            reducer = (state, action) => { // calling once from createStore (store is undefined at first)
                if(state === undefined){
                    return {
                        max_id:2,
                        mode:'welcome',
                        selcted_id:2,
                        contents:[
                            {id:1,title:'HTML',desc:'HTML is ..'},
                            {id:2,title:'CSS', desc:'CSS is ..'}
                        ]
                    }
                }

                var newState;
                if(action.type === 'SELECT'){
                    // Should assign the value via assing() or spread operator (not directly assign)
                    newState = Object.assign(
                        {}, 
                        state, 
                        {selcted_id:action.id, mode:'read'});
                } else if(action.type === 'CREATE'){
                    var newMaxId = state.max_id + 1;
                    var newContents = state.contents.concat();
                    newContents.push({id:newMaxId, title:action.title, desc:action.desc});
                    newState = Object.assign({}, state, {
                        max_id:newMaxId,
                        contents:newContents,
                        mode:'read'
                    })
                } else if(action.type === 'DELETE'){
                    var newContents = [];
                    var i = 0;
                    while(i < state.contents.length){
                        if(state.selcted_id !== state.contents[i].id){
                            newContents.push(
                                state.contents[i]
                            );
                        }
                        i = i + 1;
                    }
                    newState = Object.assign({},state, {
                        contents:newContents,
                        mode:'welcome'
                    })
                } else if(action.type === 'CHANGE_MODE'){
                    newState = Object.assign({}, state, {
                        mode:action.mode
                    });
                }
                console.log(action, state, newState);
                return newState;
            }
            // dispatch function calls reducer and action parameter will be the dispatch parameter

            var store = Redux.createStore(reducer); // Call reducer once

            store.subscribe(article); // Apply to UI
            store.subscribe(TOC); // Apply to UI
            subject();
            TOC();
            control();
            article();
        </script>
    </body>
</html>
```
---

### ReactJS Redux tutorial

##### 3 rules
- There is one store in one application.
- Keep state immutable
- The function that causes the change, the reducer, must be a pure function.

##### Required Modules
- redux
- react-redux
- redux-actions (Not necessarily)

---
[Redux Application Github](https://github.com/Cool-Hongsi/Redux-Application)

---

#### index.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

import { createStore } from 'redux';
import rootReducer from './store/modules/index';
import { Provider } from 'react-redux';

// Redux Development Tool
// const devTools = window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__();

const store = createStore(rootReducer); // Call Reducer in rootReducer

// Provider -> Combine ReactJS & store
ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>, document.getElementById('root'));

```

---

#### App.js
```js
import React, { Component } from 'react';

import './App.css';
import PaletteContainer from './containers/PaletteContainer';
import CounterContainer from './containers/CounterContainer';
import WaitingListContainer from './containers/WaitingListContainer';

export default class App extends Component {
  render() {
    return (
      <div className="App">
        <PaletteContainer />
        <CounterContainer />
        <WaitingListContainer />
      </div>
    );
  }
}
```

---

#### store/modules/index.js
```js
import { combineReducers } from 'redux';
import counter from './counter';
import waiting from './waiting';

// Root Reducer
export default combineReducers({
    counter,
    waiting,
    // Other reducers
});
```

---

#### store/modules/counter.js
```js
/* Ducks Pattern (Action & Reducer place in one file) */

// Action Type (Ducks pattern usually uses module name + action)
const CHANGE_COLOR = 'counter/CHANGE_COLOR';
const INCREMENT = 'counter/INCREMENT';
const DECREMENT = 'counter/DECREMENT';

// Action Function
export const changeColor = (color) => {
    console.log("changeColor"); // 3
    return {type : CHANGE_COLOR, color} // 4
};

export const increment = () => {
    console.log("increment");
    return {type : INCREMENT}
};

export const decrement = () => {
    console.log("decrement");
    return {type : DECREMENT}
};

// Define initial state
const initialState = {
    color : 'red',
    number : 0
};

// Reducer
export default function counter(state = initialState, action){ // state = initialState (first time only)
    console.log("counter reducer");
    console.log(state, action);
    
    // if(state === undefined){
    //     return {
    //         color : 'red',
    //         number : 0
    //     }
    // }

    // if(action.type === 'CHANGE_COLOR'){
    //     var newState;
    //     newState = Object.assign({}, state, {
    //         color : action.color
    //     });
    //     return newState;
    // }

    switch(action.type){
        case CHANGE_COLOR:
            console.log("CHANGE_COLOR");
            return {
                ...state,
                color : action.color
            };
        case INCREMENT:
            console.log("INCREMENT");
            return {
                ...state,
                number : state.number + 1
            };
        case DECREMENT:
            console.log("DECREMENT");
            return {
                ...state,
                number : state.number - 1
            };
        default:
            return state;
    }
}

```

---

#### store/modules/waiting.js
```js
/* Use redux-actions */
import { createAction, handleActions } from 'redux-actions';

const CHANGE_INPUT = 'waiting/CHANGE_INPUT';
const CREATE = 'waiting/CREATE';
const ENTER = 'waiting/ENTER';
const LEAVE = 'waiting/LEAVE';

/* FSA Rule -> Created for Readable, Useful, Simple Action Object */
/* Requirement -> Pure Javascript Object, Should have type value, Values (payload, error, meta) */

// export const changeInput = (text) => {
//     return {
//         type : CHANGE_INPUT,
//         payload : text
//     }
// };

// export const create = (text) => {
//     return {
//         type : CREATE,
//         payload : text
//     }
// };

// export const enter = (id) => {
//     return {
//         type : ENTER,
//         payload : id
//     }
// };

// export const leave = (id) => {
//     return {
//         type : LEAVE,
//         payload : id
//     }
// };

/* Same as above action functions with redux-actions */
// First parameter should be type value
// Second parameter should be payload value
let id = 3;

export const changeInput = createAction(CHANGE_INPUT, text => text);
export const create = createAction(CREATE, text => ({ text, id: id++ }));
export const enter = createAction(ENTER, id => id);
export const leave = createAction(LEAVE, id => id);

/*
    Example)
    changeInput("hi");
    { type : CHANGE_INPUT, payload : "hi" }

    create("hello");
    { type : CREATE, payload : { id : 3, text : "hello" }}
    create("bye");
    { type : CREATE, payload : { id : 4, text : "bye" }}
*/

const initialState = {

    input: '',

    list: [
      {
        id: 0,
        name: 'AAA',
        entered: true,
      },
      {
        id: 1,
        name: 'BBB',
        entered: false,
      },
      {
        id: 2,
        name: 'CCC',
        entered: false,
      },
    ],
};
  
// Reducer with handleActions
export default handleActions(
    {
        [CHANGE_INPUT]: (state, action) => ({
            ...state,
            input: action.payload,
        }),
    
        [CREATE]: (state, action) => ({
            ...state,
            list: state.list.concat({
                id: action.payload.id,
                name: action.payload.text,
                entered: false,
            }),
        }),

        [ENTER]: (state, action) => ({
            ...state,
            list: state.list.map(
                item =>
                item.id === action.payload
                    ? { ...item, entered: !item.entered }
                    : item
            ),
        }),

        [LEAVE]: (state, action) => ({
            ...state,
            list: state.list.filter(item => item.id !== action.payload),
        }),
    },
    initialState
);
```

---

#### components/Counter.js
```js
import React from 'react';
import './Counter.css';

const Counter = ({ value, color, onIncrement, onDecrement }) => {
  return (
    <div className="Counter">
      <h1 style={{ color }}>{value}</h1>
      <button onClick={onIncrement}>+</button>
      <button onClick={onDecrement}>-</button>
    </div>
  );
};

export default Counter;

```

---

#### components/Palette.js
```js
import React from 'react';
import './Palette.css';

const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'];

const PaletteItem = ({ color, active, onClick }) => {
  return (
    <div
      className={`PaletteItem ${active ? 'active' : ''}`}
      style={{ backgroundColor: color }}
      onClick={onClick}
    />
  );
};

// <Palette onSelect={this.handleSelect} selected={color} />
const Palette = ({ selected, onSelect }) => {
  return (
    <div className="Palette">
      <h2>Select the colors</h2>
      <div className="colors">
        {colors.map(color => (
          <PaletteItem color={color} key={color} active={selected === color} onClick={() => onSelect(color)}/>
        ))}
      </div>
    </div>
  );
};

export default Palette;

```

---

#### components/WaitingList.js
```js
import React from 'react';
import './WaitingList.css';

const WaitingItem = ({ text, entered, onEnter, onLeave }) => {
  return (
    <li>
      <div className={`text ${entered ? 'entered' : ''}`}>{text}</div>
      <div className="buttons">
        <button onClick={onEnter}>Enter</button>
        <button onClick={onLeave}>Leave</button>
      </div>
    </li>
  );
};

const WaitingList = ({ input, waitingList, onChange, onSubmit, onEnter, onLeave }) => {

  const waitingItems = waitingList.map(w => (
    <WaitingItem key={w.id} text={w.name} entered={w.entered} id={w.id} onEnter={() => onEnter(w.id)} onLeave={() => onLeave(w.id)} />
  ));

  return (
    <div className="WaitingList">
      <h2>LIST</h2>
      <form onSubmit={onSubmit}>
        <input value={input} onChange={onChange} />
        <button>Register</button>
      </form>
      <ul>{waitingItems}</ul>
    </div>
  );
};

export default WaitingList;
```

---

#### containers/CounterContainer.js
```js
import React, { Component } from 'react';
import { connect } from 'react-redux';
// import { bindActionCreators } from 'redux';
import Counter from '../components/Counter';
import { increment, decrement } from '../store/modules/counter';

// export const increment = () => {
//     return {type : INCREMENT}
// };

// export const decrement = () => {
//     return {type : DECREMENT}
// };

class CounterContainer extends Component{
    handleIncrement = () => {
        console.log("handleIncrement");
        this.props.increment();
    }

    handleDecrement = () => {
        console.log("handleIncrement");
        this.props.decrement();
    }

    render(){
        const { color, number } = this.props;
        console.log(color);
        console.log(number);

        return(
            <Counter color={color} value={number} onIncrement={this.handleIncrement} onDecrement={this.handleDecrement} />
        )
    }
}

const mapStateToProps = (state) => {
    console.log("mapStateToProps");
    return {
        color : state.counter.color,
        number : state.counter.number
    }
}

const mapDispatchToProps = (dispatch) => ({
    increment: () => dispatch(increment()),
    decrement: () => dispatch(decrement())
})

// Same as above mapDispatchToProps
// const mapDispatchToProps = (dispatch) =>
//   bindActionCreators({ increment, decrement }, dispatch);

// Same as above mapDispatchToProps
// const mapDispatchToProps = { increment, decrement };

export default connect(
    mapStateToProps,
    mapDispatchToProps
)(CounterContainer)

```

---

#### containers/PaletteContainer.js
```js
import React, { Component } from 'react';
import { connect } from 'react-redux'; // In order to create Container Component
import Palette from '../components/Palette';
import { changeColor } from '../store/modules/counter';
// export const changeColor = (color) => ({type : CHANGE_COLOR, color});

/* Component with redux -> Container Component */
/* Component only for transferring props and focus on view -> Presentational Component */

class PaletteContainer extends Component{
    handleSelect = (color) => {
        const { changeColor } = this.props; // Implement mapDispatchToProps
        console.log('what'); // 1
        changeColor(color); // 2 // returnning object from changeColor(color) will go to dispatch parameter (action) in mapDispatchToProps
    }

    render(){
        console.log("PaletteContainer");
        const { color } = this.props;
        console.log(color);
        return(
            <Palette onSelect={this.handleSelect} selected={color} />
        )
    }
}

// state in store that will be put in props
/* present state parameter */
/* whenever the value of state is changed, it is called */
const mapStateToProps = (state) => {
    console.log("mapStateToProps");
    return {color : state.counter.color}
}
/* First parameter in connect() -> mapStateToProps */
/* PaletteContainer Component can use this.props.color */


// action function that will be put props
/* whenever changeColor(color) is called, implement this */
const mapDispatchToProps = (dispatch) => {
    console.log("mapDispatchToProps");
    return {changeColor : color => dispatch(changeColor(color))}
}
/* Second parameter in connect() -> mapDispatchToProps */
/* PaletteContainer Component can use this.props.changeColor */

// Combine redux store with components
export default connect(
    mapStateToProps, // transfer state as props
    mapDispatchToProps // transfer action function as props
)(PaletteContainer);
// In PaletteContainer Component, this.props.color / this.props.changeColor

```

---

#### containers/WaitingListContainer.js
```js
import React, { Component } from 'react';
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';
import * as waitingActions from '../store/modules/waiting';
import WaitingList from '../components/WaitingList';

class WaitingListContainer extends Component {

  handleChange = e => {
    const { WaitingActions } = this.props;
    WaitingActions.changeInput(e.target.value);
  };

  handleSubmit = e => {
    e.preventDefault();
    const { WaitingActions, input } = this.props;
    WaitingActions.create(input);
    WaitingActions.changeInput(''); // initialize input value
  };
  
  handleEnter = id => {
    const { WaitingActions } = this.props;
    WaitingActions.enter(id);
  };

  handleLeave = id => {
    const { WaitingActions } = this.props;
    WaitingActions.leave(id);
  };

  render() {

    const { input, list } = this.props;

    return (
      <WaitingList
        input={input}
        waitingList={list}
        onChange={this.handleChange}
        onSubmit={this.handleSubmit}
        onEnter={this.handleEnter}
        onLeave={this.handleLeave}
      />
    );
  }
}

const mapStateToProps = ({ waiting }) => ({
    input: waiting.input,
    list: waiting.list,
});

const mapDispatchToProps = dispatch => ({
    WaitingActions: bindActionCreators(waitingActions, dispatch),
});

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(WaitingListContainer);
```

---