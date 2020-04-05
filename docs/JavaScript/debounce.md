# js 函数防抖和函数节流
## 概念
函数防抖(debounce)

当函数动作过n毫秒后才会执行该动作，若在这n毫秒内又调用此动作则将重新计算执行时间。即频繁触发的情况下，只有足够的空闲时间，才执行代码一次。

函数节流（throttle）

一定时间内js方法指执行一次。

共同点： 函数节流（throttle）与 函数防抖（debounce）都是为了限制函数的执行频次，以优化函数触发频率过高导致的响应速度跟不上触发频率，出现延迟，假死或卡顿的现象
## 应用场景
函数防抖   
* window变化触发resize事件
* 表单的输入验证只在停止输入后进行一次  

函数节流  
* 触发鼠标移动事件：mousemove
* 触发keyup事件
* 触发scroll事件

## 代码实现

函数防抖   
``` 
// 函数防抖
var timer = false;
document.getElementById("debounce").onscroll = function(){
  clearTimeout(timer);
  timer = setTimeout(function(){
    console.log("函数防抖");
  }, 300);
}
```
防抖的要点：setTimeout，延迟执行要跑的代码。 

函数节流  
```
// 函数节流  
var exeSign = true;
document.getElementById('throttle').onscroll = function(){
  if(!exeSign){
    return;
  }
  exeSign = false;
  setTimeout(function(){
    console.log('函数节流');
    exeSign = false;
  }, 300);
}
```
函数节流的要点是，声明一个变量当标志位，记录当前代码是否在执行。如果空闲，则可以正常触发方法执行。如果上一次代码正在执行，直接retrun，取消当前方法执行。


  
