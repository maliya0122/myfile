## css动画

### 1.animation关键帧的使用

开始到结束这个阶段的动画，开始是白色，结束也是白色，中间是自己设置的动画；

```js
main{
    width: 100px;
    height: 100px;
    background-color: #fff;
    /* 设置动画的相关属性 */
    animation-name: hd;
    animation-duration: 2s;
}

//设置动画：移动方块
@keyframes hd{
    25%{
        transform: translateX(300px);
    }
    50%{
        transform: translate(300px,300px);
    }
    75%{
        transform: translatey(300px);
    }
}

//组合关键帧，就是css样式的书写
```

### 2.多个动画使用与时间配置

animation-name: hd,red,blue;
animation-duration: 2s,4s,6s;

备注：

属性重叠的时候，出现在后面的优先级更高；

### 3.哪些属性可以使用动画

属性有中间值的，可以慢慢演化过去的；冰化成水；

比如width，height，transform等；

### 4.动画常用的属性值

| 属性                      | 取值                                          | 用途                                    |
| ------------------------- | --------------------------------------------- | --------------------------------------- |
| animation-name            | hd                                            | 设置动画的名称                          |
| animation-duration        | 2s                                            | 动画持续的时间                          |
| animation-iteration-count | 1,2，infinite                                 | 动画循环的次数                          |
| animation-direction       | normal，reverse，alternate，alternate-reverse | 控制动画的方向：0-100瞬间回去、慢慢回去 |
| animation-delay           | 2s                                            | 动画等待执行的时间                      |
| animation-fill-mode       | forwards，backwards                           | 动画填充模式                            |
| animation-timing-function |                                               | 动画运行时是否匀速/变速                 |

```js
//自定义动画属性
animation:hd(动画名称) 2s(动画时间) 2s(延迟时间) backwards
```

**其中 transform 描述了元素静态样式。而transition 和 animation 却都能实现动画效果。**

1.如果要灵活定制多个帧以及循环，用animation.

2.如果要简单的from to 效果，用 transition.

3.如果要使用js灵活设定动画属性，用transition.

```js
img{
    height：15px;
    width: 15px;
    transition-property: height;
    transition-duration: 1s;
    transition-delay: 1s;
    transition-timing-function: ease;/*属性分开写*/
}
img:hover{
    height: 450px;
    width: 450px;
}
```

transition相当于是需要事件触发与js结合使用，过渡所需要的时间、延迟，只有from，to；

animation是动画，不需要js事件触发；可以设置多个动画帧；