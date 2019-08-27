---
title: "Create Modern NavBar"
date: "2019-08-17"
draft: false
path: "/blog/create-modern-navbar"
---

### This navbar provides some featues which are cover whole page and responsive design.

---

[ReactJS](reactjs)

```html
<div className="container">
    <nav>
        <input type="checkbox" id="nav" className="hidden" />
        <label for="nav" className="nav-btn">
            <i></i>
            <i></i>
            <i></i>
        </label>

        <div className="logo">
            <a href="/">BRAND</a>
        </div>

        <div className="nav-wrapper">
            <ul>
            <li><a href="/">Home</a></li>
            <li><a href="/">About</a></li>
            <li><a href="/">Project</a></li>
            <li><a href="/">Contact</a></li>
            </ul>
        </div>
    </nav>
</div>
```

---

[SCSS](scss)

```scss
.logo{
    float: left;
    padding: 8px;
    margin: 8px 0px 0px 16px;

    a{
        color: #000;
        font-size: 18px;
        font-weight: 700;
        letter-spacing: 0px;
        text-transform: uppercase;
        text-decoration: none;
    }
}

nav{
    padding: 8px;

    ul{
        float: right;

        li{
            display: inline-block;

            a{
                display: inline-block;
                outline: none;
                color: #000;
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

@media screen and (max-width: 864px){
    .logo{
        padding: 0px;
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
                text-align: right;
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

            li:nth-child(1){
                a{
                    transition-delay: .2s;
                    -webkit-transition-delay: .2s;
                    -moz-transition-delay: .2s;
                    -ms-transition-dely: .2s;
                }
            }

            li:nth-child(2){
                a{
                    transition-delay: .3s;
                    -webkit-transition-delay: .3s;
                    -moz-transition-delay: .3s;
                    -ms-transition-dely: .3s;
                }
            }

            li:nth-child(3){
                a{
                    transition-delay: .4s;
                    -webkit-transition-delay: .4s;
                    -moz-transition-delay: .4s;
                    -ms-transition-dely: .4s;
                }
            }

            li:nth-child(4){
                a{
                    transition-delay: .5s;
                    -webkit-transition-delay: .5s;
                    -moz-transition-delay: .5s;
                    -ms-transition-dely: .5s;
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
        cursor: pointer;
        border-radius: 50%;

        i{
            display: block;
            background: #000;
            width: 20px;
            height: 2px;
            border-radius: 2px;
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
    .nav-btn{
        transform: rotate(45deg);
        -webkit-transform: rotate(45deg);
        -moz-transform: rotate(45deg);
        -ms-transform: rotate(45deg);

        i{
            background: #000;
            transition: transform .2s ease;
        }

        i:nth-child(1){
            transform: translateY(6px) rotate(180deg);
            -webkit-transform: translateY(6px) rotate(180deg);
            -moz-transform: translateY(6px) rotate(180deg);
            -ms-transform: translateY(6px) rotate(180deg);
        }

        i:nth-child(2){
            opacity: 0;
        }

        i:nth-child(3){
            transform: translateY(-6px) rotate(90deg);
            -webkit-transform: translateY(-6px) rotate(90deg);
            -moz-transform: translateY(-6px) rotate(90deg);
            -ms-transform: translateY(-6px) rotate(90deg);
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
