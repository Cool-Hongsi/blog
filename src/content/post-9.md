---
title: "Redux"
date: "2019-08-28"
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

#### ReactJS Redux tutorial (Comming Soon !)
```js

```