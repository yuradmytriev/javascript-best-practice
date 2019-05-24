
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
const obj = { foo: 'bar' };
for (let key in obj) {
    if (Object.prototype.hasOwnProperty.call(obj, key)) {
        console.log(key, obj[key]);
    }
}
```

#### Good

```js
const obj = { foo: 'bar' };
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
const err = new Error('ValidationError: Result is not valid');
console.error(err.toString());
// => Error: ValidationError: Result is not valid

#### Good
const err = new Error('Result is not valid');
err.name = 'ValidationError';
console.error(err.toString());
// => ValidationError: Result is not valid

--------------

### Avoid long argument list

Use a single object parameter and destructuring assignment instead. It also makes handling optional parameters much easier.

#### Bad

```js
function getRegisteredUsers (fields, include, fromDate, toDate) { /* implementation */ }
getRegisteredUsers(['firstName', 'lastName', 'email'], ['invitedUsers'], '2016-09-26', '2016-12-13')
```

#### Good

```js
function getRegisteredUsers ({ fields, include, fromDate, toDate }) { /* implementation */ }
getRegisteredUsers({
  fields: ['firstName', 'lastName', 'email'],
  include: ['invitedUsers'],
  fromDate: '2016-09-26',
  toDate: '2016-12-13'
})
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
 }});  
```

#### Good

```js
var allChords = new Set();
 
chords.forEach(chord => allChords.add(chord));
```

-----------------

// 2. optimize cycle  
  
for (let i = 0; i <= 10; i++) {  
  if (i === 5) {  
  break;  
 }  console.log(i);  
}

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
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTE4MjEyMjk3LDE4MDYwODkwNDMsNTIwMT
IwNDgwLDE1MjE5Nzg4ODNdfQ==
-->