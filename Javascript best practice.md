
### Use object method shorthand.

#### Bad

```js
const atom = {
  value: 1,

  addValue: function (value) {
    return atom.value + value;
  },
};
```

#### Good

```js
const atom = {
  value: 1,

  addValue(value) {
    return atom.value + value;
  },
};
```

-----

### Use property value shorthand.

#### Bad

```js
const obj = {
  lukeSkywalker: lukeSkywalker,
};
```

#### Good

```js
const obj = {
  lukeSkywalker,
};
```

-----

### Only quote properties that are invalid identifiers

#### Bad

```js
const bad = {
  'foo': 3,
  'bar': 4,
  'data-blah': 5,
};
```

#### Good

```js
const good = {
  foo: 3,
  bar: 4,
  'data-blah': 5,
};
```

------

### Do not call Object.prototype methods directly

Such as hasOwnProperty, propertyIsEnumerable, and isPrototypeOf. Why? These methods may be shadowed by properties on the object in question - consider { hasOwnProperty: false } - or, the object may be a null object (Object.create(null)).

#### Bad

```js
console.log(object.hasOwnProperty(key));
```

#### Good

```js
console.log(Object.prototype.hasOwnProperty.call(object, key));
```

#### Best

```js
const has = Object.prototype.hasOwnProperty; // cache the lookup once, in module scope.
/* or */
import has from 'has'; // https://www.npmjs.com/package/has
// ...
console.log(has.call(object, key));
```

------

### Use Array.from instead of spread ... for mapping over iterables, because it avoids creating an intermediate array.

#### Bad

```js
const baz = [...foo].map(bar);
```

#### Good

```js
const baz = Array.from(foo, bar);
```

-----------

### Use named function expressions instead of function declarations.

#### Bad

```js
const foo = function () { ... };
```

#### Good

```js
// lexical name distinguished from the variable-referenced invocation(s)

const short = function longUniqueMoreDescriptiveLexicalFoo() {};
```

----------------

### Always put default parameters last

#### Bad

```js
function handleThings(opts = {}, name) {}
```

#### Good

```js
function handleThings(name, opts = {}) {}
```

----------------

### Never reassign parameters.

#### Bad

```js
function f2(a) {
  if (!a) { 
	  a = 1; 
  }
}
```

#### Good

```js
function f3(a) {
  const b = a || 1;
}
```

---------------------------------

### Remove argumnets dublication

#### Bad

```js
const result = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
	.filter(int => isEven(int));
```

#### Good

```js
const result = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
	.filter(isEven);
```

------------

### Avoid globals

Global variables and function names are an incredibly bad idea. The reason is that every JavaScript file included in the page runs in the same scope. If you have global variables or functions in your code, scripts included after yours that contain the same variable and function names will overwrite your variables/functions.

You can protect those from being overwritten by using function:

```js
myNameSpace = function(){
  var current = null;
  function init(){...}
  function change(){...}
  function verify(){...}
}();
```

------------

### Use shortcut notation when it makes sense

Conditions can be shortened using “ternary notation”. For example, the following construct defines a variable as 1 or -1, depending on the value of another variable:

```js
var direction;
if(x > 100){
  direction = 1;
} else {
  direction = -1;
}
```

This can be shortened to a single line:

```js
var direction = (x > 100) ? 1 : -1;
```

Another common situation in JavaScript is providing a preset value for a variable if it is not defined, like so:

```js
if(v){
  var x = v;
} else {
  var x = 10;
}
```

The shortcut notation for this is the double pipe character:

```js
var x = v || 10;
```

-----------------

### Modularize — one function per task

This is a general programming best practice — making sure that you create functions that fulfill one job at a time makes it easy for other developers to debug and change your code without having to scan through all the code to work out what code block performs what function.

This also applies to creating helper functions for common tasks. If you find yourself doing the same thing in several different functions then it is a good idea to create a more generic helper function instead, and reuse that functionality where it is needed.

Also, one way in and one way out makes more sense than forking the code in the function itself. Say you wanted to write a helper function to create new links. You could do it like this:

```js
function addLink(text,url,parentElement){
  var newLink = document.createElement('a');
  newLink.setAttribute('href',url);
  newLink.appendChild(document.createTextNode(text));
  parentElement.appendChild(newLink);
}
```

This works ok, but you might find yourself having to add different attributes depending on which elements you apply the link to. For example:

```js
function addLink(text,url,parentElement){
  var newLink = document.createElement('a');
  newLink.setAttribute('href',url);
  newLink.appendChild(document.createTextNode(text));
  if(parentElement.id === 'menu'){
    newLink.className = 'menu-item';
  }
  if(url.indexOf('mailto:')!==-1){
    newLink.className = 'mail';
  }
  parentElement.appendChild(newLink);
}
```

This makes the function more specific and harder to apply to different situations. A cleaner way is to return the link and cover the extra cases in the main functions that need them. This turns  `addLink()`  into the more generic  `createLink()`:

```js
function createLink(text,url){
  var newLink = document.createElement('a');
  newLink.setAttribute('href',url);
  newLink.appendChild(document.createTextNode(text));
  return newLink;
}

function createMenu(){
  var menu = document.getElementById('menu');
  var items = [
    {t:'Home',u:'index.html'},
    {t:'Sales',u:'sales.html'},
    {t:'Contact',u:'contact.html'}
  ];
  for(var i=0;i<items.length;i++){
    var item = createLink(items.t,items.u);
    item.className = 'menu-item';
    menu.appendChild(item);
  }
}
```

--------------------------

### Avoid heavy nesting

Nesting code explains its logic and makes it much easier to read, however nesting it too far can also make it hard to follow what you are trying to do. Readers of your code shouldn’t have to scroll horizontally, or suffer confusion when their code editors wrap long lines (this makes your indentation efforts moot anyway).

#### Bad

```js
function renderProfiles(o){
  var out = document.getElementById(‘profiles’);
  for(var i=0;i<o.members.length;i++){
    var ul = document.createElement(‘ul’);
    var li = document.createElement(‘li’);
    li.appendChild(document.createTextNode(o.members[i].name));
    var nestedul = document.createElement(‘ul’);
    for(var j=0;j<o.members[i].data.length;j++){
      var datali = document.createElement(‘li’);
      datali.appendChild(
        document.createTextNode(
          o.members[i].data[j].label + ‘ ‘ +
          o.members[i].data[j].value
        )
      );
      nestedul.appendChild(datali);
    }
    li.appendChild(nestedul);
  } 
  out.appendChild(ul);
}
```

#### Good

```js
function renderProfiles(o){
  var out = document.getElementById(‘profiles’);
  for(var i=0;i<o.members.length;i++){
    var ul = document.createElement(‘ul’);
    var li = document.createElement(‘li’);
    li.appendChild(document.createTextNode(data.members[i].name));
    li.appendChild(addMemberData(o.members[i]));
  } 
  out.appendChild(ul);
}
function addMemberData(member){
  var ul = document.createElement(‘ul’);
  for(var i=0;i<member.data.length;i++){
    var li = document.createElement(‘li’);
    li.appendChild(
      document.createTextNode(
        member.data[i].label + ‘ ‘ +
        member.data[i].value
      )
    );
  }
  ul.appendChild(li);
  return ul;
}
```

-----------------

### Use Decimals Cautiously

When is 0.1 + 0.2 not equal to 0.3? When you do the calculation in JavaScript. The actual value of 0.1 + 0.2 comes out to be something like 0.30000000000000004. The reason for this (nope, not a bug) is because JavaScript uses Binary Floating Point numbers. To get around this issue, you can multiply your numbers to remove the decimal portion:

#### Bad

```js
var hamburger = 8.20;
var fries = 2.10;
var total = hamburger + fries;
console.log(total); //Outputs 10.299999999999999
```

#### Good

```js
hamburger = hamburger * 100;
fries = fries * 100;
total = hamburger + fries;
total = total / 100;
console.log(total); //Outputs 10.3
```

------------

### Start Blocks on the Same Line

Most developers that write software in other C-family programming languages use the Allman style of formatting for block quotes. This places the opening curly brace on its own line. This pattern would look like this in JavaScript:

```js
if(myState === 'testing') 
{
   console.log('You are in testing');
} 
else 
{
   console.log('You are in production');
}
```

This will work most of the time. However, JavaScript is designed in such a way that following the K&R style of formatting for blocks is a better idea. This format starts the opening curly brace on the same line as the preceding line of code. It looks like this:

```js
if(myState === 'testing') {
   console.log('You are in testing');
} else {
   console.log('You are in production');
}
```

While this may only seem like a stylistic difference, there can be times when there is a impact on your code if you use the Allman style. Earlier we talked about the browser inserting semicolons where it felt they were needed. One fairly serious issue with this is on return statements. Let's look at an example:

```js
return
{
   age: 15,
   name: Jon
}
```

You would assume that the object would be returned but instead the return value will be  `undefined`. The reason for this is because the browser has inserted a semicolon after the word return, assuming that one is missing. While return is probably the most common place where you will experience this issue, it isn't the only place. Browsers will add semi-colons after a number of keywords, including var and throw.

It is because of this type of issue that is is considered best practice to always use the K&R style for blocks to ensure that your code always works as expected.

-----------------

### Declare All Variables First

Most languages that conform to the C-family style will not put an item into memory until the program execution hits the line where the item is initialized.

JavaScript is not like most other languages. It utilizes function-level scoping of variables and functions. When a variable is declared, the declaration statement gets hoisted to the top of the function. The same is true for functions:

```js
function simpleExample(){
   i = 7;
   console.log(i); // undefined
   var i;
}
```

-------------------

### Be Careful Using typeof

Again, the edge cases here will bite you if you aren't careful. Normally,  `typeof`  returns the string representation of the value type ('number', 'string', etc.) The problem comes in when evaluating NaN ('number'), null ('object'), and other odd cases:

```js
var i = 10 - 'taxi'; // NaN

if (typeof(i) === 'number') {
   console.log('i is a number'); // return this statement
} else {
   console.log('You subtracted a bad value from i');
}
```

----------------------

### Use `parseInt` with radix

```js
parseInt("12", 10); // 12
```

---------------------------

### Use spaces between operators.

#### Bad

```js
const squashed=Math.PI*r*r;
```

#### Good

```js
const roomy = Math.PI * r * r;
```

------------------------

### Do not add spaces inside parentheses.

#### Bad

```js
console.log( 'Hello world' );
```

#### Good

```js
console.log('Hello world');
```

--------------------

### Do not add spaces inside square brackets.

#### Bad

```js
[ 'Foo' ];
```

#### Good

```js
['Bar'];
```

-----------------------

### Add spaces inside braces and after colons.

#### Bad

```js
{foo:'bar'};
```

#### Good

```js
{ foo: 'bar' }
```

-----------------

### Add spaces after commas.

#### Bad

```js
fn([1,2,3],'a','b','c');
```

#### Good

```js
fn([1, 2, 3], 'a', 'b', 'c');
```

----------------

### Use spaces around keywords, argument lists and conditions.

#### Bad

```js
function squashed(x){
    if(x){
        return typeof(x);
    }
}
```

#### Good

```js
function roomy (x) {
    if (x) {
        return typeof x;
    }
}
```

-------------------

### Declare each variable separately and on their own lines. The exception is undefined variables, which may be declared in the same statement.

#### Bad

```js
var foo, bar = 123,
    baz = true, qux;
```    
    
#### Good

```js
var bar = 123;
var baz = true;
var foo, qux;
```

-------------------

### Avoid saving references to  `this`. These can almost always be replaced with an arrow function.

#### Bad

```js
function foo () {
    const self = this;
    return new Promise(function (resolve) {
        setTimeout(function () {
            resolve(self.foo);
        }, 1000);
    });
}
```

#### Good

```js
function bar () {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve(this.bar);
        }, 1000);
    });
}
```

--------------

### Assign the result of expensive functions to a variable to avoid unnecessary recalculation.

#### Bad

```js
if (expensiveOperation()) {
    console.log('Result: ' + expensiveOperation());
}
```

#### Good

```js
const result = expensiveOperation();
if (result) {
    console.log('Result: ' + result);
}
```

--------------

### Use PascalCase for constructors and class names.

#### Bad
```js
function controller () {}
class applicationView extends view {}
```

#### Good

```js
function Controller () {}
class ApplicationView extends View {}
```

-------------

### Use UPPER_CASE for global constants.

```js
const API_ORIGIN = 'http://example.com';

function fetchProfile (userID) {
    const endpoint = `${API_ORIGIN}/user/${userID}`;
    return $.ajax(endpoint);
}
```

---------------

### Use  `instanceof`  to check the type of objects.

```js
class Base {}
class Foo extends Base {}
class Bar extends Base {}
const foo = new Foo();
const bar = new Bar();
```

#### Bad

```js
foo.constructor === Foo; // => true
foo.constructor === Base; // => false
```

#### Good

```js
foo instanceof Foo; // => true
foo instanceof Base; // => true
```

-------------------

### Try and use template strings to concatenate values.

```js
const foo = 'foo';
const bar = 'bar';
let result;
```

#### Bad

```js
result = [foo, ' - ', bar].join('');
```

#### Good

```js
result = `${foo} - ${bar}`;
```

--------------------

### Use  `Number()`  to coerce values into numbers where possible. This pattern is preferred for numeric values generated by other systems (e.g. via APIs).

```js
const response = '123';
let result;
```

#### Bad

```js
result = +response;
```

#### Bad

```js
result = response >> 0;
```

#### Good

```js
result = parseInt(response);
```

#### Best

```js
result = Number(response);
```

----------------

### Use  `isNaN`  to compare with  `NaN`

#### Bad

```js
NaN === NaN; // => false
```

#### Good

```js
isNaN(NaN); // => true
```

--------------------------

### Split method chains with newlines.

#### Bad

```js
$('.foo').filter('ul').find('li').text((i) => `Item ${i + 1}`);
```

#### Good

```js
$('.foo')
    .filter('ul')
    .find('li')
    .text((i) => `Item ${i + 1}`);
```

---------------------------

### Use a leading dot when chaining. Indent chained methods.

#### Bad

```js
Promise.resolve().then(() => {
    const data = getUserInput();
    return asyncFunction1(data);
}).then(() => asyncFunction2())
.catch((err) => displayError(err));
```

#### Good

```js
Promise.resolve()
    .then(() => {
        const data = getUserInput();
        return asyncFunction1(data);
    })
    .then(() => asyncFunction2())
    .catch((err) => displayError(err));
```
    
-----------

### Limit chain length where possible.

#### Bad

```js
$('#items')
  .find('.selected')
  .highlight()
  .end()
  .find('.open')
  .updateCount();
```        

#### Good

```js
const $items = $('#items');
const $selected = $items.find('.selected');
const $open = $items.find('.open');

$selected.highlight();
$open.updateCount();
```

------------------

### Avoid using  `for..in`  loops.

#### Bad

```js
const obj = {
  foo: 'bar'
};
for (let key in obj) {
  if (Object.prototype.hasOwnProperty.call(obj, key)) {
    console.log(key, obj[key]);
  }
}
```

#### Good

```js
const obj = {
  foo: 'bar'
};
Object.keys(obj).forEach((key) => {
  console.log(key, obj[key]);
});
```

------------

### Always throw error objects, never strings or any other object type.

#### Bad

```js
if (!valid) {
  throw 'Result is not valid';
}
```

#### Good

```js
if (!valid) {
  throw new Error('Result is not valid');
}
```


--------------------

### Do not prepend  `Error:` to error messages, or any similar label.

#### Bad

```js
const err = new Error('ValidationError: Result is not valid');
console.error(err.toString());
```

#### Good

```js
const err = new Error('Result is not valid');
err.name = 'ValidationError';
console.error(err.toString());
```

--------------

### Avoid long argument list

Use a single object parameter and destructuring assignment instead. It also makes handling optional parameters much easier.

#### Bad

```js
function getRegisteredUsers(fields, include, fromDate, toDate) {}
getRegisteredUsers(['firstName', 'lastName', 'email'], ['invitedUsers'], '2016-09-26', '2016-12-13')
```

#### Good

```js
function getRegisteredUsers({
  fields,
  include,
  fromDate,
  toDate
});

getRegisteredUsers({
  fields: ['firstName', 'lastName', 'email'],
  include: ['invitedUsers'],
  fromDate: '2016-09-26',
  toDate: '2016-12-13'
});
```

----------------

### Use explanatory variables  
  
#### Bad  
  
```js  
const address = 'One Infinite Loop, Cupertino 95014';  
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;  
saveCityZipCode(address.match(cityZipCodeRegex)[1], address.match(cityZipCodeRegex)[2]);  
```
  
#### Good
  
```js  
const address = 'One Infinite Loop, Cupertino 95014';  
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;  
const [, city, zipCode] = address.match(cityZipCodeRegex) || [];  
saveCityZipCode(city, zipCode);  
```
  
---------
  
#### Array Alternative: Sets  
  
#### Bad

```js  
var allChords = [];

chords.forEach(chord => {
  if (!allChords.includes(chord)) {
    allChords.push(chord);
  }
}); 
```

#### Good

```js
var allChords = new Set();
 
chords.forEach(chord => allChords.add(chord));
```

-----------------

### Optimize loops. If you don't need all data looping use `break`. 

```js  
for (let i = 0; i <= 10; i++) {
  if (i === 5) {
    break;
  }
  console.log(i);
}

```

-----------------------

// 1. Don't over-optimize  
  
// Bad:  
  
// On old browsers, each iteration with uncached `list.length` would be costly  
// because of `list.length` recomputation. In modern browsers, this is optimized.  
for (let i = 0, len = list.length; i < len; i++) {  
  // ...  
}  
// Good:  
  
for (let i = 0; i < list.length; i++) {  
  // ...  
}

----------------

/*------------------- Variables ---------------------*/  
  
// 1. Use meaningful variable names  
  
const yyyymmdstr = moment.format('YYYY/MM/DD');  
  
// ->  
  
const yearsMonthDay = moment.format('YYYY/MM/DD');  
  
/*----------------------------------------------*/  
  
// 2. Remove obvious data  
  
getUserData();  
  
// ->  
  
getUser();  
  
/*----------------------------------------------*/

// 3. No magid numbers  
  
for (let i = 0; i < 525600; i += 1) {  
  runCron();  
}  
  
// ->  
  
const minutesInYear = 525600;  
for (let i = 0; i < minutesInYear; i += 1) {  
  runCron();  
}

// 5. Avoid Mental Mapping  
  
const locations = ['Austin', 'New York', 'San Francisco'];  
locations.forEach((l) => {  
  doStuff();  
  doSomeOtherStuff();  
  // ...  
 // ... // ... // Wait, what is `l` for again?  dispatch(l);  
});  
  
// ->  
  
const locations = ['Austin', 'New York', 'San Francisco'];  
locations.forEach((location) => {  
  doStuff();  
  doSomeOtherStuff();  
  // ...  
 // ... // ...  dispatch(location);  
});

  
// 6. Don't add unneeded context  
  
const Car = {  
  carMake: 'Honda',  
  carModel: 'Accord',  
  carColor: 'Blue'  
};  
  
function paintCar(car) {  
  car.carColor = 'Red';  
}  
  
// ->  
  
const Car = {  
  make: 'Honda',  
  model: 'Accord',  
  color: 'Blue'  
};  
  
function paintCar(car) {  
  car.color = 'Red';  
}

-----------------

// 7. Use default arguments instead of short circuiting or conditionals  
  
function createMicrobrewery(name) {  
  const breweryName = name || 'Hipster Brew Co.';  
  // ...  
}  
  
// ->  
  
function createMicrobrewery(name = 'Hipster Brew Co.') {  
  // ...  
}

------------

## Functions:

// 1. Note: ECMA-262 defines a block as a list of statements. A function declaration is not a statement.  
  
// bad  
if (currentUser) {  
  function test() {  
  console.log('Nope.');  
 }}  
  
// good  
let test;  
if (currentUser) {  
  test = () => {  
  console.log('Yup.');  
 };}  
  
/*----------------------------------------------*/  
  
// 2. Never name a parameter arguments. This will take precedence over the arguments object that is given to every function scope.  
  
// bad  
function foo(name, options, arguments) {  
  // ...  
}  
  
// good  
function foo(name, options, args) {  
  // ...  
}  
  
/*----------------------------------------------*/  
  
// 3. Never use arguments, opt to use rest syntax ... instead. eslint: prefer-rest-params  
  
// Why? ... is explicit about which arguments you want pulled. Plus, rest arguments are a real Array, and not merely Array-like like arguments.  
  
// bad  
function concatenateAll() {  
  const args = Array.prototype.slice.call(arguments);  
  return args.join('');  
}  
  
// good  
function concatenateAll(...args) {  
  return args.join('');  
}  
  
/*----------------------------------------------*/  
  
// 4. Use default parameter syntax rather than mutating function arguments.  
  
// really bad  
function handleThings(opts) {  
  // No! We shouldn’t mutate function arguments.  
 // Double bad: if opts is falsy it'll be set to an object which may // be what you want but it can introduce subtle bugs.  opts = opts || {};  
  // ...  
}  
  
// still bad  
function handleThings(opts) {  
  if (opts === void 0) {  
  opts = {};  
 }  // ...  
}  
  
// good  
function handleThings(opts = {}) {  
  // ...  
}  
  
/*----------------------------------------------*/  
  
// 5 Avoid confusing arrow function syntax (=>) with comparison operators (<=, >=). eslint: no-confusing-arrow  
  
// bad  
const itemHeight = item => item.height > 256 ? item.largeSize : item.smallSize;  
  
// good  
const itemHeight = item => (item.height > 256 ? item.largeSize : item.smallSize);  
  
/*----------------------------------------------*/  
  
// 6. Never reassign parameters. eslint: no-param-reassign  
  
// Why? Reassigning parameters can lead to unexpected behavior, especially when accessing the arguments object. It can also cause optimization issues, especially in V8.  
  
// bad  
function f1(a) {  
  a = 1;  
  // ...  
}  
  
function f2(a) {  
  if (!a) { a = 1; }  
  // ...  
}  
  
// good  
function f3(a) {  
  const b = a || 1;  
  // ...  
}  
  
function f4(a = 1) {  
  // ...  
}  
  
/*-------------------------------------------------------*/  
  
// 7. Never mutate parameters. eslint: no-param-reassign  
  
// Why? Manipulating objects passed in as parameters can cause unwanted variable side effects in the original caller.  
  
// bad  
function f1(obj) {  
  obj.key = 1;  
}  
  
// good  
function f2(obj) {  
  const key = Object.prototype.hasOwnProperty.call(obj, 'key') ? obj.key : 1;  
}  
  
/*----------------------------------------------*/  
  
// 8. Never use the Function constructor to create a new function. eslint: no-new-func  
  
// Why? Creating a function in this way evaluates a string similarly to eval(), which opens vulnerabilities.  
  
// bad  
var add = new Function('a', 'b', 'return a + b');  
  
// still bad  
var subtract = Function('a', 'b', 'return a - b');  
  
/*------------------- Functions ---------------------*/  
  
// 1. Function arguments (2 or fewer ideally)  
  
function createMenu(title, body, buttonText, cancellable) {  
  // ...  
}  
  
// ->  
  
function createMenu({ title, body, buttonText, cancellable }) {  
  // ...  
}  
  
/*----------------------------------------------*/  
  
// 2. Functions should do one thing  
  
function emailClients(clients) {  
  clients.forEach((client) => {  
  const clientRecord = database.lookup(client);  
  if (clientRecord.isActive()) {  
  email(client);  
 } });}  
  
// ->  
  
function emailActiveClients(clients) {  
  clients  
      .filter(isActiveClient)  
 .forEach(email);  
}  
  
function isActiveClient(client) {  
  const clientRecord = database.lookup(client);  
  return clientRecord.isActive();  
}  
  
/*----------------------------------------------*/  
  
// 3. Function names should say what they do  
  
function addToDate(date, month) {  
  // ...  
}  
  
const date = new Date();  
  
// It's hard to tell from the function name what is added  
addToDate(date, 1);  
  
// ->  
  
function addMonthToDate(month, date) {  
  // ...  
}  
  
const date = new Date();  
addMonthToDate(1, date);  
  
/*----------------------------------------------*/  
  
// 4. Set default objects with Object.assign  
  
const menuConfig = {  
  title: null,  
  body: 'Bar',  
  buttonText: null,  
  cancellable: true  
};  
  
function createMenu(config) {  
  config.title = config.title || 'Foo';  
  config.body = config.body || 'Bar';  
  config.buttonText = config.buttonText || 'Baz';  
  config.cancellable = config.cancellable !== undefined ? config.cancellable : true;  
}  
  
createMenu(menuConfig);  
  
// ->  
  
const menuConfig = {  
  title: 'Order',  
  // User did not include 'body' key  
  buttonText: 'Send',  
  cancellable: true  
};  
  
function createMenu(config) {  
  config = Object.assign({  
  title: 'Foo',  
  body: 'Bar',  
  buttonText: 'Baz',  
  cancellable: true  
  }, config);  
  
  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}  
 // ...}  
  
createMenu(menuConfig);  
  
/*----------------------------------------------*/  
  
// 5. Don't use flags as function parameters  
  
/*   
  Flags tell your user that this function does more than one thing.   
  Functions should do one thing. Split out your functions if they are following different code paths based on a boolean.  
*/  
  
function createFile(name, temp) {  
  if (temp) {  
  fs.create(`./temp/${name}`);  
 } else {  
  fs.create(name);  
 }}  
  
// ->  
  
function createFile(name) {  
  fs.create(name);  
}  
  
function createTempFile(name) {  
  createFile(`./temp/${name}`);  
}  
  
/*----------------------------------------------*/  
  
// 6. Avoid Side Effects  
  
// Global variable referenced by following function.  
// If we had another function that used this name, now it'd be an array and it could break it.  
let name = 'Ryan McDermott';  
  
function splitIntoFirstAndLastName() {  
  name = name.split(' ');  
}  
  
splitIntoFirstAndLastName();  
  
console.log(name); // ['Ryan', 'McDermott'];  
  
// ->  
  
function splitIntoFirstAndLastName(name) {  
  return name.split(' ');  
}  
  
const name = 'Ryan McDermott';  
const newName = splitIntoFirstAndLastName(name);  
  
console.log(name); // 'Ryan McDermott';  
console.log(newName); // ['Ryan', 'McDermott'];  
  
/*----------------------------------------------*/  
  
// 7. Avoid Side Effects v.2  
  
const addItemToCart = (cart, item) => {  
  cart.push({ item, date: Date.now() });  
};  
  
// ->  
  
const addItemToCart = (cart, item) => {  
  return [...cart, { item, date: Date.now() }];  
};  
  
/*----------------------------------------------*/  
  
// 8. Don't write to global functions  
  
Array.prototype.diff = function diff(comparisonArray) {  
  const hash = new Set(comparisonArray);  
  return this.filter(elem => !hash.has(elem));  
};  
  
// ->  
  
class SuperArray extends Array {  
  diff(comparisonArray) {  
  const hash = new Set(comparisonArray);  
  return this.filter(elem => !hash.has(elem));  
 }}  
  
/*----------------------------------------------*/  
  
// 9. Don't write to global functions  
  
if (fsm.state === 'fetching' && isEmpty(listNode)) {  
  // ...  
}  
  
// ->  
  
function shouldShowSpinner(fsm, listNode) {  
  return fsm.state === 'fetching' && isEmpty(listNode);  
}  
  
if (shouldShowSpinner(fsmInstance, listNodeInstance)) {  
  // ...  
}  
  
/*----------------------------------------------*/  
  
// 10. Avoid negative conditionals  
  
function isDOMNodeNotPresent(node) {  
  // ...  
}  
  
if (!isDOMNodeNotPresent(node)) {  
  // ...  
}  
  
// ->  
  
function isDOMNodePresent(node) {  
  // ...  
}  
  
if (isDOMNodePresent(node)) {  
  // ...  
}  
  
/*----------------------------------------------*/  
  
// 10. Avoid conditionals  
  
/*  
 This seems like an impossible task. Upon first hearing this, most people say,  "how am I supposed to do anything without an if statement?" The answer is that   
  you can use polymorphism to achieve the same task in many cases. The second question   
  is usually, "well that's great but why would I want to do that?" The answer is   
  a previous clean code concept we learned: a function should only do one thing.  
 When you have classes and functions that have if statements, you are telling your user that your  function does more than one thing. Remember, just do one thing.  
*/  
  
class Airplane {  
  // ...  
  getCruisingAltitude() {  
  switch (this.type) {  
  case '777':  
  return this.getMaxAltitude() - this.getPassengerCount();  
  case 'Air Force One':  
  return this.getMaxAltitude();  
  case 'Cessna':  
  return this.getMaxAltitude() - this.getFuelExpenditure();  
 } }}  
  
// ->  
  
class Airplane {  
  // ...  
}  
  
class Boeing777 extends Airplane {  
  // ...  
  getCruisingAltitude() {  
  return this.getMaxAltitude() - this.getPassengerCount();  
 }}  
  
class AirForceOne extends Airplane {  
  // ...  
  getCruisingAltitude() {  
  return this.getMaxAltitude();  
 }}  
  
class Cessna extends Airplane {  
  // ...  
  getCruisingAltitude() {  
  return this.getMaxAltitude() - this.getFuelExpenditure();  
 }}  
  
/*----------------------------------------------*/  
  
// 11. Don't over-optimize  
  
// On old browsers, each iteration with uncached `list.length` would be costly  
// because of `list.length` recomputation. In modern browsers, this is optimized.  
for (let i = 0, len = list.length; i < len; i++) {  
  // ...  
}  
  
// ->  
  
for (let i = 0; i < list.length; i++) {  
  // ...  
}

-----------------

### if/else propblem

const say = (word, sentence) => {  
  if (sentence) {  
  console.log(sentence);  
  return;  
 }  console.log(word);  
};  
  
say(null, 'sentence');  
  
->  
  
const say = (word, sentence) => {  
  if (word) {  
  console.log(word);  
 }  return (sentence) => {  
  console.log(sentence);  
  return;  
 }};  
  
const saySentence = say(null);  
  
saySentence('sentence');   
->  
  
const say = {  
  word(word) {  
  console.log(word);  
  return;  
 },  sentence(sentence) {  
  console.log(sentence);  
  return;  
 },  nothing() {  
  return;  
 }};  
  
const sayWord = (say['sentence'] || say.nothing);  
  
sayWord('word');

/*------------------- Objects and Data Structures ---------------------*/  
  
// 1. Use getters and setters  
  
/*  
Using getters and setters to access data on objects could be better than simply looking for a property on an object.  
"Why?" you might ask. Well, here's an unorganized list of reasons why:  
  
 When you want to do more beyond getting an object property, you don't have to look up and change every accessor in your codebase. Makes adding validation simple when doing a set. Encapsulates the internal representation. Easy to add logging and error handling when getting and setting. You can lazy load your object's properties, let's say getting it from a server.*/  
  
function makeBankAccount() {  
  // ...  
  
  return {  
  balance: 0,  
  // ...  
  };  
}  
  
const account = makeBankAccount();  
account.balance = 100;  
  
// ->  
  
function makeBankAccount() {  
  // this one is private  
  let balance = 0;  
  
  // a "getter", made public via the returned object below  
  function getBalance() {  
  return balance;  
 }  
  // a "setter", made public via the returned object below  
  function setBalance(amount) {  
  // ... validate before updating the balance  
  balance = amount;  
 }  
  return {  
  // ...  
  getBalance,  
  setBalance,  
 };}  
  
const account = makeBankAccount();  
account.setBalance(100);  
  
/*----------------------------------------------*/  
  
// 2. Use method chaining  
  
class Car {  
  constructor(make, model, color) {  
  this.make = make;  
  this.model = model;  
  this.color = color;  
 }  
  setMake(make) {  
  this.make = make;  
 }  
  setModel(model) {  
  this.model = model;  
 }  
  setColor(color) {  
  this.color = color;  
 }  
  save() {  
  console.log(this.make, this.model, this.color);  
 }}  
  
const car = new Car('Ford', 'F-150', 'red');  
car.setColor('pink');  
car.save();  
  
// ->  
  
class Car {  
  constructor(make, model, color) {  
  this.make = make;  
  this.model = model;  
  this.color = color;  
 }  
  setMake(make) {  
  this.make = make;  
  // NOTE: Returning this for chaining  
  return this;  
 }  
  setModel(model) {  
  this.model = model;  
  // NOTE: Returning this for chaining  
  return this;  
 }  
  setColor(color) {  
  this.color = color;  
  // NOTE: Returning this for chaining  
  return this;  
 }  
  save() {  
  console.log(this.make, this.model, this.color);  
  // NOTE: Returning this for chaining  
  return this;  
 }}  
  
const car = new Car('Ford', 'F-150', 'red')  
 .setColor('pink')  
 .save();  
  
  
/*----------------------------------------------*/  
  
// 2. Prefer composition over inheritance  
  
/*  
As stated famously in Design Patterns by the Gang of Four, you should prefer composition over inheritance where you can.  
There are lots of good reasons to use inheritance and lots of good reasons to use composition.The main point for this maxim is that if your mind instinctively goes for inheritance, try to think if composition could model your problem better.  
In some cases it can.  
  
You might be wondering then, "when should I use inheritance?" It depends on your problem at hand, but this is a decent list of when inheritance makes more sense than composition:  
  
Your inheritance represents an "is-a" relationship and not a "has-a" relationship(Human -> Animal vs.User -> UserDetails).  
You can reuse code from the base classes(Humans can move like all animals).  
You want to make global changes to derived classes by changing a base class.(Change the caloric expenditure of all animals when they move).  
*/  
  
class Employee {  
  constructor(name, email) {  
  this.name = name;  
  this.email = email;  
 }  
  // ...  
}  
  
// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee  
class EmployeeTaxData extends Employee {  
  constructor(ssn, salary) {  
  super();  
  this.ssn = ssn;  
  this.salary = salary;  
 }  
  // ...  
}  
  
// ->  
  
class EmployeeTaxData {  
  constructor(ssn, salary) {  
  this.ssn = ssn;  
  this.salary = salary;  
 }  
  // ...  
}  
  
class Employee {  
  constructor(name, email) {  
  this.name = name;  
  this.email = email;  
 }  
  setTaxData(ssn, salary) {  
  this.taxData = new EmployeeTaxData(ssn, salary);  
 }  // ...  
}  
  
  
/*--------------------------------------------------*/  
  
// A fast half sum:  
  
const hight = 555555555;  
const low = 222222222;  
const big = Math.floor((hight + low) / 2);  
const mid = Math.floor(low + ((hight - low) / 2)); // it`s better

---------------

1.
-------------------------------------------------------

Bad: setTimeout(blastOff, 86400000);
Good:
const MILLISECONDS_IN_A_DAY = 86400000;
setTimeout(blastOff, MILLISECONDS_IN_A_DAY);
-------------------------------------------------------

2.
-------------------------------------------------------

Bad:  car.carColor = 'Red';
Good: car.color = 'Red';
-------------------------------------------------------

3.
-------------------------------------------------------

------------------------------------------------

Bad: const createMicrobrewery = name =>  const breweryName = name || 'Hipster Brew Co.';
Good: const createMicrobrewery = (breweryName = 'Hipster Brew Co.') => {}

4.
-------------------------------------------------------------

Encapsulate conditionals

Bad: if (fsm.state === 'fetching') {}
Good:
const shouldShowSpinner (fsm, listNode) => fsm.state === 'fetching';
if (shouldShowSpinner(fsmInstance, listNodeInstance)) {}

5. Avoid negative conditionals

--------------------------------------------------------------

6. Function must do one thing

--------------------------------------------------------------

7. Don't use flags as function parameters
--------------------------------------------------------------

Bad:

function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}

->

Good:

function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}

--------------------------------------------------------------

8. Неплохо было бы взять за правило сохранять внешние значения в локальных переменных:

Доступ к пере­менной doc выполняется быстрее, чем к переменной document, потому что она является локальной переменной

document.getElementById("go-btn")

->

var doc = document;
doc.getElementByld('go-btn')

--------------------------------------------------------------

9. Mark optional parameters via the parameter default value `undefined`

function foo(optional = undefined) { ··· }

--------------------------------------------------------------

10. Mark required parameters via a function that throws an exception

function foo(required = throwException()) { ··· }

--------------------------------------------------------------

11. Enforcing a maximum arity (variant 1 of 2)

  function f(x, y, ...empty) { // max arity: 2
      if (empty.length > 0) {
          throw new Error();
      }
  }

--------------------------------------------------------------

12. Normalize variable

const hasEitherClass = (element, classNamel, className2) =>
   element.className == classNamel || element.className == className2;


->

const hasEitherClass = (element, classNamel, className2) => {
    const currentClassName = element.className;
    return currentClassName == classNamel || currentClassName == className2;
};

--------------------------------------------------------------

13. refactoring

  const getItem = () => {
    const item = myStorage.getItem('customKey');
    console.log(item);
  };

  takeItem.onclick = () => getItem();

  ->

  const getItem = () => myStorage.getItem('customKey');

  takeItem.onclick = () => {
    const item = getItem();
    console.log(item);
  }

--------------------------------------------------------------  

14. Для parseInt всегда определять систему исчесления

--------------------------------------------------------------

15. Для функции конструктора используются заглавные буквы

--------------------------------------------------------------

16. cache:

-> const len = arr.lenght

   for(cont i =0; i < len)

--------------------------------------------------------------   

17. 

function makeRegexParser(regex) {
    return regex.exec;
}
var parseSsn = makeRegexParser(/^\d{3}-\d{2}-\d{4}$/);
var parsePhone = makeRegexParser(/^\(\d{3}\)\d{3}-\d{4}$/);

//instead

var parseSsn = /^\d{3}-\d{2}-\d{4}$/.exec;
var parsePhone = /^\(\d{3}\)\d{3}-\d{4}$/.exec; 

--------------------------------------------------------------

18. 

// bad
function moreThanThree(number){
  if(number > 3){
  return true;
  } else {
  return "No. The number was only " + number + ".";
  }
};

// good
function moreThanThree(number){
  if(number > 3){
  return true;
  } else {
  return false;
  }
};


This function returns either a boolean or a string (see Figure 5-10). This isn’t
great because code that calls this function will likely have its own conditionals
to check for which type was returned.

--------------------------------------------------------------

19. 

Do-Nothing Code
Moving on to do-nothing code, we have an example of this in our file. The conditional
check that follows contains an unnecessary part:

if(!!(Object.keys(labelCounts).includes(label))){

->

if(Object.keys(labelCounts).includes(label)){

-------------------------------------------------------------

20. 

DUPLICATION IN CONDITIONALS: ANOTHER TYPE OF USELESS CODE

if(dog.weight > 40){
 buyFood('big bag');
 dog.feed();
}
else{
 buyFood('small bag');
 dog.feed();
}

-> 

if(dog.weight > 40){
 buyFood('big bag');
}
else{
 buyFood('small bag');
}

dog.feed();

-------------------------------------------------------------

21.

Long Lines:

probabilityOfChordsInLabels[difficulty][chord] = probabilityOfChords (line continues...)

->

var chordInstances = probabilityOfChordsInLabels[difficulty][chord];
chordInstances = probabilityOfChords ... 

-----------------------------------------------------------------

22.

Inlining Function Calls

function getNumberOfSongs(){
 return songs.length;
};

 var numberOfSongs = getNumberOfSongs();
 labelProbabilities[label] = labelCounts[label] / numberOfSongs;

->

 var numberOfSongs = songs.length;
 labelProbabilities[label] = labelCounts[label] / numberOfSongs;

----------------------------------------------------------------

23. 

// bad
const legthOfArray = array.length; // this case don`t need explanation

//good
const isWidthResizeable = width !== null; // but this need

----------------------------------------------------------------

24. Methods can return this to help with method chaining.

// bad
Jedi.prototype.jump = function () {
  this.jumping = true;
  return true;
};

Jedi.prototype.setHeight = function (height) {
  this.height = height;
};

const luke = new Jedi();
luke.jump(); // => true
luke.setHeight(20); // => undefined

// good
class Jedi {
  jump() {
    this.jumping = true;
    return this;
  }

  setHeight(height) {
    this.height = height;
    return this;
  }
}

const luke = new Jedi();

luke.jump()
  .setHeight(20);

----------------------------------------------------------

25. Do not use wildcard imports.

Why? This makes sure you have a single default export.

// bad
import * as AirbnbStyleGuide from './AirbnbStyleGuide';

// good
import AirbnbStyleGuide from './AirbnbStyleGuide';  

-----------------------------------------------------------

26. And do not export directly from an import.

Why? Although the one-liner is concise, having one clear way to import and one clear way to export makes things consistent.

// bad
// filename es6.js
export { es6 as default } from './AirbnbStyleGuide';

// good
// filename es6.js
import { es6 } from './AirbnbStyleGuide';
export default es6;

-----------------------------------------------------------

27. Only import from a path in one place. eslint: no-duplicate-imports

Why? Having multiple lines that import from the same path can make code harder to maintain.

// bad
import foo from 'foo';
// … some other imports … //
import { named1, named2 } from 'foo';

// good
import foo, { named1, named2 } from 'foo';

// good
import foo, {
  named1,
  named2,
} from 'foo';

------------------------------------------------------------------

28. In modules with a single export, prefer default export over named export. eslint: import/prefer-default-export

Why? To encourage more files that only ever export one thing, which is better for readability and maintainability.

// bad
export function foo() {}

// good
export default function foo() {}

---------------------------------------------

29. Multiline imports should be indented just like multiline array and object literals.

Why? The curly braces follow the same indentation rules as every other curly brace block in the style guide, as do the trailing commas.

// bad
import {longNameA, longNameB, longNameC, longNameD, longNameE} from 'path';

// good
import {
  longNameA,
  longNameB,
  longNameC,
  longNameD,
  longNameE,
} from 'path';

---------------------------------------------

30. Use one const or let declaration per variable. eslint: one-var

Why? It’s easier to add new variable declarations this way, and you never have to worry about swapping out a ; for a , or introducing punctuation-only diffs. You can also step through each declaration with the debugger, instead of jumping through all of them at once.

// bad
const items = getItems(),
    goSportsTeam = true,
    dragonball = 'z';

// bad
// (compare to above, and try to spot the mistake)
const items = getItems(),
    goSportsTeam = true;
    dragonball = 'z';

// good
const items = getItems();
const goSportsTeam = true;
const dragonball = 'z';

---------------------------------------------

31. Avoid using unary increments and decrements (++, --). eslint no-plusplus

Why? Per the eslint documentation, unary increment and decrement statements are subject to 
automatic semicolon insertion and can cause silent errors with incrementing or decrementing 
values within an application. It is also more expressive to mutate your values with statements 
like num += 1 instead of num++ or num ++. Disallowing unary increment and decrement statements 
also prevents you from pre-incrementing/pre-decrementing values unintentionally which can 
also cause unexpected behavior in your programs.

--------------------------------------------------

32. Avoid unneeded ternary statements. eslint: no-unneeded-ternary

// bad
const foo = a ? a : b;
const bar = c ? true : false;
const baz = c ? false : true;

// good
const foo = a || b;
const bar = !!c;
const baz = !c;

--------------------------------------------------

33. start all comments with a space to make it easier to read. eslint: spaced-comment

---------------------------------------------------

34. Do not pad your blocks with blank lines. eslint: padded-blocks

// bad
function bar() {

  console.log(foo);

}

// good
function bar() {
  console.log(foo);
}

---------------------------------------

35. Do not add spaces inside parentheses. eslint: space-in-parens

// bad
function bar( foo ) {
  return foo;
}

// good
if (foo) {
  console.log(foo);
}

---------------------------------------

36. Add spaces inside curly braces. eslint: object-curly-spacing

// bad
const foo = {clark: 'kent'};

// good
const foo = { clark: 'kent' };

---------------------------------------

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
37. Do not use trailing or leading underscores. eslint: no-underscore-dangle

Why? JavaScript does not have the concept of privacy in terms of properties or methods. Although a leading underscore is a common convention to mean “private”, in fact, these properties are fully public, and as such, are part of your public API contract. This convention might lead developers to wrongly think that a change won’t count as breaking, or that tests aren’t needed. tl;dr: if you want something to be “private”, it must not be observably present.

// bad
this.__firstName__ = 'Panda';
this.firstName_ = 'Panda';
this._firstName = 'Panda';

// good
this.firstName = 'Panda';

// good, in environments where WeakMaps are available
// see https://kangax.github.io/compat-table/es6/#test-WeakMap
const firstNames = new WeakMap();
firstNames.set(this, 'Panda');

---------------------------------------

38. A base filename should exactly match the name of its default export.

39. 
var siteUrl = "http://refactoringjs.com";
$('.my-button').on('click', function(){
 window.location = siteUrl;
});
$('.other-button').on('click', function(){
 window.location = siteUrl;
});

This does make it easier to change siteUrl in the future (assuming it is
used in other places), but we can reduce duplication further by extracting a
function:

var siteUrl = "http://refactoringjs.com";
function visitSite(){
 window.location = siteUrl;
}
$('.my-button').on('click', visitSite);
$('.other-button').on('click', visitSite);

----------------------------------------------------

// Function callers and callees should be close

Bad:

class PerformanceReview {
    constructor(employee) {
        this.employee = employee;
    }

    lookupPeers() {
        return db.lookup(this.employee, 'peers');
    }

    lookupManager() {
        return db.lookup(this.employee, 'manager');
    }

    getPeerReviews() {
        const peers = this.lookupPeers();
        // ...
    }

    perfReview() {
        this.getPeerReviews();
        this.getManagerReview();
        this.getSelfReview();
    }

    getManagerReview() {
        const manager = this.lookupManager();
    }

    getSelfReview() {
        // ...
    }
}

const review = new PerformanceReview(employee);
review.perfReview();

Good:

class PerformanceReview {
    constructor(employee) {
        this.employee = employee;
    }

    perfReview() {
        this.getPeerReviews();
        this.getManagerReview();
        this.getSelfReview();
    }

    getPeerReviews() {
        const peers = this.lookupPeers();
        // ...
    }

    lookupPeers() {
        return db.lookup(this.employee, 'peers');
    }

    getManagerReview() {
        const manager = this.lookupManager();
    }

    lookupManager() {
        return db.lookup(this.employee, 'manager');
    }

    getSelfReview() {
        // ...
    }
}

const review = new PerformanceReview(employee);
review.perfReview();

/* ---------------------------------------------------- */

// I/O functions should have failure cases handled

// Any function that does I/O should handle when something goes wrong

function getIngredientsFromFile() {
    const onFulfilled = (buffer) => {
        let lines = buffer.split('\n');
        return lines.forEach(line => <Ingredient ingredient={line} />)
    };

    // What about when this rejected because of an error? What do we return?
    return readFile('./ingredients.txt').then(onFulfilled);
}

/* ---------------------------------------------------- */

// User input should be limited

/* 
Users can potentially input an unlimited amount of data to send to you. It's important to set limits 
if a function takes any kind of user data in.
*/

router.route('/message').post((req, res) => {
    const message = req.body.content;

    // What happens if the message is many megabytes of data? Do we want to store
    // that in the database? We should set limits on the size.
    db.save(message);
});

/*------------------------------------------------------------*/

// XSS should not be possible

/* 
Cross-site scripting (XSS), is one of the largest vectors for security attacks on a web application. 
It occurs when you take user data and include it in your page without first properly sanitizing it. 
This can cause your site to execute source code from remote pages.
*/

function () {
  let badge = document.getElementsByClassName('badge');
  let nameQueryParam = getQueryParams('name');

  /**
    * What if nameQueryParam was `<script>sendCookie(document.cookie)</script>`?
    * If that was the query param, a malicious user could lure a user to click a
    * link with that as the `name` query param, and have the user unknowingly
    * send their data to a bad actor.
    */
  badge.children[0].innerHTML = nameQueryParam;
}



/*-----------------------------------------------------------*/

tableColumns.length > 0 -> tableColumns.length 

---------------------------------------------------------------


        // good
        let node;
        if (this.props.columnTreeNode) {
            node = this.props.dataNode;
        } else if (this.props.treeNode) {
            node = this.props.treeNode;
        }

        const isCellSelected = node && node.state && node.state.selected;


        // bad
        const isCellSelected =
            (this.props.treeNode && this.props.treeNode.state && this.props.treeNode.state.selected) ||
            (this.props.dataNode && this.props.dataNode.state && this.props.dataNode.state.selected);

-----------------------------------------------------

columnTreeNode.userType === 'subTotal' || rowTreeNode.userType === 'subTotal'

->

(columnTreeNode.userType || rowTreeNode.userType) === 'subTotal';

--------------------------------------------------------

    isSubTotals({
        rowTreeNode, userType, dataNode,
    }) {
        const isDataCell = (typeof rowTreeNode !== 'undefined');
        if (isDataCell) {
            return userType === 'subTotal';
        }

        if (!isDataCell) {
            return dataNode.userType === 'subTotal';
        }
    }   


-----------------------------------------------------

  const db = {
	data: 'some data',
};

const getData = (db) => {
	const changedData = db.data + ' with some changes';
  
  return () => {
  	console.log('write this data to fs ->', changedData)
  }
};

const writeChangedDataToFs = getData(db);

writeChangedDataToFs();
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5NjAxOTYzMTUsMTgwNjA4OTA0Myw1Mj
AxMjA0ODAsMTUyMTk3ODg4M119
-->