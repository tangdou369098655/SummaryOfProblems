@[前端防錯以及好用小tips指南總結](https://12312312313.md)

  简体中文 | [English](https://github.com/tangdou369098655/FrontEndDeployment/blob/master/nginx_eng.md)

  
## 内容

    * 1.一般情況下我們接收到的都是對象格式，某些情況下，需要接到後端傳過來的奇怪的字符串格式的JSON,需要解析成對象，但是有時候他們傳過來的格式有問題，會報錯
    * 解決方案：可以将方法放在try{JSON.parse(...)}catch(e){}代码块中。
    * 好用的JSON.stringify 方法
    * 1.1接受一个数组参数，指定需要转成字符串的属性，第二个参数指定，只转a属性。
```bash
    JSON.stringify({ a: 1, b: 2 }, ['a'])
    * '{"a":1}'
    * 1.2接受函数参数，转化对象的键和值
    function f12(key, value) {
      if (typeof value === "number") {
        value = 2 * value;
      }
      return value;
    }

    JSON.stringify({ a: 1, b: 2 }, f12)
    // '{"a":2,"b":4}'
    // 1.3只拿自己想要的值，如果处理函数返回undefined或没有返回值，這個属性就沒了啊哈哈。
    function f13(key, value) {
      if (typeof (value) == "string") {
        return undefined;
      }
      return value;
    }

    JSON.stringify({ a: "abc", b: 123 }, f13)
    // '{"b":123}'
    // 1.4給數據添加可讀性
    var d = { a: 1, b: 2 }
    JSON.stringify(d, null, 2);
    // "{
    //   "a": 1,
    //   "b": 2
    // }"
    // 1.5只输出你你想要的keys，加強版
    var e = { a: 1, b: 2, c: 3, d: 4 }
    JSON.stringify(e, ["a", "d"], 4);
    // "{
    //     "a": 1,
    //     "d": 4
    // }"
    // 1.5還可以這樣玩一下
    JSON.stringify({ a: 1, b: 2, c: 3 }, null, '|----');
    // "{
    // |----"a": 1,
    // |----"b": 2,
    // |----"c": 3
    // }"
    // 1.6關於JSON.parse你也可以試試看喲
    // 2.switch case 一定要記得寫break
    // 3.对象最後一個屬性结尾多逗号兼容IE會出問題，記得格式化一下代碼
    // 4.輸入框防呆要注意:如果是字符串類型就用myInput && myInput.trim()
    const myInput = '   '
    if (myInput && myInput.trim()) { console.log('這個輸入框不為空') }
    // 5.禁止給未声明的变量赋值,否則會搞出来很多全局变量
    // 6.多JS文件打包,如果你不喜歡寫分號，有時候會出問題，可以在文件開頭加個分號
    // 7.如果你不喜歡寫分號，有時候會引起函数返回值不正确或者是代碼執行有問題
    // 舉例說明
    function e() {
      return
      {
        a: "e"
      }
    }
    console.log(e()) // undefined
    let z = 'z'
    let m = 'm'
    [z, m] = [m, z]
    console.log(z, m) // Uncaught ReferenceError: m is not defined

    let z1 = 'z';
    let m1 = 'm';
    [z1, m1] = [m1, z1];
    console.log(z1, m1); // m z
    // 8.Array forEach()中无法return和break，代替方法有some()与every()
    var arr = [1, 2, 3, 4, 5];
    var num = 3;
    arr.forEach(function (v) {
      if (v == num) {
        break;
      }
      console.log(v);
    });
    // VM59:5 Uncaught SyntaxError: Illegal break statement
    var arr = [1, 2, 3, 4, 5];
    var num = 3;
    arr.forEach(function (v) {
      if (v == num) {
        return;
      }
      console.log(v);
    });
    // VM62:7 1
    // VM62:7 2
    // VM62:7 4
    // VM62:7 5
    // 解決如下,使用数组的另外两个方法some()与every()：
    var arr = [1, 2, 3, 4, 5];
    var num = 3;
    arr.some(function (v) {
      if (v == num) {
        return true;
      }
      console.log(v);
    });
    // VM65:7 1
    // VM65:7 2
    // true 跳出循環啦~~~~
    var arr = [1, 2, 3, 4, 5];
    var num = 3;
    arr.every(function (v) {
      if (v == num) {
        return false;
      } else {
        console.log(v);
        return true;
      }
    });
    // 1
    // 2
    // false 跳出循環啦~~~~
    // 小總結
    // 8.1 forEach没有返回值，只针对每个元素调用函數.
    // 8.2 some 当内部return true时跳出整个循环.
    // 8.3 every 当内部return false时跳出整个循环.
    // 8.4 map 有返回值，返回一个新的数组，每个元素为调用函數的结果
    // 9.需要使用到 eval()的时候，使用 Function()来搞一下

    // 10.箭头函数不仅没有this，常用的arguments也没有。如果你能获取到arguments，那它一定是来自父作用域的。
    // 11.防止後端瞎搞，我常用的寫法
    var ccc = { a: { t: 123 } }
    function setC() { ccc['b'] = 111 }
    ccc && ccc.a && ccc.a.t && setC()
    // 12.關於arguments,你要注意一下，想要把它當成數組用的話，需要轉化一下哦~
    function setArg() {
      console.log(arguments) // Arguments(4)
      console.log([].slice.call(arguments)) // Array(4) ----[1,2,3,'4']
    }
    setArg(1, 2, 3, '4')
// 13.判断数组用這個：Array.isArray，別用其他的~
// 14.使用Object.prototype 和 for in 搭配使用可能引發的坑，要注意下下~~
// 14.1如果你非要用，那就記得搭配上Object.hasOwnProperty(key)
var q = { a: 1, s: 2, d: 3 };

    Object.prototype.w = function () { };
    for (var key in q) {
      if (q.hasOwnProperty(key)) {
        console.log('for in搭配hasOwnProperty方法', '對象的key：', key, '對象的value:', q[key]);
    }
      console.log('for in方法', '對象的key：', key, '對象的value:', q[key]);
    }
    Object.keys(q).forEach(key => {
      console.log('forEach方法', '對象的key：', key, '對象的value:', q[key]);
    })
    Object.keys(q).some(key => {
      if (key === 'e') {
        return true; // some  true时跳出 循环.
      }
      console.log('some方法', '對象的key：', key, '對象的value:', q[key]);
    })
    Object.keys(q).every(key => {
      if (key === 'e') {
        return false; // every  false 跳出 循环
      } else {
        console.log('every方法', '對象的key：', key, '對象的value:', q[key]);
        return true;

      }
    })
    // for in搭配hasOwnProperty方法 對象的key： a 對象的value: 1
    // for in方法 對象的key： a 對象的value: 1
    // for in搭配hasOwnProperty方法 對象的key： s 對象的value: 2
    // for in方法 對象的key： s 對象的value: 2
    // for in搭配hasOwnProperty方法 對象的key： d 對象的value: 3
    // for in方法 對象的key： d 對象的value: 3
    // for in方法 對象的key： w 對象的value: ƒ () { }
    // forEach方法 對象的key： a 對象的value: 1
    // forEach方法 對象的key： s 對象的value: 2
    // forEach方法 對象的key： d 對象的value: 3
    // some方法 對象的key： a 對象的value: 1
    // some方法 對象的key： s 對象的value: 2
    // some方法 對象的key： d 對象的value: 3
    // every方法 對象的key： a 對象的value: 1
    // every方法 對象的key： s 對象的value: 2
    // every方法 對象的key： d 對象的value: 3
    
// 16.關於按给定的参数创建一日期对象以及獲取瀏覽器版本
  const data1 = new Date("2019-01-01")
  // Tue Jan 01 2019 08:00:00 GMT+0800 (台北标准时间)
  // ie某些版本會告訴你語法錯誤
  const data2 = new Date(2019,0,1)
  // Tue Jan 01 2019 00:00:00 GMT+0800 (台北标准时间), IE在用版本還OK
  const data3 = new Date("2019/01/01")
  // Tue Jan 01 2019 00:00:00 GMT+0800 (台北标准时间), IE在用版本還OK
  const userAgent1 = navigator.userAgent.toLowerCase()
  // "mozilla/5.0 (windows nt 10.0; win64; x64) applewebkit/537.36 (khtml, like gecko) chrome/71.0.3578.98 safari/537.36"
  function getBroswer(){
      var Sys = {};
      var ua = navigator.userAgent.toLowerCase();
      var s;
      (s = ua.match(/edge\/([\d.]+)/)) ? Sys.edge = s[1] :
      (s = ua.match(/rv:([\d.]+)\) like gecko/)) ? Sys.ie = s[1] :
      (s = ua.match(/msie ([\d.]+)/)) ? Sys.ie = s[1] :
      (s = ua.match(/firefox\/([\d.]+)/)) ? Sys.firefox = s[1] :
      (s = ua.match(/chrome\/([\d.]+)/)) ? Sys.chrome = s[1] :
      (s = ua.match(/opera.([\d.]+)/)) ? Sys.opera = s[1] :
      (s = ua.match(/version\/([\d.]+).*safari/)) ? Sys.safari = s[1] : 0;
  
      if (Sys.edge) return { broswer : "Edge", version : Sys.edge };
      if (Sys.ie) return { broswer : "IE", version : Sys.ie };
      if (Sys.firefox) return { broswer : "Firefox", version : Sys.firefox };
      if (Sys.chrome) return { broswer : "Chrome", version : Sys.chrome };
      if (Sys.opera) return { broswer : "Opera", version : Sys.opera };
      if (Sys.safari) return { broswer : "Safari", version : Sys.safari };
      
      return { broswer : "", version : "0" };
  }
  var abc = getBroswer();
  console.log("broswer:"+abc.broswer+" version:"+abc.version);
  // broswer:Chrome version:71.0.3578.98
  // 17. js裡常用的replace方法--string/正则我們容易忽略或者忘記的地方
  
  </script>
<div id="myDiv">15.带有ID的DOM树元素會成为全局变量，SO:尽量避免使用全局变量</div>
<script>
if (typeof myDiv === 'undefined') {
  myDiv = '測試一下我是不是字符串' // 其实这里不会执行到的
}
console.log(myDiv) // <div id="myDiv"></div>--输出DOM对象
myDiv = '改一改變量感受一下'
console.log(myDiv) // 改一改變量感受一下 
</script>
