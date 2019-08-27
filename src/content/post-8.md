---
title: "Jest & Enzyme"
date: "2019-08-27"
draft: false
path: "/blog/jest-enzyme"
---

### Jest is a javascript testing framework, created by developers who created react. Jest is not limited to react framework, it is general purpose javascript testing framework. But as it is from react developers more inclination is there. Enzyme is another framework which is specifically designed to test react components.

---

#### Dependencies
```js
  "dependencies": {
    "check-prop-types": "^1.1.2",
    "enzyme": "^3.10.0",
    "enzyme-adapter-react-16": "^1.14.0",
    "node-sass": "^4.12.0",
    "prop-types": "^15.7.2",
    "react": "^16.9.0",
    "react-dom": "^16.9.0",
    "react-scripts": "3.1.1"
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

#### App.js
```js
import React from 'react';
import './App.scss';

import Header from './component/Header';

const tempArr = [
  {
      fName: 'Joe',
      lName: 'Bloggs',
      email: 'joebloggs@gmail.com',
      age: 24,
      onlineStatus: true
  }
];

export default class App extends React.Component{
  render(){
    return(
      <div>
        <Header header="This is Header" desc="Hello" tempArr={tempArr} />
      </div>
    )
  }
}
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