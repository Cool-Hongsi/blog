---
title: "Jest & Enzyme"
date: "2019-09-08"
draft: false
path: "/blog/jest-enzyme"
---

### Jest is a javascript testing framework, created by developers who created react. Jest is not limited to react framework, it is general purpose javascript testing framework. But as it is from react developers more inclination is there. Enzyme is another framework which is specifically designed to test react components.

---

[Jest Testing Application Github](https://github.com/Cool-Hongsi/testing)

---

#### Dependencies
```js
  "dependencies": {
    "axios": "^0.19.0",
    "check-prop-types": "^1.1.2",
    "enzyme": "^3.10.0",
    "enzyme-adapter-react-16": "^1.14.0",
    "jest": "^24.8.0",
    "jest-enzyme": "^7.0.2",
    "moxios": "^0.4.0",
    "node-sass": "^4.12.0",
    "prop-types": "^15.7.2",
    "react": "^16.8.6",
    "react-dom": "^16.8.6",
    "react-redux": "^7.0.0-beta.0",
    "react-scripts": "3.0.1",
    "react-test-renderer": "^16.9.0",
    "redux": "^4.0.4",
    "redux-thunk": "^2.3.0"
  },
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
import rootReducer from './reducers/index';
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
import React from 'react';
import './app.scss';

import Header from './component/header/index';
import Headline from './component/headline/index';
import SharedButton from './component/button';
import ListItem from './component/listItem';
import { connect } from 'react-redux';
import { fetchPosts } from './actions';

const tempArr = [
  {
      fName: 'Joe',
      lName: 'Bloggs',
      email: 'joebloggs@gmail.com',
      age: 24,
      onlineStatus: true
  }
];

class App extends React.Component{

  constructor(props){
    super(props);

    this.fetch = this.fetch.bind(this);
  }

  fetch(){
    this.props.fetchPosts();
  }

  render(){

    const { posts } = this.props;

    const configButton = {
      buttonText : 'Get Posts',
      emitEvent : this.fetch
    }

    return(
      <div data-test="appComponent">
        <Header />
        <section className="main">
          <Headline header="Posts" desc="Click the button to render posts" tempArr={tempArr} />
          <SharedButton {...configButton} />
          {posts.length > 0 &&
            <div>
              {posts.map((post, index) => {
                const { title, body } = post;
                const configListItem = {
                  title,
                  desc : body
                };
                return(
                  <ListItem key={index} {...configListItem} />
                )
              })}
            </div>
          }
        </section>
      </div>
    )
  }
}

const mapStateToProps = (state) => {
  return {
    posts : state.posts
  }
}

export default connect(
  mapStateToProps,
  {fetchPosts}
)(App);
```
---

#### App.test.js
```js
import App from './App';
import { shallow } from 'enzyme';
import { findByTestAttr, testStore } from './../Utils/index';
import React from 'react';

const setUp = (initialState={}) => {
    const store = testStore(initialState);
    const wrapper = shallow(<App store={store} />).childAt(0).dive();
    return wrapper;
};

describe('App Component', () => {

    let wrapper;
    beforeEach(() => {
        const initialState = {
            posts : [
                {
                    title : 'Example Title 1',
                    body : 'Some Text'
                },
                {
                    title : 'Example Title 2',
                    body : 'Some Text'
                },
                {
                    title : 'Example Title 3',
                    body : 'Some Text'
                }
            ]
        }

        wrapper = setUp(initialState);
    });

    it('Should render without errors', () => {
        const component = findByTestAttr(wrapper, "appComponent");
        expect(component.length).toBe(1);
    });

});
```
---

#### Header.js
```js
import React from 'react';
import './Header.scss';

import PropTypes from 'prop-types';

export default class Header extends React.Component{
    render(){

        const { header, desc } = this.props;

        if(!header){
            return null;
        }

        return(
            <div data-test="HeaderComponent">
                <h1 data-test="header">
                    {header}
                </h1>

                <p data-test="desc">
                    {desc}
                </p>
            </div>
        )
    }
}

// Validate props
Header.propTypes = {
    header : PropTypes.string,
    desc : PropTypes.string,
    tempArr : PropTypes.arrayOf(PropTypes.shape({
        fName: PropTypes.string,
        lName: PropTypes.string,
        email: PropTypes.string,
        age: PropTypes.number,
        onlineStatus: PropTypes.bool
    }))
}
```
---

#### Header.test.js
```js
import React from 'react';
import { shallow } from 'enzyme';
import Header from './Header';

import { findByTestAttr, checkProps } from '../../Utils/index';

const setUp = (props={}) => {
    const component = shallow(<Header {...props} />);
    console.log(component.debug());
    return component;
};

describe('Header component', () => {

    describe('Checking PropTypes', () => {
        it('Should not throw an error or warning', () => {
            const expectedProps = {
                header: 'Test Header',
                desc: 'Test Desc',
                tempArr: [
                    {
                        fName: 'Test fName',
                        lName: 'Test lName',
                        email: 'test@gmail.com',
                        age: 23,
                        onlineStatus: false
                    }
                ]
            };

            const propsErr = checkProps(Header, expectedProps);
            expect(propsErr).toBeUndefined();
        })
    });

    describe('Have props', () => {
        let wrapper;

        // beforeEach function should be called before implementing it function
        beforeEach(() => {
            const props = {
                header : 'Test Header',
                desc : 'Test Desc'
            };

            wrapper = setUp(props);
        });

        it('Should render without errors', () => {
            const component = findByTestAttr(wrapper, 'HeaderComponent');
            expect(component.length).toBe(1);
        });

        it('Should render a H1', () => {
            const h1 = findByTestAttr(wrapper, 'header');
            expect(h1.length).toBe(1);
        });

        it('Should render a desc', () => {
            const desc = findByTestAttr(wrapper, 'desc');
            expect(desc.length).toBe(1);
        });
    });

    describe('Have no Props', () => {
        let wrapper;
        beforeEach(() => {
            wrapper = setUp();
        });

        it('Should not render', () => {
            const component = findByTestAttr(wrapper, 'HeaderComponent');
            expect(component.length).toBe(0);
        });
    });

});
```
---

#### index.js
```js
import checkPropTypes from 'check-prop-types';

export const checkProps = (component, expectedProps) => {
    const propsErr = checkPropTypes(component.propTypes, expectedProps, 'props', component.name);
    return propsErr;
};

export const findByTestAttr = (component, attr) => {
    const wrapper = component.find(`[data-test='${attr}']`);
    return wrapper;
};
```

---
#### integration.test.js
```js
import moxios from 'moxios';
import { testStore } from '../../Utils/index';
import { fetchPosts } from '../actions';

describe('fetchPosts action', () => {

    beforeEach(() => {
        moxios.install();
    });

    afterEach(() => {
        moxios.uninstall();
    });

    test('Store is updated correctly', () => {
        const expectedState = [
            {
                title : 'Example title 1',
                body : 'Some text'
            },
            {
                title : 'Example title 2',
                body : 'Some text'
            },
            {
                title : 'Example title 3',
                body : 'Some text'
            }
        ];

        const store = testStore();
        moxios.wait(() => {
            const request = moxios.requests.mostRecent();
            request.respondWith({
                status : 200,
                response : expectedState
            })
        });

        return store.dispatch(fetchPosts()).then(() => {
            const newState = store.getState();
            expect(newState.posts).toBe(expectedState);
        });
        
    });

});
```
---