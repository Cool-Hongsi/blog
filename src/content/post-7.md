---
title: "Fetch & Axios (ReactJS and NodeJS)"
date: "2019-08-20"
draft: false
path: "/blog/fetch-axios"
---

### fetch
> Make it possible to have access to API composed by JSON format
---

```js
import React from 'react';

import MainContainer from './MainContainer';
import Github from './Github';

export default class Main extends React.Component{

    constructor(props){
        super(props);

        this.state = {
            // Github
            githubId : '',
            githubArray : [
                {
                    login : '',
                    id : '',
                    avatar_url : ''
                }
            ],
        }

        this.githubPutId = this.githubPutId.bind(this);
        this.githubFetch = this.githubFetch.bind(this);
    }

    githubPutId = (e) => {
        this.setState({
            githubId : e.target.value
        })
    }

    githubFetch = () => {
        fetch("https://api.github.com/users/" + this.state.githubId).then((data) => {
            data.json().then((realData) => {
                this.setState({
                    githubArray : [...this.state.githubArray, {
                        login : realData.login,
                        id : realData.id,
                        avatar_url : realData.avatar_url
                    }]
                })
            }).catch((err) => {
                console.log(err);
            })
        }).catch((err) => {
            console.log(err);
        })
    }

    render(){
        return(
            <MainContainer>

                <h4>Github</h4>
                <input type="text" placeholder="Github ID" onChange={this.githubPutId} /><br/>
                <button onClick={this.githubFetch}>Click</button><br/>
                {this.state.githubArray.map((el, index) => {
                    return(
                        <div key={index}>
                            <Github login={el.login} id={el.id} avatar_url={el.avatar_url} />
                        </div>
                    )
                })}
                
            </MainContainer>
        )
    }
}

import React from 'react';

export default class Github extends React.Component{
    render(){
        return(
            <div>
                {this.props.login !== "" ?
                    <div>
                        {this.props.login} / {this.props.id} / <img src={this.props.avatar_url} alt="" style={{width: "25px", height: "25px"}} />
                    </div>
                : null}
            </div>
        )
    }
}
```

---

### axios
> transfer data between Frontend & Backend with the standard of REST API

---

```js
import React from 'react';
import axios from 'axios';

import MainContainer from './MainContainer';
import Item from './Item';

export default class Main extends React.Component{

    constructor(props){
        super(props);

        this.state = {
            // Item
            item : {
                id : '',
                name : '',
                price : ''
            },
            itemArray : [
                {
                    id : '',
                    name : '',
                    price : ''
                }
            ],

            // Sort
            sortData : ''
        }

        this.itemPutData = this.itemPutData.bind(this);
        this.itemAxios = this.itemAxios.bind(this);

        this.sortFunc = this.sortFunc.bind(this);
    }

    itemPutData = (e) => {
        var name = e.target.name;
        var value = e.target.value;

        this.setState({
            item : {
                ...this.state.item,
                [name] : value
            }
        })
    }

    itemAxios = () => {
        axios.put(`/api/${this.state.item.id}`, {name: this.state.item.name, price: this.state.item.price}).then((data) => {
            this.setState({
                itemArray : [...this.state.itemArray, {
                    id : data.data.id,
                    name : data.data.name,
                    price : data.data.price
                }]
            })
        }).catch((err) => {
            console.log(err);
        })
    }

    sortFunc = (e) => {
        var sortedData = this.state.itemArray;

        if(e.target.value === "low-price"){
            sortedData = sortedData.sort((a, b) => {
                return a.price - b.price;
            })
        }
        else if(e.target.value === "high-price"){
            sortedData = sortedData.sort((a, b) => {
                return b.price - a.price;
            })
        }

        this.setState({
            itemArray : sortedData,
            sortData : e.target.value
        })
    }

    render(){
        return(
            <MainContainer>

                <h4>Item</h4>
                <select value={this.state.sortData} onChange={this.sortFunc}>
                    <option value="select">Select</option>
                    <option value="low-price">Low Price</option>
                    <option value="high-price">High Price</option>
                </select><br/><br/>
                
                <input type="text" name="id" placeholder="ID" onChange={this.itemPutData} /><br/>
                <input type="text" name="name" placeholder="NAME" onChange={this.itemPutData} /><br/>
                <input type="text" name="price" placeholder="PRICE" onChange={this.itemPutData} /><br/>
                <button onClick={this.itemAxios}>Send</button><br/>
                {this.state.itemArray.map((el, index) => {
                    return(
                        <div key={index}>
                            <Item id={el.id} name={el.name} price={el.price} />
                        </div>
                    )
                })}

            </MainContainer>
        )
    }
}

import React from 'react';

export default class Github extends React.Component{
    render(){
        return(
            <div>
                {this.props.id !== "" ?
                    <div>
                        {this.props.id} / {this.props.name} / {this.props.price}
                    </div>
                : null}
            </div>
        )
    }
}

/* NodeJS */
const express = require('express');
const app = express();
const port = process.env.PORT || 8081;
const bodyParser = require('body-parser');

app.use(bodyParser.urlencoded({extended: false}));
app.use(bodyParser.json());

app.put('/api/:id', (req, res) => {
    // database.updateData(parseInt(req.params.id), req.body.name, parseInt(req.body.price)).then((data) => {})
    // module.exports.updateData = (id, name, price) => {
    //     return new Promise((resolve, reject) => {
    //         resolve(aaa) -> should go to then((data)) 
    //     })
    // }

    console.log("This is Backend");
    let data = {
        id : parseInt(req.params.id),
        name : req.body.name,
        price : parseInt(req.body.price)
    };
    console.log(data);

    res.json(data);
});

app.listen(port, () => {
    console.log(`Connected ${port}`);
});
```