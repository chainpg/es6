# ECMAScript 6 
ECMAScript 5 （簡稱 ES5）寫過大量前端應用程式在沒有良好的規範往往會造成效能及程式碼難以維護的狀況，為解決這些問題並讓程式更簡潔也出現了 CoffeeScript 來撰寫 Javascript，但這些問題在 ECMAScript 6（簡稱 ES6）的釋出及 Babel （編譯 ES6 的工具）的發展下，ES6被社群關注度也越來越高，希望把學習的過程記錄並分享。


- [Template Literals](#template-literals)
- [Constants](#constants)
- [Scope](#scope)
- [Arrow Functions](#arrow-functions)
- [Enhanced Object Properties](#enhanced-object-properties)
- [Extended Parameter Handling](#extended-parameter-handling)
- [Destructuring Assignment](#destructuring-assignment)
- [Modules](#modules)
- [Classes](#classes)
- [Symbol Type](#symbol-type)
- [Iterators](#iterators)
- [Generators](#generators)
- [Map Set](#map-set)
- [Promises](#promises)


# Template Literals
ES 5 本身對字串本身沒有內建 Template ，所以要達到這功能往往要透過第三方元件，或自己刻一個，相信網路上有一堆範例，某種程度也會造成程式碼不易維護的狀況，ES 6 的出現解決了這問題，來看看下列範例。
```
var player = { name : 'Bill'};
var message = `hello ${player.name}`;
console.log(message);
```
字串前後採用 `（鍵盤數字1的左邊），再將需要的變數放入 ${} 內，除了顯示之外還可以在${} 內進行運算式的操作如下

```
var a = 1
var b = 2
var message = `a + b = ${ a + b}`
console.log(message);
```
[(回頁首)](#ecmascript-6)

# Constants
程式開發往往需要定義一些常數且任何情況下都不被改變，在 ES5 能達到這目地但需要寫上一段 Object.defineProperty 的語法宣告。
```
var myConst = {};
Object.defineProperty(myConst, "PI", {
    value:        3.141593,
    enumerable:   true,
    writable:     false,
    configurable: false
});
console.log(myConst.PI);
myConst.PI = 99;
console.log(myConst.PI);
```
我們會看到 `3.141593` 印出兩次的結果，而且這樣的程式在 ES5 裡面合法，也就是不會有任合錯誤訊息發生。


看看 ES6 的做法
```
const PI = 3.141593
console.log(PI)
PI = 99
console.log(PI)
```
這邊宣告了一個PI的常數，動作是列印修改常數在列印，由下列結果發現在 `PI=99`的定義會發生錯誤，且錯誤訊息非常直白告訴我們不能賦予 constant 值並中斷程式碼，這告訴我們做錯事情因該要被處罰。
```
3.141593
VM231:3 Uncaught TypeError: Assignment to constant variable
```
[(回頁首)](#ecmascript-6)

# Scope
看一下現實程式碼裡可能發生的悲劇。
```
// A工程師
var i= 1;
// ... 經過了幾百行程式碼後
// B工程師
function go(){
  for(var i=0 ; i < 3 ; i++){
  }
}
console.log('i',i);
go();
console.log('i',i);
```
B工程師將 i 重新定義成`3`了，而且也不會被提示意思就是B工程師根本不知道自己造就了這場悲劇，而要在 ES 5 避免這場悲劇也可以這麼寫。
```
var i=1;
(function(){
  for(var i=0 ; i < 3 ; i++){}
  console.log('i',i);
})();
console.log('i',i);
```
ES 6 的 let 宣告讓變數僅在該區塊內存活
```
let i =1
{
for(let i=0 ; i< 3 ; i++){}
}
console.log(i);
```
這確保了變數再定義後在該範圍內的唯一性，試著在同範圍定義重複定義如 `let i=0 ; let i=0;` 會直接產生語法錯誤，讓變數定義更加嚴謹。
[(回頁首)](#ecmascript-6)

# Arrow Functions 
```
function showMembers(){
  var self = this;
  this.preWorld = 'Hello ';
  ['Bill' , 'Kris' , 'Tommy'].forEach(function(v){
    console.log(self.preWorld + v);
   });
}
showMembers();
```
看到 var self = this 因該滿熟悉的，這是決匿名方法為了取得外層區塊 this 的做法，原本定義方法語法 [function] [argraments]，可以想像把他們顛倒過來在吧[function]改成 => 變成 [argraments] [=>]如下，而且匿名方法還可以直接透過 this 取得外層區塊的 this，ES 6 的這改變讓程式碼更簡潔。
```
var showMembers = () => {
  this.preWorld = 'Hello ';
  ['Bill' , 'Kris' , 'Tommy'].forEach((v) => {
    console.log(this.preWorld + v);
   });
}
showMembers();
```
[(回頁首)](#ecmascript-6)

# Enhanced Object Properties
物件宣告更簡潔，讓屬性更強大
```
var firstName = 'Jeremy'
var lastName = 'Lin'
var customer = { firstName , lastName , [firstName+' number'] : 7 };
console.log(customer);
```
得到結果`Object {firstName: "Jeremy", lastName: "Lin", Jeremy number: 7}`
[(回頁首)](#ecmascript-6)

# Extended Parameter Handling
Javascript 參數在方法定義三個如 a , b , c ，在呼叫時可以只帶入一個，執行時不會出錯不像 JAVA，但沒帶入的參數我們會希望給予預設值，ES5 的寫法
```
function fun(a , b , c){
  if( b === undefined ){
    b = 0;
  }
  if( c === undefined ){
  c = 0;
  }
  return a + b + c;
}
fun( 1 );
```
ES 6 可在參數定義初始值
```
function fun(a , b = 0 , c = 0){
  return a + b + c;
}
fun( 1 );
```
也可以吧多個參數合併在一個變數中，如下範例將會吧 b1 , b2 , b3 封裝為一個陣列
```
function fun(a , ...b){
 console.log(b);
}
fun(1 , 'b1' , 'b2' , 'b3');
```
`[1 , 2 ].concat([3 ,4])`這段程式是將陣列合併，ES6 有更簡潔的宣告方式
```
var a = [3 , 4 ];
var t = [1 , 2 , ...a];
console.log(t);
```
[(回頁首)](#ecmascript-6)

# Destructuring Assignment
陣列跟物件都可以透過新語言的特性將程式再宣告，而在宣告的方式更為精簡且明確，下列是陣列的範例 
```
function fun(){
  return [1 ,2 ,3]
}
var {a , b , c} = fun();
```
下列範例物件的宣告可以針對需要的屬性宣告，沒有定義的屬性不會進入
```
function fun(){
  return { a:1 , b:2 , c:3 , d:4}
}
var {a , c} = fun();
```
以上都是取值後重新定義，也可在參數找指定需要的屬性
```
function fun({ a , b}){
  console.log(`a > ${a} , b > ${b}`);
}
fun({ a: 1 , b : 2});
```
[(回頁首)](#ecmascript-6)

# Modules
模組具有獨立及不相互污染變數跟單一檔案的特性，模組可相互匯入以及匯出，這樣的表現讓模組在程式區塊獨立，讓每支程式內聚力更強
```
//  lib/common.js
var name = 'Bill';
export function whoAreYou() { return name }
export name


//  app.js
import * as com from "lib/common"
console.log(com.whoAreYou());
console.log(com.name);
```
可以看到將 `common.js` 模組中匯出的值都定義在`com`的別名的變數中，也可依據需要提取。
```
// or app.js
import { whoAreYou , name} from "lib/common"
console.log(whoAreYou());
console.log(name);
```
[(回頁首)](#ecmascript-6)

# Classes
ES 6 讓開發人員透過新語言特性來更明確的實作物件導向程式（OOP），新語言也可將 ES 5 多種定義類型的方式做統一。
```
class Player{
  constructor(name){
    this.name = name
  }
  run () {
    console.log('run')
  }
}
class Basketball extends Player{
  constructor(name , pts , reb ,ast){
    super(name)
    this.pts = pts
    this.reb = reb
    this.ast = ast
  }
  shoot (){
    console.log(`${this.name} shoot`);
  }
}
var jeremy = new Basketball('Jeremy Lin' , 20 , 6 , 10);
jeremy.shoot();
```
get & set 宣告方式
```
class Player{
  constructor(name){
    this.name = name
  }
  get name (){ return this.name }
  set name(name){ this.name = name }
}
```
還有 static 的方法
```
class Player{
  constructor(name){
    this.name = name
  }
  static who(){ console.log('is player') }
}
Player.who()
```
[(回頁首)](#ecmascript-6)

# Symbol Type
這是繼 Javascript 添加了第7個資料型態，型態有：Undefined、Boolean、NULL、String、Number、Object、Symbol，主要是解決數值再判斷是造成的衝突，如下
```
var type1 = "type";
var type2 = "type";


var input = 'type';


switch(input){
  case type1 :
  console.log('type1');
  break;
  case type2 :
    console.log('type2');
  break;  
}
```
Symbol 能確保每個變數所乘載的值是獨一無二的，如下
```
var type1 = Symbol();
var type2 = Symbol();


var input = type2;


switch(input){
  case type1 :
  console.log('type1');
  break;
  case type2 :
    console.log('type2');
  break;  
}
```
[(回頁首)](#ecmascript-6)

# Iterators
有時候數據結構非現有可以走訪`for(let ? of ?)`的類型，在 ES6 中可以具備走訪的勢必實作Symbol.iterator 的屬性，讓我們將資料實現 Symbol.iterator 的屬性使其可走訪，添加 Symbol.iterator 也必須在其實做 next 的方法，done 屬性為是否繼續走訪而 value 則是當下索引的資料
```
class MyTeam{
  constructor(members) {
    this.members = members;
    this.index = 0;
  }


  [Symbol.iterator]() { return this; }


  next() {
    if (this.index < this.members.length) {
      return {
        value: this.members[this.index++],
        done: false
      };
    } else {
      return { value: undefined, done: true };
    }
  }
}


var brooklynTeam = new MyTeam(
  [{name : "Jeremy"},{name : "Lopez"}]
);


for(let member of brooklynTeam){
  console.log(member);
}


```
[(回頁首)](#ecmascript-6)

# Generators
若程式碼區塊中可依據不同狀態或步驟進行切割，並需要引用時可以依據邏輯進行走訪區塊，參考如下
```
const setup1 = Symbol('setup1');
const setup2 = Symbol('setup2');
const setup3 = Symbol('setup3');


function* Work() {
  console.log('do setup1');
  yield setup1;
  console.log('do setup2');
  yield setup2;
  console.log('do setup3');
  yield setup3;
  return;
}


var work = Work();


work.next(); // {value: Symbol(setup1), done: false}
work.next(); // {value: Symbol(setup2), done: false}
work.next(); // {value: Symbol(setup3), done: false}
work.next(); // {value: undefined, done: true}


// 或這樣走訪 
for(let setup of work) {
  console.log( setup );
}
```
[(回頁首)](#ecmascript-6)

# Map Set
## Set 
提供了新的資料結構，當中成員資料不重複。
```
let teamSet = new Set();
teamSet.add('Jeremy');
teamSet.add('Jeremy');
teamSet.add('Lopez');
// 或直接宣告時以參數初始 let teamSet = new Set(['Jeremy','Jeremy','Lopez']);


console.log(teamSet);
// {"Jeremy", "Lopez"}
```
可以`keys()`進行走訪
```
let teamSet = new Set(['Jeremy','Jeremy','Lopez']);
for(let member of teamSet.keys()) {
  console.log(member);
}
// Jeremy
// Lopez
```


## WeakSet
WeakSet 僅允許傳入物件，不允許傳入值，複雜的應用中容易不小心造成記憶體洩漏，如下
```
function member( ) {
  name = 'Jeremy Lin';
} 
```
這等同於 `window.name =  'Jeremy Lin';` 而更嚴重的是 setInterval 內有沒有停止的情況下持續定義無法回收的變數，而 WeakSet 可解決並避免這個狀況。
```
var names = new WeakSet();
function member() {
  names.add(['Jeremy Lin']);
}
member();
```


雖然為全局的 names 變數，但宣告為 WeakSet 可確保進行回收


## Map
資料集(Hash)的 keys 僅能接受字串，而 Map keys 可以接受任何型態，
```
let member = { id:1,name:'Jeremy'};
let team = new Map();
team.set(member , 'brooklyn');


team.get(member); // brooklyn
team.has(member); // true
```
[(回頁首)](#ecmascript-6)

# Promises
Promises 更精準的定義了該宣告的程式是處理非同步的處理，這裡採用實務的圖片驗證當作範例，當`<file/>`發生異動表示檔案獲取後，透過兩個非同步的方法來取得對應的參數，`FileReader.onload`取得檔案大小，`Image.onload`取得檔案的寬及高，透過取得寬高各位也可進一步驗證圖片橫版或直版甚至比例。


html
```
<input type="file" id="file-upload" multiple="">
```


script
```
const ERROR_TYPE_NOT_MATCH = 'ERROR_TYPE_NOT_MATCH';
const ERROR_OUT_SIZE = 'ERROR_OUT_SIZE';


function fileCheck(file , limitType , fileSizeKb){
	var promise = new Promise((resolve, reject) => {


		// 驗證檔案類型
		if(limitType && limitType.indexOf(file.type) == -1){
			reject(ERROR_TYPE_NOT_MATCH);
		}
		function format_float(num, pos){
			let size = Math.pow(10, pos);
			return Math.round(num * size) / size;
		}
		let reader = new FileReader();
	    reader.onload = function (e) {
	    	console.log(e.total);	
	    	let KB = format_float(e.total / 1024, 2);
	    	// 驗證檔案大小
	    	if(fileSizeKb && KB > fileSizeKb){
	    		reject(ERROR_OUT_SIZE);
	    	}


	    	let img = new Image;
	    	img.onload = function(e){
	    		resolve({name : file.name , size : KB , width : img.width , height : img.height});
	    	}
	    	img.src = e.target.result;
	    	
	    }    
	    reader.readAsDataURL(file);


	});
	return promise;
}


$('#file-upload').change(function(){
	fileCheck(this.files[0] , ['image/png','image/jpg','image/jpeg'] , 1024).then(function(result){
		console.log(result);
	},function(type){
		alert(type);
	});
});


```
[(回頁首)](#ecmascript-6)
