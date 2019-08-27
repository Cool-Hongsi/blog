---
title: "Algorithm Practice"
date: "2019-08-26"
draft: false
path: "/blog/algorithm-practice"
---

#### Reverse Sentence
```js
reverseSentence = (str) => {
// return str.split('').reverse().join('');
  
   let array = [];
  
   for(let i=str.length-1; i>=0; i--){
     array.push(str[i]);
   };
  
  return array.join('');
}

console.log(reverseSentence("Hello, World ABCD"));
```
---

#### Check the number of words
```js
findWord = (word, str) => {
  let array = str.split(' ');
  let count = 0;
  
  array.map((el) => {
     if(el.includes(word)){
       count++;
     } 
  });
  
  return count;
}

console.log(findWord('w', "Hello, This is awesome world"));
```

---

#### Change second character as UpperCase
```js
changeSecondWord = (str) => {
  let array = str.split(' ');
  
  let newArray = array.map((el) => {
     return el.replace(el.charAt(1), el.charAt(1).toUpperCase());
  });
  
  return newArray.join(' ');
}

console.log(changeSecondWord("Hello, This is awesome world"));
```

---

#### Change the property of object
```js
var obj = [
  {
    name : "abc",
    age : 29
  },
  {
    name : "def",
    age : 30
  }
];

var newobj = obj.map((el) => {
  let newProperty = {};
  
  newProperty['NickName'] = el.name;
  newProperty['Number'] = el.age;
  
  return newProperty;
});

console.log(newobj);
```

---

#### Multiply 2 in array (value should be larger than 0)
```js
let array = [7, 2, -5, -1, 0, 9];

console.log(array.filter((el) => {
  return el > 0;
}).map((el) => {
  return el * 2;
}));
```

---

#### Factorial
```js
let sum = 1;

for(let i=5; i>0; i--){
  sum *= i;
};

console.log(sum);
```

---

#### Find the longest word in sentence
```js
longestWord = (str) => {
  let array = str.split(' ');
  let result = {
    word : '',
    length : 0
  };
  
  array.map((el) => {
    if(el.length > result.length){
      result.word = el;
      result.length = el.length;
    }
  });
  
  return result;
}

console.log(longestWord("Hello, This is awesome world"));
```

---

#### Maximum and Minimum Value
```js
var array = [7, 10, -5, 2, 9, -1, 0, 8];
var maxValue = 0;

array.map((el) => {
  if(el > maxValue){
    maxValue = el;
  }
});

console.log("Max Value is " + maxValue);

var minValue = maxValue;

array.map((el) => {
  if(el < minValue){
    minValue = el;
  }
});

console.log("Min Value is " + minValue);
```

---

#### Repeated Value
```js
var array = [7, 10, -5, 2, 10, 9, -1, 0, 8];

for(let i=0; i<array.length; i++){
  for(let j=i+1; j<array.length; j++){
    if(array[i] === array[j]){
      console.log(array[i]);
    }
  }
}
```

---

#### String Rotation
```js
stringRotation = (str1, str2) => {
  let newstr = str1 + str1;
  
  if(newstr.toUpperCase().indexOf(str2.toUpperCase()) !== -1){
    return true;
  }
  else{
    return false;
  }
}

console.log(stringRotation("aBcD", "CdA"));
```

---

#### String Left Rotation
```js
stringLeftRotation = (value) => {
  let deletedArray = array.splice(0, value);
  
  return array.concat(deletedArray);
}

var array = [1, 2, 3, 4, 5];

var value = prompt("Number");

console.log(stringLeftRotation(value));
```

---

#### Put new value into odd index
```js
var array = [1, 2, 3, 4, 5];

for(let i=0; i<array.length; i++){
  if(i % 2 === 1){
    array.splice(i, 1, 100);
  }
}

console.log(array);
```

---
