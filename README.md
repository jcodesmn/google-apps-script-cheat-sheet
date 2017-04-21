# Google Apps Script Library

[General](#general)
* [Array](#array)
  * [Check for a Value](#check-for-a-value)
  * [Remove Duplicates](#remove-duplicates)
  * [Remove Empty Values](#remove-empty)
  * [Get Count of Values](#get-count-of-values)
  * [Intersect of Two Arrays](#intersect-of-two-arrays)
  * [Compare Two Arrays](#compare-two-arrays)
  * [Array as Delimited String](#array-as-delimited-string)
  * [Array as Modified Delimited String](#array-as-modified-delimited-string)

## General

### Array

#### Check for a Value | return: `boolean`

```javascript
function checkValIn(arr, val) { 
  return arr.indexOf(val) > -1; 


var arr_cvi = [1,2,3,4];
Logger.log(checkValIn(arr_cvi,5)); // false
```

#### Remove Duplicates | return: `array`

```javascript
function rmDuplicatesFrom(arr) {
  var check  = {};
  var _arr = [];
  var j = 0;
  for(var i = 0; i < arr.length; i++) {
    var item = arr[i];
    if(check[item] !== 1) {
      check[item] = 1;
      _arr[j++] = item;
    }
  }
  return _arr;
}

var arr_rdf = [1,2,3,1,2,3,4,];
Logger.log(rmDuplicatesFrom(arr_rdf)); // [1,2,3,4]
```

#### Remove Empty Values | return: `array`

```javascript
function rmEmptyVal(x){
  return (x !== (undefined || ''));
}

var arr_rev = ["a",,"b",,,"c"];
Logger.log(arr_rev.filter(rmEmptyVal)); // [a,b,c]
```

#### Get Count of Values | return: `array (objects)`

```javascript
function countOfValIn(arr){
  var _arr = [];
  var copy = arr.slice(0);
  for (var i = 0; i < arr.length; i++) {
    var myCount = 0;  
    for (var w = 0; w < copy.length; w++) {
      if (arr[i] == copy[w]) {
        myCount++;
        delete copy[w];
      }
    }
    if (myCount > 0) {
      var obj = new Object();
      obj.value = arr[i];
      obj.count = myCount;
      _arr.push(obj);
    }
  }
  return _arr;
}

var arr_covi  = ["A", "B", "C", "A", "B", "C", "A"];
Logger.log(countOfValIn(arr_covi)); // {count=3.0, value=A}, {count=2.0, value=B}, {count=2.0, value=C}]
```

#### Intersect of Two Arrays | return: `array`

```javascript
function intersectOf(arrA, arrB) {
  var a = 0;
  var b = 0;
  var _arr = [];
  while( a < arrA.length && b < arrB.length ) {
     if (arrA[a] < arrB[b] ) { a++; }
     else if (arrA[a] > arrB[b] ) { b++; }
     else {
       _arr.push(arrA[a]);
       a++;
       b++;
     }
  }
  return _arr;
}

var arr1_io = [1, 2, 3];
var arr2_io = [3, 4, 5];
Logger.log(intersectOf(arr1_io, arr2_io)); // [3]
```

#### Compare Two Arrays | return: `boolean`

```javascript
function compareArr(arr1, arr2) {
    if(arr1.length !== arr2.length) return false;
    for(var i = arr1.length; i--;) {
        if(arr1[i] !== arr2[i]) return false;
    }
    return true;
}

var arr1_ca = [1,2,3,4,5]
var arr2_ca = [1,2,3,4,5]
var arr3_ca = ["a","b","c","d","e"]
Logger.log(compareArr(arr1_ca, arr2_ca)); // true
Logger.log(compareArr(arr1_ca, arr3_ca)); // false
```

#### Array as Delimited String | return: `string`

```javascript
function delimited(arr, delimiter){
  var _arr = rmDuplicatesFrom(arr).sort();
  var str  = "";
  for (var i = 0; i < _arr.length; i++) {
    str += _arr[i] + delimiter + "  ";
  }
  str = str.slice(0, -2);
  return str;
}

var arr_clf = ["c@example.com", "b@example.com", "a@example.com"];
Logger.log(commaListFrom(arr_clf, ",")); // a@example.com, b@example.com, c@example.com
```

#### Array as Modified Delimited String | return: `string`

```javascript
function delimitedModified(arr, extension, delimiter) {
  var _arr = rmDuplicatesFrom(arr).sort();
  var str  = "";
  for (var i = 0; i < _arr.length; i++) {
    str += _arr[i] + extension + delimiter + " "; 
  }
  str = str.slice(0, -2);
  return str;
}

var arr_clfd = ["x", "z", "y"];
Logger.log(delimitedModified(arr_clfd, "@example.com", ",")); // x@example.com, y@example.com, z@example.com
```

### Multidimensional Array

#### Flatten Multidimensional Array | return: `array`

```javascript
function flattenMultiArr(multiArr){
  var arr = multiArr.reduce(function(a, b) {
    return a.concat(b);
  });
  return arr;
}

var ss_fma  = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Sheet1");
var val_fma = ss_fma.getRange("G2:H5").getValues();
Logger.log(flattenMultiArr(val_fma).sort()); // [1, 2, 3, 4, 5, 6, 7, 8]
```

### Array of Objects

```javascript
var ex_arrObj = [
{a: 1000, b: 1, c: 5}, 
{a: 10000, b: 2, c: 5000}, 
{a: 10, b: 2, c: 500},
{a: 1, b: 1, c: 50}
]
```

#### Sort by Property or Properties | return: `array (objects)`

```javascript
function dynSort(prop) {
  var sortOrder = 1;
  if(prop[0] === "-") {
    sortOrder = -1;
    prop = prop.substr(1);
  }
  return function (a,b) {
    var result = (a[prop] < b[prop]) ? -1 : (a[prop] > b[prop]) ? 1 : 0;
    return result * sortOrder;
  }
}

Logger.log(ex_arrObj.sort(dynSort("a"))); 
// [{a=1.0, b=1.0, c=50.0}, {a=10.0, b=2.0, c=500.0}, {a=1000.0, b=1.0, c=5.0}, {a=10000.0, b=2.0, c=5000.0}]

function dynSortM() {
  var props = arguments;
  return function (obj1, obj2) {
    var i = 0, result = 0, numberOfProperties = props.length;
    while(result === 0 && i < numberOfProperties) {
      result = dynSort(props[i])(obj1, obj2);
      i++;
    }
    return result;
  }
}

Logger.log(ex_arrObj.sort(dynSortM("b", "c"))); 
// [{a=1000.0, b=1.0, c=5.0}, {a=1.0, b=1.0, c=50.0}, {a=10.0, b=2.0, c=500.0}, {a=10000.0, b=2.0, c=5000.0}]
```

#### Find Object With Unique Property Value | return: `object / value`

```javascript
function findObjIn(arrObj, pQuery, val) {
  for (var i = 0; i < arrObj.length; i++) {
    var obj = arrObj[i];
    for (var prop in obj) {
      if (obj.hasOwnProperty(pQuery) && prop == pQuery && obj[prop] == val) {
        return obj;
      }
    }
  }
}

Logger.log(findObjIn(ex_arrObj,"a",1000)); // {a=1000.0, b=1.0, c=5.0}

function findObjValIn(arrObj, pQuery, val, pReturn) {
  for (var i = 0; i < arrObj.length; i++) {
    var obj = arrObj[i];
    for (var prop in obj) {
      if (obj.hasOwnProperty(pQuery) && prop == pQuery && obj[prop] == val) {
        return obj[pReturn];
      }
    }
  }
}

Logger.log(findObjValIn(ex_arrObj, "c", 500, "a")); // 10
```
