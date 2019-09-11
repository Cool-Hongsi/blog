---
title: "Github Cart (React / Redux / Jest / Enzyme / SCSS)"
date: "2019-09-10"
draft: false
path: "/blog/github-cart"
---

### Introduction
This application is motivated on shopping cart. Used ReactJS / Redux / Jest / Enzyme / SCSS. The main points for this application is that firstly, used axios to receive the github information via github API with ReduxThunk (Async). Secondly, by using Jest, test Component, Action Functions, PropsType and Integration for store.

---

### Process
- In main page, once a user input github ID, the primary information for github ID will be printed out on front page and there is 'Link' button that can open the repository for github ID.
- In addition, there is a cart button. If clicks this button, then the information the user clicks will be stored in cart.
- In cart page, able to increment or decrease the number of quantity of github ID as well as possible to remove.
- The number of quantity and total quantity will be processed as long as the user controls the buttons.
- Testing React and Redux application by using Jest and Enzyme (Integration / checkPropTypes / checkActionFunction / checkComponent)
- It consists 5 test files and 12 test modules.
- Verify prop types / Verify Action function / Verify Component / Integration
- This application is using axios to receive the data from API and integration test in order to check store is working properly via moxios module.

---

[Github-Cart](https://github.com/Cool-Hongsi/Github-Cart)

---

#### Dependencies
```js
    "axios": "^0.19.0",
    "check-prop-types": "^1.1.2",
    "enzyme": "^3.10.0",
    "enzyme-adapter-react-16": "^1.14.0",
    "moxios": "^0.4.0",
    "node-sass": "^4.12.0",
    "prop-types": "^15.7.2",
    "react": "^16.8.6",
    "react-dom": "^16.9.0",
    "react-redux": "^7.0.0-beta.0",
    "react-router-dom": "^5.0.1",
    "react-scripts": "3.1.1",
    "redux": "^4.0.4",
    "redux-thunk": "^2.3.0"
```

---

#### Configuration
```js
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() });
```
---

#### index.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.scss';
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
import './App.scss';

import { BrowserRouter, Route, Switch } from 'react-router-dom';
import MainContainer from './containers/MainContainer';
import GithubContainer from './containers/GithubContainer';
import CartContainer from './containers/CartContainer';

export default class App extends Component{
  render(){
    return(
      <BrowserRouter>
        <div className="App">

          <MainContainer />

          <Switch>
            <Route exact path="/" component={GithubContainer} />
            <Route path="/cart" component={CartContainer} />
          </Switch>
        </div>
      </BrowserRouter>
    )
  }
}
```
---

#### Utils/index.js
```js
import checkPropTypes from 'check-prop-types';
import { createStore, applyMiddleware } from 'redux';
import rootReducer from '../store/modules/index';
import ReduxThunk from 'redux-thunk';

export const checkProps = (component, expectedProps) => {
    const propsErr = checkPropTypes(component.propTypes, expectedProps, 'props', component.name);
    return propsErr;
};

export const findByTestAttr = (component, attr) => {
    const wrapper = component.find(`[data-test='${attr}']`);
    return wrapper;
};

export const testStore = (initialState) => {
    const createStoreWithMiddleware = applyMiddleware(ReduxThunk)(createStore);
    return createStoreWithMiddleware(rootReducer, initialState);
};
```
---

#### store/modules/index.js
```js
import { combineReducers } from 'redux';
import cart from './cart';

// Root Reducer
export default combineReducers({
    cart,
    // Other Reducers
});
```
---

#### store/modules/action.js
```js
/* GithubContainer */
export const GET_POST_PEDNING = 'GET_POST_PENDING';
export const GET_POST_SUCCESS = 'GET_POST_SUCCESS';
export const GET_POST_FAILURE = 'GET_POST_FAILURE';
export const ADD_CART = 'ADD_CART';

/* CartContainer */
export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';
export const REMOVE = 'REMOVE';
```

---

#### store/modules/cart.js
```js
import axios from 'axios';
import { GET_POST_PEDNING, GET_POST_SUCCESS, GET_POST_FAILURE, ADD_CART, INCREMENT, DECREMENT, REMOVE } from './action';

/* Action Functions */
export const getPostAPI = (githubID) => {
    return axios.get(`https://api.github.com/users/${githubID}`);
};

export const getPost = (githubID) => (dispatch) => {
    dispatch( { type : GET_POST_PEDNING } );

    return getPostAPI(githubID).then((res) => {
        dispatch( { type : GET_POST_SUCCESS, payload : res } );
    }).catch((err) => {
        dispatch( { type : GET_POST_FAILURE, payload : err } );
    })
};

export const addCart = (addGithub) => (dispatch) => {
    dispatch( { type : ADD_CART, payload : addGithub } );
};

export const increment = (data) => {
    return { type : INCREMENT, payload : data };
};

export const decrement = (data) => {
    return { type : DECREMENT, payload : data };
};

export const remove = (data) => {
    return { type : REMOVE, payload : data };
};

export const initialState = {
    pending : false,
    error : false,
    repeated : false,
    data : [
        {
            login : 'Cool-Hongsi',
            id : 39789641,
            avatar_url : 'https://avatars3.githubusercontent.com/u/39789641?v=4',
            public_repos : '31',
            html_url : 'https://github.com/Cool-Hongsi',
            quantity : 0
        }
    ],
    total : 0
    // cartData : [
    //     {
    //         login : '',
    //         id : '',
    //         avatar_url : '',
    //         public_repos : '',
    //         quantity : 0
    //     }
    // ]
};

/* Cart Reducer */
export default function cart(state = initialState, action) {

    switch(action.type){

        case GET_POST_PEDNING:
            return { ...state, pending : true, error : false, repeated : true };

        case GET_POST_SUCCESS:
            let repeatedFlag = false; // To check whether added data is already exist or not
            // If I use the 'map' method, it will create array
            for(let i=0; i<state.data.length; i++){
                if(state.data[i].id === action.payload.data.id){
                    repeatedFlag = true;
                    break; // Once it finds the repeated value, then stop for looping
                }
                else{
                    repeatedFlag = false;
                }
            }
            if(repeatedFlag === true){
                return { ...state, pending : false, error : false, repeated : true };
            }
            else{
                return { ...state, pending : false, error : false, repeated : false, data : state.data.concat({
                    login : action.payload.data.login,
                    id : action.payload.data.id,
                    avatar_url : action.payload.data.avatar_url,
                    public_repos : action.payload.data.public_repos,
                    html_url : action.payload.data.html_url,
                    quantity : 0
                }) };
            }

        case GET_POST_FAILURE:
            return { ...state, pending : false, error : true, repeated : false };

        case ADD_CART:
            state.data.map((el) => {
                if(el.id === action.payload.id){
                    el.quantity++;
                }
                return { ...state, pending : false, error : false, repeated : false, total : state.total + 1 };
            });
            return { ...state, pending : false, error : false, repeated : false, total : state.total + 1 };

            // state.cartData.map((el) => {
            //     if(el.id === action.payload.id){
            //         el.quantity += 1;
            //         return { ...state, pending : false, error : false, total : state.total + 1 };
            //     }
            // });
            // return { ...state, pending : false, error : false, total : state.total + 1, cartData : state.cartData.concat({
            //     login : action.payload.login,
            //     id : action.payload.id,
            //     avatar_url : action.payload.avatar_url,
            //     public_repos : action.payload.public_repos,
            //     quantity : action.count + 1
            // })}

        case INCREMENT:
            state.data.map((el) => {
                if(el.id === action.payload.id){
                    el.quantity++;
                }
                return { ...state, pending : false, error : false, total : state.total + 1 };
            })
            return { ...state, pending : false, error : false, total : state.total + 1 };

        case DECREMENT:
            state.data.map((el) => {
                if(el.id === action.payload.id){
                    el.quantity--;
                }
                return { ...state, pending : false, error : false, total : state.total - 1 };
            })
            return { ...state, pending : false, error : false, total : state.total - 1 };

        case REMOVE:
            var elQuantity = 0;
            state.data.map((el) => {
                if(el.id === action.payload.id){
                    elQuantity = el.quantity;
                    el.quantity = 0;
                    // If I do below, the data in array will be deleted. Then it can not show in Mainpage.
                    // Alternatively, make the value of quantity as '0' in order to remain the data in Mainpage and not show in cart page
                    // let index = state.data.indexOf(el);
                    // state.data.splice(index, 1);                    
                }   
                return { ...state, pending : false, error : false, total : state.total - elQuantity };
            })
            return { ...state, pending : false, error : false, total : state.total - elQuantity };
            
        default:
            return state
    }
};
```
---

#### store/modules/cart.spec.js
```js
import { GET_POST_PENDING, GET_POST_SUCCESS, GET_POST_FAILURE, ADD_CART, INCREMENT, DECREMENT, REMOVE } from './action';
import { initialState } from './cart';
import cart from './cart';
import axios from 'axios';

describe('Cart Reducer', () => {

    it('Should return default state', () => {
        const newState = cart(undefined, {});

        expect(newState).toEqual(initialState);
    });

    it('Should return new state if receiving GET_POST_PENDING', () => {
        const GET_POST_PENDING_VALUE = initialState;

        const newState = cart(undefined, {
            type : GET_POST_PENDING,
            payload : GET_POST_PENDING_VALUE
        });

        expect(newState).toEqual(GET_POST_PENDING_VALUE);
    });

    it('Should return new state if receiving GET_POST_SUCCESS', () => {
        const newState = initialState;

        axios.get(`https://api.github.com/users/Cool-Hongsi`).then((res) => {
            newState = cart(undefined, {
                type : GET_POST_SUCCESS,
                payload : res
            });
        });

        const GET_POST_SUCCESS_VALUE = initialState;
        GET_POST_SUCCESS_VALUE.data.concat(newState);

        expect(newState).toEqual(GET_POST_SUCCESS_VALUE);
    });

    it('Should return new state if receiving GET_POST_FAILURE', () => {
        const GET_POST_FAILURE_VALUE = initialState;
        GET_POST_FAILURE_VALUE.error = true;

        const newState = cart(undefined, {
            type : GET_POST_FAILURE,
            payload : GET_POST_FAILURE_VALUE
        });

        expect(newState).toEqual(GET_POST_FAILURE_VALUE);
    });

    it('Should return new state if receiving ADD_CART', () => {
        const ADD_CART_VALUE = initialState;
        ADD_CART_VALUE.error = false;

        const newState = cart(undefined, {
            type : ADD_CART,
            payload : ADD_CART_VALUE
        });

        ADD_CART_VALUE.total += 1;

        expect(newState).toEqual(ADD_CART_VALUE);
    });

    it('Should return new state if receiving INCREMENT', () => {
        const INCREMENT_VALUE = initialState;

        const newState = cart(undefined, {
            type : INCREMENT,
            payload : INCREMENT_VALUE
        });

        INCREMENT_VALUE.total += 1;

        expect(newState).toEqual(INCREMENT_VALUE);
    });

    it('Should return new state if receiving DECREMENT', () => {
        const DECREMENT_VALUE = initialState;

        const newState = cart(undefined, {
            type : DECREMENT,
            payload : DECREMENT_VALUE
        });

        DECREMENT_VALUE.total -= 1;

        expect(newState).toEqual(DECREMENT_VALUE);
    });

    it('Should return new state if receiving REMOVE', () => {
        const REMOVE_VALUE = initialState;

        const newState = cart(undefined, {
            type : REMOVE,
            payload : REMOVE_VALUE
        });

        expect(newState).toEqual(REMOVE_VALUE);
    });
});

```
---

#### containers/MainContainer.js
```js
import React, { Component } from 'react';
import Navbar from '../components/Navbar';

export default class MainContainer extends Component{
    render(){
        return(
            <div>
                <Navbar />
                {this.props.children}
            </div>
        )
    }
}
```
---

#### containers/CartContainer.js
```js
import React, { Component } from 'react';
import { connect } from 'react-redux';
import Cart from '../components/Cart';
import { increment, decrement, remove } from '../store/modules/cart';

class CartContainer extends Component{

    onSelectedIncrement = (data) => {
        const { increment } = this.props;
        increment(data);
    };

    onSelectedDecrement = (data) => {
        const { decrement } = this.props;
        decrement(data);
    };

    onSelectedRemove = (data) => {
        const { remove } = this.props;
        remove(data);
    };

    render(){

        const { total, data } = this.props;

        /* As long as the quantity value of state data array is 0, can NOT show it to the page */
        let newData = data.filter((el) => {
            return el.quantity > 0;
        });

        return(
            <Cart
                total={total}
                newData={newData}
                onSelectedIncrement={this.onSelectedIncrement}
                onSelectedDecrement={this.onSelectedDecrement}
                onSelectedRemove={this.onSelectedRemove}
            />
        )
    }
};

const mapStateToProps = (state) => {
    return {
        total : state.cart.total,
        data : state.cart.data
    }
};

const mapDispatchToProps = (dispatch) => {
    return {
        increment : (data) => { dispatch(increment(data)) },
        decrement : (data) => { dispatch(decrement(data)) },
        remove : (data) => { dispatch(remove(data)) }
    }
};

export default connect(
    mapStateToProps,
    mapDispatchToProps
)(CartContainer);
```
---

#### containers/GithubContainer.js
```js
import React, { Component } from 'react';
import { connect } from 'react-redux';
import Github from '../components/Github';
import { getPost, addCart } from '../store/modules/cart';

class GithubContainer extends Component{

    // componentDidMount = () => {
    //     const { number, getPost } = this.props;
    //     getPost(number);
    // };

    // componentWillReceiveProps = (nextProps) => {
    //     const { number, getPost } = this.props;
    //     if(number !== nextProps.number){
    //         getPost(nextProps.number);
    //     }
    // };

    onSelectedID = (githubID) => {
        const { getPost } = this.props;
        getPost(githubID);
    };

    onSelectedCart = (addGithub) => {
        const { addCart } = this.props;
        addCart(addGithub);
    };

    render(){

        const { pending, error, repeated, data } = this.props;

        return(
            <Github
                pending={pending}
                error={error}
                repeated={repeated}
                data={data}
                onSelectedID={this.onSelectedID}
                onSelectedCart={this.onSelectedCart}
            />
        )
    }
}

const mapStateToProps = (state) => {
    return {
        pending : state.cart.pending,
        error : state.cart.error,
        repeated : state.cart.repeated,
        data : state.cart.data
    }
};

const mapDispatchToProps = (dispatch) => {
    return {
        getPost : (githubID) => { dispatch(getPost(githubID)) },
        addCart : (addGithub) => { dispatch(addCart(addGithub)) }
    }
};

export default connect(
    mapStateToProps,
    mapDispatchToProps
)(GithubContainer);
```
---

#### components/Navbar.js
```js
import React, { Component } from 'react';
import { connect } from 'react-redux';
import { Link } from 'react-router-dom'
import PropTypes from 'prop-types';

import './scss/Navbar.scss';

class Navbar extends Component{
    render(){

        const { total } = this.props;

        return(
            <div className="navbar" data-test="navbarComponent">
                <nav>
                    <input type="checkbox" id="nav" className="hidden" />
                    <label htmlFor="nav" className="nav-btn">
                        <i></i>
                        <i></i>
                        <i></i>
                    </label>

                    <div className="logo">
                        <a href="/">Github Cart</a>
                    </div>

                    <div className="nav-wrapper" data-test="navwrapper">
                        <ul>
                            <li><Link to="/">Home</Link></li>
                            <li><Link to="/cart">Cart ({total})</Link></li>
                            
                            {/* 'a' link reload the page (In other words, the value of state will be initialized) 
                            Alternatively, 'Link' shows the page without reloading. So, it is possible to retain the value of state */}

                        </ul>
                    </div>
                </nav>
            </div>
        )
    }
}

const mapStateToProps = (state) => {
    return {
        total : state.cart.total
    }
};

export default connect(
    mapStateToProps
)(Navbar);

// Validate props
Navbar.propTypes = {
    total : PropTypes.number
};
```
---

#### components/Navbar.spec.js
```js
import React from 'react';
// import { shallow } from 'enzyme';
import Navbar from './Navbar';
import { checkProps } from '../Utils/index';
// import { checkProps, findByTestAttr } from '../Utils/index';

// setUp function usually stay in test file becuase of initial setting up function (shallow)
// const setUp = (props={}) => {
//     const component = shallow(<Navbar {...props} />).childAt(0).dive(); // shallow function acts as rendering <Header /> Component
//     // console.log(component.debug()); // check what shallow function is doing
//     return component;
//     in order to use shallow method from enzyme,
//     should replace the version of 'react' , 'react-redux' from package.json with previous version.
// };

describe('Navbar component', () => {

    describe('Checking PropTypes', () => {
        it('Should not throw an error or warning', () => {
            const expectedProps = {
                total: 100
            };

            const propsErr = checkProps(Navbar, expectedProps);
            expect(propsErr).toBeUndefined();
        })
    });

    // describe('Have props', () => {
    //     let wrapper;
    //     beforeEach(() => {
    //         const props = {
    //             title : 123
    //         };

    //         wrapper = setUp(props);
    //     });

    //     it('Should render without errors', () => {
    //         const component = findByTestAttr(wrapper, 'navbarComponent');
    //         expect(component.length).toBe(1);
    //     });

    //     it('Should render a nav-wrapper', () => {
    //         const navwrapper = findByTestAttr(wrapper, 'navwrapper');
    //         expect(navwrapper.length).toBe(1);
    //     });
    // });
});
```
---

#### components/Cart.js
```js
import React from 'react';
import './scss/Cart.scss';
import PropTypes from 'prop-types';

const Cart = ( { total, newData, onSelectedIncrement, onSelectedDecrement, onSelectedRemove } ) => {
    return(
        <div className="cart-page">
            <table className="cart-table">
                <caption>Total : {total}</caption>
                <thead></thead>
                <tbody>
                    <tr>
                        <th>Login</th>
                        <th>ID</th>
                        <th>Repo</th>
                        <th>Image</th>
                        <th>Link</th>
                        <th>quantity</th>
                        <th>Inc</th>
                        <th>Dec</th>
                        <th>Remove</th>
                    </tr>
                    {newData.map((el, index) => {
                        return(
                            <tr key={index}>
                                <td>{el.login}</td>
                                <td>{el.id}</td>
                                <td>{el.public_repos}</td>
                                <td><img src={el.avatar_url} alt="" style={{width:"25px", height: "25px"}} /></td>
                                <td><a href={el.html_url} target="_blank" rel="noopener noreferrer">Click</a></td>
                                <td>{el.quantity}</td>
                                <td><button onClick={() => onSelectedIncrement(el)}>+</button></td>
                                <td><button onClick={() => onSelectedDecrement(el)}>-</button></td>
                                <td><button onClick={() => onSelectedRemove(el)}>X</button></td>
                            </tr>
                        )
                    })}
                </tbody>
            </table>
        </div>
    )
};

export default Cart;

// Validate props
Cart.propTypes = {
    total : PropTypes.number,
    newData : PropTypes.arrayOf(PropTypes.shape({
        login : PropTypes.string,
        id : PropTypes.number,
        avatar_url : PropTypes.string,
        public_repos : PropTypes.string,
        html_url : PropTypes.string,
        quantity : PropTypes.number
    }))
};

```
---

#### components/Cart.spec.js
```js
// import React from 'react';
// import { shallow } from 'enzyme';
import Cart from './Cart';

import { checkProps } from '../Utils/index';

describe('Cart component', () => {

    describe('Checking PropTypes', () => {
        it('Should not throw an error or warning', () => {
            const expectedProps = {
                total : 123,
                newData : [
                    {
                        login : "string",
                        id : 123,
                        avatar_url : "string",
                        public_repos : "string",
                        html_url : "string",
                        quantity : 123
                    }
                ]
            };

            const propsErr = checkProps(Cart, expectedProps);
            expect(propsErr).toBeUndefined();
        })
    });

});

```
---

#### components/Github.js
```js
import React, { Component } from 'react';
import './scss/Github.scss';
import PropTypes from 'prop-types';

export default class Github extends Component{

    constructor(props){
        super(props);

        this.state = {
            id : ''
        }

        this.setData = this.setData.bind(this);
        this.keyPress = this.keyPress.bind(this);
    };

    setData = (e) => {
        this.setState({
            id : e.target.value
        })
    };

    keyPress = (e) => {
        if(e.key === 'Enter'){
            this.props.onSelectedID(this.state.id);
        }
    };

    render(){

        const { pending, error, repeated, data, onSelectedID, onSelectedCart } = this.props;

        return(
            <div className="github">
                <h1 className="github-title">Search Github ID</h1>
                <input className="github-input" type="text" placeholder="Github ID" onChange={this.setData} onKeyPress={this.keyPress} />
                <button className="github-button" onClick={() => onSelectedID(this.state.id)}>SEND</button>
                <br/><br/><br/>
                {pending ? <h3 className="github-loading">Loading...</h3> : null}
                {repeated ? <h3 className="github-repeated">You have already added.</h3> : null}
                {error ? <h3 className="github-error">Error... (Please put correct ID)</h3> : (
                    <div className="github-data">
                        <table className="github-table">
                            <thead></thead>
                            <tbody>
                                <tr>
                                    <th>Login</th>
                                    <th>ID</th>
                                    <th>Repo</th>
                                    <th>Image</th>
                                    <th>Link</th>
                                    <th>Cart</th>
                                </tr>
                                {data.map((el, index) => {
                                    return(
                                        <tr key={index}>
                                            <td>{el.login}</td>
                                            <td>{el.id}</td>
                                            <td>{el.public_repos}</td>
                                            <td><img src={el.avatar_url} alt="" style={{width:"25px", height: "25px"}} /></td>
                                            <td><a href={el.html_url} target="_blank" rel="noopener noreferrer">Click</a></td>
                                            <td><button onClick={() => onSelectedCart(el)}>Cart</button></td>
                                        </tr>
                                    )
                                })}
                            </tbody>
                        </table>
                    </div>
                )}
            </div>
        )
    }
}

// Validate props
Github.propTypes = {
    pending : PropTypes.bool,
    error : PropTypes.bool,
    repeated : PropTypes.bool,
    data : PropTypes.arrayOf(PropTypes.shape({
        login : PropTypes.string,
        id : PropTypes.number,
        avatar_url : PropTypes.string,
        public_repos : PropTypes.string,
        html_url : PropTypes.string,
        quantity : PropTypes.number
    }))
};
```
---

#### components/Github.spec.js
```js
// import React from 'react';
// import { shallow } from 'enzyme';
import Github from './Github';

import { checkProps } from '../Utils/index';

describe('Github component', () => {

    describe('Checking PropTypes', () => {
        it('Should not throw an error or warning', () => {
            const expectedProps = {
                pending : false,
                error : false,
                repeated : false,
                data : [
                    {
                        login : "string",
                        id : 123,
                        avatar_url : "string",
                        public_repos : "string",
                        html_url : "string",
                        quantity : 123
                    }
                ]
            };

            const propsErr = checkProps(Github, expectedProps);
            expect(propsErr).toBeUndefined();
        })
    });

});

```
---

#### integration.spec.js
```js
import moxios from 'moxios';
import { testStore } from '../Utils/index';
import { getPost } from '../store/modules/cart';

describe('gethPost action', () => {

    beforeEach(() => {
        moxios.install();
    });

    afterEach(() => {
        moxios.uninstall();
    });

    test('Store is updated correctly', () => {
        const expectedState = {
            pending : false,
            error : false,
            repeated : false,
            data : [
                {
                    login : 'Cool-Hongsi',
                    id : 39789641,
                    avatar_url : 'https://avatars3.githubusercontent.com/u/39789641?v=4',
                    public_repos : '31',
                    html_url : 'https://github.com/Cool-Hongsi',
                    quantity : 0
                },
                {
                    login : undefined,
                    id : undefined,
                    avatar_url : undefined,
                    public_repos : undefined,
                    html_url : undefined,
                    quantity : 0
                }
            ],
            total : 0
        };

        const store = testStore();
        moxios.wait(() => {
            const request = moxios.requests.mostRecent();
            request.respondWith({
                status : 200,
                response : expectedState
            })
        });

        return store.dispatch(getPost()).then(() => {
            const newState = store.getState();
            expect(newState.cart).toStrictEqual(expectedState);
        });
        
    });

});
```
---

#### Navbar.scss
```scss
nav{
    padding: 8px;

    ul{
        float: right;

        li{
            display: inline-block;

            a{
                display: inline-block;
                outline: none;
                color:#000;
                font-size: 14px;
                font-weight: 600;
                letter-spacing: 1.2px;
                text-transform: uppercase;
                text-decoration: none;
            }
        }

        li:not(:first-child){
            margin-left: 48px;
        }

        li:last-child{
            margin-right: 24px;
        }
    }
}

.logo{
    float: left;
    padding: 8px;
    margin: 8px 0px 0px 16px;

    a{
        color: #000;
        font-size: 18px;
        font-weight: 600;
        letter-spacing: 0px;
        text-transform: uppercase;
        text-decoration: none;
    }
}

@media screen and (max-width: 864px){
    .logo{
        padding: 8px 0px 0px 0px;
    }

    .nav-wrapper{
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        z-index: -1;
        opacity: 0;
        background: #fff;
        transition: all .2s ease;
        -webkit-transition: all .2s ease;
        -moz-transition: all .2s ease;
        -ms-transition: all .2s ease;

        ul{
            position: absolute;
            width: 100%;
            top: 50%;
            transform: translateY(-50%);
            -webkit-transform: translateY(-50%);
            -moz-transform: translateY(-50%);
            -ms-transform: translateY(-50%);

            li{
                display: block;
                float: none;
                width: 100%;
                text-align: center;
                margin-bottom: 10px;

                a{
                    padding: 10px 24px;
                    opacity: 0;
                    color: #000;
                    font-size: 14px;
                    font-weight: 600;
                    letter-spacing: 1.2px;
                    transform: translateX(-20px);
                    -webkit-transform: translateX(-20px);
                    -moz-transform: translateX(-20px);
                    -ms-transform: translateX(-20px);
                    transition: all .2s ease;
                    -webkit-transition: all .2s ease;
                    -moz-transition: all .2s ease;
                    -ms-transition: all .2s ease;
                }
            }

            @mixin transition-delay($time){
                transition-delay: $time;
                -webkit-transition-delay: $time;
                -moz-transition-delay: $time;
                -ms-transition-delay: $time;
            }

            li:nth-child(1){
                a{
                    @include transition-delay(.2s);
                }
            }

            li:nth-child(2){
                a{
                    @include transition-delay(.3s);
                }
            }

            li:nth-child(3){
                a{
                    @include transition-delay(.4s);
                }
            }

            li:nth-child(4){
                a{
                    @include transition-delay(.5s);
                }
            }

            li:not(:first-child){
                margin-left: 0px;
            }
        }
    }

    .nav-btn{
        position: fixed;
        display: block;
        top: 10px;
        right: 10px;
        width: 48px;
        height: 48px;
        z-index: 9999;
        border-radius: 50%;
        cursor: pointer;

        i{
            display: block;
            width: 20px;
            height: 2px;
            border-radius: 2px;
            background: #000;
            margin-left: 14px;
        }

        i:nth-child(1){
            margin-top: 16px;
        }

        i:nth-child(2){
            margin-top: 4px;
            height: 1.5px;
            opacity: 1;
        }

        i:nth-child(3){
            margin-top: 4px;
        }
    }
}

#nav:checked + {

    @mixin rotate-first($value){
        transform: rotate($value);
        -webkit-transform: rotate($value);
        -moz-transform: rotate($value);
        -ms-transform: rotate($value);
    }

    .nav-btn{
        @include rotate-first(45deg);

        i{
            background: #000;
            transition: transform .2s ease;
            -webkit-transition: transform .2s ease;
            -moz-transition: transform .2s ease;
            -ms-transition: transform .2s ease;
        }

        @mixin rotate-second($value1, $value2){
            transform: translateY($value1) rotate($value2);
            -webkit-transform: translateY($value1) rotate($value2);
            -moz-transform: translateY($value1) rotate($value2);
            -ms-transform: translateY($value1) rotate($value2);
        }

        i:nth-child(1){
            @include rotate-second(6px, 180deg);
        }

        i:nth-child(2){
            opacity: 0;
        }

        i:nth-child(3){
            @include rotate-second(-6px, 90deg);
        }
    }
}

#nav:checked ~ {
    .nav-wrapper{
        z-index: 9990;
        opacity: 1;

        ul li a{
            opacity: 1;
            transform: translateX(0);
            -webkit-transform: translateX(0);
            -moz-transform: translateX(0);
            -ms-transform: translateX(0);
        }
    }
}

.hidden{
    display: none;
}
```
---
