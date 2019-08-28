---
title: "Redux"
date: "2019-08-25"
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

#### Vanila Javascript Redux tutorial
```js
<!-- Redux CDN -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/redux/4.0.4/redux.js"></script>

<script>
/* Redux */

function reducer(state, action){
    if(state === undefined){ // calling once from createStore (store is not defined)
        return {color : 'yellow'}
    }

    var newState;
    if(action.type === 'CHANGE_COLOR'){
        newState = Object.assign({}, state, {color:action.color});
        // Should assign the value via assing() (not directly assign)
    }

    console.log(action, state, newState);

    return newState;
}

// Create store
var store = Redux.createStore(reducer); // Call reducer once

function red(){
    var newColor = store.getState();
    document.querySelector("#red").innerHTML = 
    `
        <div class="container" id="red-container" style="background-color : ${newColor.color}">
            <h1>Red</h1>
            <input type="button" value="Fire" onclick="
                store.dispatch({type:'CHANGE_COLOR', color:'red'});
            " />
        </div>
    `;
}
// dispatch function calls reducer and action parameter will be the dispatch parameter
// action parameter = {type:'CHANGE_COLOR', color:'red'}


store.subscribe(red); // Apply to UI
red();
```
---

#### ReactJS Redux tutorial (Comming Soon !)
```js

```