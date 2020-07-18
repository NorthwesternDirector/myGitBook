

### Date 对象

> Date 对象用于处理日期和时间，使用：`var myDate=new Date()` 创建Date对象。可给Date传入 ***string类型*** ：'Fri Oct 11 2019 18:30:30' (具体值) 或 ***number类型*** ：8640000 (距离1970-1-1的毫秒数) 的参数

```js
var myDate=new Date() //创建Date对象
myDate.getMilliseconds() // 以 (0 ~ 999)返回毫秒数
myDate.getSeconds()// 以 (0 ~ 59)返回秒数
myDate.getMinutes() // 以(0 ~ 59)返回分钟
myDate.getHours()// 以(0 ~ 23)返回小时
myDate.getDate()// 以(1 ~ 31)返回一个月中的某一天
myDate.getDay()// 以(0 ~ 6)返回星期
myDate.getMonth()// 以(0 ~ 11)返回月份
myDate.getFullYear() // 以四位数字返回年份
myDate.toString() // "Mon Nov 18 2019 10:17:16 GMT+0800 (中国标准时间)"
myDate.toDateString() // "Mon Nov 18 2019"
myDate.toTimeString() // "10:17:16 GMT+0800 (中国标准时间)"
```

##### 应用1 差值

```javascript
var date1 = new Date('2019-11-18')
var date2 = new Date('2019-11-17') 
date1-dat2 //86400000 差值结果为毫秒数，86400000毫秒 /1000=86400秒 /60=1440分钟 /60=24小时 /24=1天
```

##### 应用2 增减

```js
var myDate = new Date('2019-11-18')
var newDate=  new Date( myDate.setDate(myDate.getDate()+1) ) // myDate的后一天
//注意：myDate.setDate(myDate.getDate()+1)不加new Date的包裹时，它的值为一个毫秒数（相对于1970-1-1）
```

##### 应用3 标准格式转换

```javascript
function dateFormat(dateObj){
    var myDate=dateObj
    function addZero(v){ //月份、日期个位数补零
                if(v<10)return `0${v}`
        return v.toString()
    }
    var format = `${myDate.getFullYear().toString()}-${addZero(myDate.getMonth()+1)}-${addZero(myDate.getDate())}`
    return  format // 2019-11-18
}
```

##### 应用4 消息时间显示

```javascript
function dateFormat(date){
  var hDate=new Date(date)
  var nowDate=new Date()
  if(nowDate.getDate()>handleDate.getDate()){
    return `${hDate.getMonth()+1} ${hDate.getDate()}${hDate.gethours()} :${hDate.getMinutes()}`
  }else if(nowDate.getHours()-hDate.getHours()<2){
    var dvalue=Math.abs(nowDate.getMinutes()-hDate.getMinutes()
    if(dvalue<1){
      return '刚刚'  
    }else if(dvalue<5){
      return `${dvalue}分钟前`
    }else if(dvalue>30){
      return '半小时前'
    }
  }
}
```