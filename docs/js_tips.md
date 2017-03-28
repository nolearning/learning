1. Get Right Type
  
  typeof operator
  * typeof undefined === "undefined"
  * typeof void(0) === "undefined"
  * typeof Boolean === "function"
  * typeof Boolean(false) === "boolean"
  * typeof Boolean(1) === "boolean"
  * typeof Number === "function"
  * typeof Number(1) === "number"
  * typeof String === "function"
  * typeof String(123) === "string"
  * typeof "abc" === "string"
  * typeof Object === "function"

  * typeof null === "object"
  * typeof undeclared_var === "undefined"
  * typeof NaN === "number"
  * typeof [1,2] === "object"
  * typeof new Date === "object"
  * typeof /a-z/ === "object"
  * (function () {console.log(typeof arguments)})();  //"object"

[[Class]] Accoding to ES5, [[Class]] is "a String value indicating a specification defined classification of objects".

Object.prototype.toString (Acccording to ES5)

    1. Let O be the result of calling ToObject passing the this value as the argument.
    2. Let class be the value of the [[Class]] internal property of O.
    3. Return the String value that is the result of concatenating the three Strings "[object ", class, and "]".

In short, someObject.toString() -> [object [[Class]]]

Unfortunately, the specialized built-in objects mostly override toString method.
```
[1,2,3].toString(); //"1,2,3"
(new Date).toString(); //"Tue Mar 28 2017 17:04:43 GMT+0800 (CST)"
/a-z/.toString(); //"/a-z"
```
...fortunately we can use the `call` or `apply` function, like:
```
Object.prototype.toString.call([1,2,3]); //"[object Array]"
Object.prototype.toString.call(new Date); //"[object Date]"
Object.prototype.toString.call(/a-z/); //"[object RegExp]"
```

then we can create a tiny functoin
let ofType = (obj) => {
  return Object.prototype.toString.call(obj)
    .match(/\s(\w+)/)[1].toLowerCase();
}

``````
function CustomType(){};

ofType({a:4}); // "object"
ofType([1,2,3]) //"array"
(function () {console.log(ofType(arguments))})();  //"arguents"
ofType(new ReferenceError); //"error"
ofType(new Date);   //"date"
ofType(/a-z/);  //"regexp"
ofType(Math);   //"math"
ofType(JSON);   //"json"
ofType(new Number(4)) //"number"
ofType(new String("abc")) //"string"
ofType(new Boolean(true)); //"boolean"
ofType(new CustomType);  //"object"

typeof {a:4}; // "object"
typeof [1,2,3]; //"object"
(function () {console.log(typeof arguments)})();  //"object"
typeof new ReferenceError; //"object"
typeof new Date;   //"object"
typeof /a-z/;  //"object"
typeof Math;   //"object"
typeof JSON;   //"object"
typeof new Number(4)) //"object"
typeof new String("abc")) //"object"
typeof new Boolean(true); //"object"
typeof new CusteomType;   //"object"

new Date instanceof Date;   //true
[1,2,3] instanceof Array;   //true
new CustomType instanceof CustomType; //true

Math instanceof Math //TypeError

For DOM Object (Chrome Safari FF IE9+??)
ofType(window); //"window"
ofType(document); //"htmldocuemnt"
ofType(document.createElement('a')); //"htmlanchorelement"
ofType(alert);   //"function"

typeof window; //"object"
typeof document; //"object"
typeof document.createElement('a'); //"object"
typeof alert);   //"function"
``````

comparing to typeof, ofType can check more correctly, eg.
``````
function jsonParseIt(jsonTest) {
  if (jsonTest()) {
      return JSON.parse('{"a":2}');
    } else {
      console.log("non-compliant JSON object detected!");
    }
}
//then create a fake JSON
window.JSON = {
  parse: function() {
    console.log("It's just a mock.");
  }
}
//check with typeof and specific functoin, will call a mock
jsonParseIt(()=>{return JSON && (typeof JSON.parse === "function")})

//check with oftype, will output a messages in console.
jsonParseIt(()=>{return ofType(JSON) === "json"});
``````
More details see [Fixing the JavaScript typeof operator](https://javascriptweblog.wordpress.com/2011/08/08/fixing-the-javascript-typeof-operator/)
