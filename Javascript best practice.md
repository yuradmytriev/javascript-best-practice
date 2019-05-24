
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
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTIwMTIwNDgwLDE1MjE5Nzg4ODNdfQ==
-->