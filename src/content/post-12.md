---
title: "Pixabay API (React / Redux / SCSS)"
date: "2019-10-18"
draft: false
path: "/blog/pixabay-api"
---

### Introduction
This application is created to receive the pictures from Pixabay API and make it possible to store what the user saves pictures. Used ReactJS / Redux / SCSS. The main points for this application is that used axios to receive the picture information via Pixabay API with ReduxThunk (Async).

---

### Process
- Once you start this application, you are able to see the title, search bar and My Collection button.
- Please type keywords in search bar and type 'Enter Key' or click search button.
- Some of pictures associated with the keywords will be showed.
- If you mouse over to one of the pictures, then there will be save button in the center of pictures.
- If you click the save button, the the picture will be stored into My Collection.
- If you click the My Collection button, then you are able to see the pictures that you saved.
- If you would like to search pictures more, then click the 'Go To Search' button again.

---

[Pixabay-API](https://github.com/Cool-Hongsi/Pixabay-API)

---

#### Dependencies
```js
    "axios": "^0.19.0",
    "bootstrap": "^4.3.1",
    "node-sass": "^4.12.0",
    "react": "^16.10.2",
    "react-bootstrap": "^1.0.0-beta.14",
    "react-dom": "^16.10.2",
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
import 'bootstrap/dist/css/bootstrap.min.css'; // In order to use Bootstrap

import { createStore, applyMiddleware } from 'redux';
import rootReducer from './store/modules/index';
import { Provider } from 'react-redux';
import ReduxThunk from 'redux-thunk'; // Redux Middleware 'Redux Thunk' (In order to use async (axios))

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
import PixabayContainer from './container/PixabayContainer';
import './App.scss';

export default class App extends Component{
  render(){
    return(
      <div className="app">
        <PixabayContainer />
      </div>
    )
  }
}
```
---

#### store/modules/index.js
```js
import { combineReducers } from 'redux';
import pixabayReducer from './pixabay';
import pixabayMyCollectionReducer from './mycollection';

export default combineReducers({
    pixabayReducer,
    pixabayMyCollectionReducer
    // Other Reducers ...
});
```
---

#### store/modules/actions/action.js
```js
/* pixabayReducer */
export const GET_PICTURES = 'GET_PICTURES';

/* pixabayMyCollectionReducer */
export const SELECTED_PICTURES = 'SELECTED_PICTURES';
```

---

#### store/modules/pixabay.js
```js
import axios from 'axios';
import { GET_PICTURES } from './actions/action';

/* In order to receive pictures data from Pixabay API, it requires to put the paramAPIKey (From PixabayContainer) */
export const getPostAPI = (paramContent, paramAPIKey) => {
    return axios.get(`https://pixabay.com/api/?key=${paramAPIKey}&q=${paramContent}`);
};

export const getPictures = (content, APIKey) => (dispatch) => {
    return getPostAPI(content, APIKey).then((res) => {
        dispatch( { type : GET_PICTURES, payload : res.data } );
    }).catch((err) => {
        console.log(err);
    });
};

export default function pixabayReducer(state = [], action){
    switch(action.type){
        case GET_PICTURES:
            return { pixabayList : action.payload };
        
        default:
            return state;
    }
};
```
---

#### store/modules/mycollection.js
```js
import { SELECTED_PICTURES } from './actions/action';

export const selectedPictures = (selectedPic, selectedId) => {
    return { type : SELECTED_PICTURES, url : selectedPic, id : selectedId };
};

const initialMyCollection = {
    data : [
        {
            url : '',
            id : ''
        }
    ]
}

export default function pixabayMyCollectionReducer(state = initialMyCollection, action){
    switch(action.type){
        case SELECTED_PICTURES:

            let repeatedFlag = false;
            // To check whether added data is already exist or not
            // If I use the 'map' method, it will create array

            for(let i=0; i<state.data.length; i++){
                if(state.data[i].id === action.id){
                    repeatedFlag = true;
                    break; // Once it finds the repeated value, then stop for looping
                }
                else{
                    repeatedFlag = false;
                }
            }

            if(repeatedFlag === true){
                return { ...state };
            }
            
            else{
                return { ...state, data : state.data.concat({
                    url : action.url,
                    id : action.id
                }) };
            }

        default:
            return state;
    }
}

```
---

#### containers/MainContainer.js
```js
import React, { Component } from 'react';
import './MainContainer.scss';

export default class MainContainer extends Component{
    render(){

        const { title } = this.props;
        // title came from PixabayContainer Component

        return(
            <div className="maincontainer">
                <p className="title">{title}</p>
                {this.props.children}
            </div>
        )
    }
}
```
---

#### containers/PixabayContainer.js.js
```js
import React, { Component } from 'react';
import { connect } from 'react-redux';
import MainContainer from './MainContainer';
import Pixabay from '../component/Pixabay';
import MyCollection from '../component/MyCollection';
import { getPictures } from '../store/modules/pixabay';
import { selectedPictures } from '../store/modules/mycollection';
import { Container, Row, Col, Button } from 'react-bootstrap';
import './PixabayContainer.scss';

class PixabayContainer extends Component{

    constructor(props){
        super(props);

        this.state = {
            search : '',

            toggle : false,

            toggleContext : 'My Collection'
        };

        this.onSelectedSearch = this.onSelectedSearch.bind(this);
        this.setSearch = this.setSearch.bind(this);
        this.keyPress = this.keyPress.bind(this);
        this.onSelectedPicture = this.onSelectedPicture.bind(this);
        this.toggleChange = this.toggleChange.bind(this);
    };

    // componentDidMount = () => {
    //     const { getAllPictures } = this.props;
    //     getAllPictures();
    // };

    /* Receive pictures by search content */
    onSelectedSearch = (content) => {
        const { getPictures } = this.props;
        
        /* ################# Insert your Pixabay API Key in second parameter ################# */
        getPictures(content, "");
    };

    /* Receive the search content from input tag */
    setSearch = (e) => {
        this.setState({
            search : e.target.value
        })
    };

    /* Enable to use Enter key */
    keyPress = (e) =>{
        if(e.key === 'Enter'){
            this.onSelectedSearch(this.state.search);
        }
    };

    /* Once the user clicks the pic to save, the function will be called to save pic into my collection with url & id */
    onSelectedPicture = (selectedPic, selectedId) => {
        const { selectedPictures } = this.props;
        selectedPictures(selectedPic, selectedId);
    };

    /* To convert page (The page will be changed as the value of this.state.toggle */
    toggleChange = () => {
        if(this.state.toggleContext === "My Collection"){
            this.setState({
                toggle : !this.state.toggle,
                toggleContext : "Go To Search"
            })
        }
        else if(this.state.toggleContext === "Go To Search"){
            this.setState({
                toggle : !this.state.toggle,
                toggleContext : "My Collection"
            })
        }
    };

    render(){

        const { pixabayList, myCollection } = this.props;
        // pixabayList came from pixabayReducer
        // myCollection came from pixabaymyCollectionReducer

        /* Since myCollection array has initial value (empty), use filter function to abandon first initial value */
        let newMyCollection = myCollection.filter((el) => {
            return el.url !== "";
        });

        return(
            <MainContainer title="Pixabay API">
                <div className="pixabayContainer">
                    <Container>
                        <Row>
                            <Col sm={6} xs={6}>
                                {/* The reason why I use visibility is because it takes up the space although it is invisible */}
                                <div style={{visibility: this.state.toggle ? 'hidden' : 'visible'}}>
                                    <div className="form">
                                        <input className="search-box" name="search" type="text" onChange={this.setSearch} onKeyPress={this.keyPress} autoComplete="off" required />
                                        <label htmlFor="search" className="label-search">
                                            <span className="content-search">Search</span>
                                        </label>
                                        <Button className="search-btn" variant="outline-dark" onClick={() => this.onSelectedSearch(this.state.search)}><i className="fa fa-search" aria-hidden="true"></i></Button>
                                    </div>
                                </div>
                            </Col>
                            <Col sm={6} xs={6}>
                                <div className="btn-section">
                                    <Button className="toggle-btn" variant="outline-dark" onClick={this.toggleChange}>{this.state.toggleContext}</Button>
                                </div>
                            </Col>
                        </Row>
                    </Container>
                    <div className="result-section">
                        {pixabayList.length !== 0 && this.state.toggle !== true
                        ?
                        <div>
                            <Pixabay pixabayList={pixabayList} onSelectedPicture={this.onSelectedPicture} />
                        </div>
                        : null}

                        {this.state.toggle !== false
                        ? <MyCollection myCollection={newMyCollection} />
                        : null}
                    </div>
                </div>
            </MainContainer>
        )
    }
};

/* pixabayList : Storing pictures once the user search (via Pixabay API) */
/* myCollection : Storing pictures that the user clicks save button */
const mapStateToProps = (state) => {
    return {
        pixabayList : state.pixabayReducer,
        myCollection : state.pixabayMyCollectionReducer.data
    };
};

/* getPictures function came from pixabay.js */
/* selectedPictures function came from mycollection.js */
const mapDispatchToProps = (dispatch) => {
    return {
        getPictures : (content, APIKey) => { dispatch(getPictures(content, APIKey)) },
        selectedPictures : (selectedPic, selectedId) => { dispatch(selectedPictures(selectedPic, selectedId)) }
    };
};

/* In order to use mapState / mapDispatch within PixabayContainer */
export default connect(
    mapStateToProps,
    mapDispatchToProps
)(PixabayContainer);
```
---

#### components/Pixabay.js
```js
import React, { Component } from 'react';
import { Container, Row, Col, Button } from 'react-bootstrap';
import './style.scss';

export default class Pixabay extends Component{

    render(){

        const { pixabayList, onSelectedPicture } = this.props;
        // pixabayList came from pixabayReducer
        // onSelectedPicture is associated with myCollectionReducer

        return(
            <div>
                <Container>
                    <Row>
                        {pixabayList.pixabayList.hits.map((el, index) => {
                            return(
                                <Col sm={4} key={index}>
                                    <div className="pixabayImg-container">
                                        <img src={el.largeImageURL} className="pixabayImg" alt={index} />
                                        <div className="middleImg">
                                            <Button className="pixabayBtn" variant="dark" onClick={() => onSelectedPicture(el.largeImageURL, el.id)}><i className="fa fa-floppy-o" aria-hidden="true"></i></Button>
                                        </div>
                                    </div>
                                </Col>
                            )
                        })}
                    </Row>
                </Container>
            </div>
        )
    }
}
```
---

#### components/MyCollection.js
```js
import React from 'react';
import { Container, Row, Col } from 'react-bootstrap';
import './style.scss';

const MyCollection = ({ myCollection }) => {
    return(
        <div>
            <Container>
                <Row>
                    {myCollection.map((el, index) => {
                        return(
                            <Col sm={4} key={index}>
                                <div className="mycollection-container">
                                    <img src={el.url} className="pixabayImg" alt={index} />
                                </div>
                            </Col>
                        )
                    })}
                </Row>
            </Container>
        </div>
    )
}

export default MyCollection;

```
---

#### container/PixabayContainer.scss
```scss
@mixin crossbrowserTransformX($value){
    transform: translateX($value);
    -webkit-transform: translateX($value);
    -moz-transform: translateX($value);
    -ms-transform: translateX($value);
}

@mixin crossbrowserTransformY($value){
    transform: translateY($value);
    -webkit-transform: translateY($value);
    -moz-transform: translateY($value);
    -ms-transform: translateY($value);
}

@mixin crossbrowserTransition($target, $time, $effect){
    transition: $target $time $effect;
    -webkit-transition: $target $time $effect;
    -moz-transition: $target $time $effect;
    -ms-transition: $target $time $effect;
}

.pixabayContainer{
    width: 100%;
    margin: 70px 0 0 0;
    
    .form{
        width: 100%;
        position: relative;
        height: 100px;
        overflow: hidden;
        text-align: left;
    }

    .form input{
        width: 50%;
        height: 100%;
        color: #595f6e;
        padding-top: 40px;
        border: none;
        outline: none;
    }

    .form label{
        position: absolute;
        bottom: 0px;
        left: 0%;
        width: 50%;
        height: 100%;
        pointer-events: none;
        border-bottom: 1px solid black;
    }

    .form label::after{
        content: "";
        position: absolute;
        height: 100%;
        left: 0px;
        bottom: -1px;
        width: 100%;
        border-bottom: 3px solid #5fa8d3;
        @include crossbrowserTransformX(-100%);
        @include crossbrowserTransition(transform, .1s, ease);
    }

    .content-search{
        position: absolute;
        bottom: 5px;
        left: 0px;
        @include crossbrowserTransition(all, .1s, ease);
    }

    .form input:focus + .label-search .content-search,
    .form input:valid + .label-search .content-search{
        @include crossbrowserTransformY(-140%);
        font-size: 14px;
        color: #5fa8d3;
    }
    
    .form input:focus .label-search::after,
    .form input:valid + .label-search::after{
        @include crossbrowserTransformX(0%);
    }

    .search-btn{
        margin-left: 20px;
    }

    .btn-section{
        height: 100px;
        padding-top: 50px;
        text-align: right;

        .toggle-btn{
            margin-left: 30px;
        }
    }

    .result-section{
        margin-top: 70px;
    }

}

@media screen and (max-width: 425px){
    .form input{
        width: 100%;
    }

    .form label{
        width: 100%;
    }

    .search-btn{
        margin-left: 0px;
    }
}

```
---