---
title: "SCSS"
date: "2019-08-14"
draft: false
path: "/blog/scss"
---

### SCSS
> - Sass Cascading Style Sheet.
> - In order to use it, need to configure webpack and babel.
> - However, to use it easily, require to install node-sass module.
---
### Syntax and Function

```scss

// example - variable
$main-fonts: Arial, sans-serif;
$headings-color: green;

//To use variables:
h1 {
  font-family: $main-fonts;
  color: $headings-color;
}



// example - nesting
nav {
  background-color: red;

  ul {
    list-style: none;

    li {
      display: inline-block;
    }
  }
}



// general CSS rule
div {
  -webkit-box-shadow: 0px 0px 4px #fff;
  -moz-box-shadow: 0px 0px 4px #fff;
  -ms-box-shadow: 0px 0px 4px #fff;
  box-shadow: 0px 0px 4px #fff;
}
// mixin - like functions for CSS 
@mixin box-shadow($x, $y, $blur, $c){ 
  -webkit-box-shadow: $x, $y, $blur, $c;
  -moz-box-shadow: $x, $y, $blur, $c;
  -ms-box-shadow: $x, $y, $blur, $c;
  box-shadow: $x, $y, $blur, $c;
}

@mixin customName ($param1, $param2, …) { }



<style type='text/sass'>
  @mixin border-radius($radius){
  -webkit-border-radius: $radius;
  -moz-border-radius: $radius;
  -ms-border-radius: $radius;
  border-radius: $radius;
  }
 
  #awesome {
    width: 150px;
    height: 150px;
    background-color: green;
    @include border-radius(15px);
  }
</style>



<style type='text/sass'>
  @mixin border-stroke($val){
    @if $val == light {
      border: 1px solid black;
    }
    @else if $val == medium{
      border: 3px solid black;
    }
    @else if $val == heavy{
      border: 6px solid black;
    }
    @else {
      border: none;
    }
  }
</style>



// 'through' end example
@for $i from 1 through 12 {
  .col-#{$i} { width: 100%/12 * $i; }
}

.col-1 {
  width: 8.33333%;
}

.col-2 {
  width: 16.66667%;
}

…

.col-12 {
  width: 100%;
}
```