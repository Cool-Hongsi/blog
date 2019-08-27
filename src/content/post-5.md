---
title: "Big O"
date: "2019-07-16"
draft: false
path: "/blog/big-o"
---

### Big O
> - Describe the efficiency of algorithms.

#### 1. Time complexity (runtime)
#### 2. Space complexity (memory)

---

#### Types
##### 1 - Regardless of amount of data, the time dealing with data is consistent
---
##### N - The time dealing with data is propotional as much as amount of data (for loop)
```js
for(let i=0; i<data; i++){

}
```
---
##### N Square - The time dealing with data is more propotional as much as amount of data (repeated for loop)
```js
for(let i=0; i<data; i++){
    for(let i=0; i<data; i++){

    }
}
```
---
##### NM - The time dealing with data is more propotional as much as amount of data (two for loop)
```js
for(let i=0; i<data; i++){
    for(let j=0; j<datal j++){

    }
}
```
---
##### 2 N Square - fibonacci sequence
