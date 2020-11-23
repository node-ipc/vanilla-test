# vanilla-test

This is a minimalist and pure js testing utility/suite/framework. It will work with ES6+ modules and common.js modules. The `VanillaTest` class is perfect for extending and very fast. There is no boilerplate, no fancy setup, just write your tests and run them from your npm test command.

npm info :  
![vanilla-test npm version](https://img.shields.io/npm/v/vanilla-test.svg) ![total npm downloads for vanilla-test](https://img.shields.io/npm/dt/vanilla-test.svg) ![monthly npm downloads for vanilla-test](https://img.shields.io/npm/dm/vanilla-test.svg)

GitHub info :  
![vanilla-test GitHub Release](https://img.shields.io/github/release/RIAEvangelist/vanilla-test.svg) ![GitHub license vanilla-test license](https://img.shields.io/github/license/RIAEvangelist/vanilla-test.svg) ![open issues for vanilla-test on GitHub](https://img.shields.io/github/issues/RIAEvangelist/vanilla-test.svg)

Build Info :  
Travis CI (linux,windows & Mac) : [![Build Status](https://travis-ci.org/RIAEvangelist/vanilla-test.svg?branch=main)](https://travis-ci.org/RIAEvangelist/vanilla-test) Appveyor CI (Windows) : [![vanilla-test windows build status](https://ci.appveyor.com/api/projects/status/github/riaevangelist/vanilla-test?branch=main&svg=true)](https://ci.appveyor.com/project/RIAEvangelist/vanilla-test/history)

***Super light and fast*** Extensible pure JS testing for the win! Vanilla Test works in node, browsers, electron, anywhere JS runs.

## Core Test Methods
|method|args|returns|description|
|-|-|-|-|
|expects|`description`:`string` a unique test descriptor |`string` : numbered test descriptor|this sets up the current test|
|pass|`strict`:`boolean` throw if the test already passed or failed previously. This defaults to `false`|`string` : numbered test descriptor|call this to pass the test|
|fail|`strict`:`boolean` throw if the test already passed or failed previously. This defaults to `false`|`string` : numbered test descriptor|call this to fail the test|
|done||`string` : numbered test descriptor|Ends the test. If the test has not yet passed|failed, it will fail|
|report|`CI`:`boolean` defaults to `true`. This will try to exit after reporting letting your CI know the test is complete in node, or return the number of failures in the browser. ***If set to `false`,*** it will return the `results`:`object` for you to use. This could be used for report builders or other integrations. `{passed:[...strings], failed:[...strings]}`|report : `string` : `report`|report showing passed and failed tests. This will also communicate with your CI like circle CI or Travis CI|
|delay|`delay`:`number` number of times to itterate|chainable ref|this can be used to waste some time while waiting for short async operations to execute, like event propagation |
|compare|arg1:`any`, arg2:`any`, error:`string`|chainable ref| inherited [stong-type](https://github.com/RIAEvangelist/strong-type) .compare method which checks arg1==arg2 and throws  |


## Strong Type Checking
`vanilla-test` uses the `strong-type` class which provides methods to test ***all*** the built in js primatives, objects, classes, and even fancy things like async functions and generators.

[full strong-type documentation](https://github.com/RIAEvangelist/strong-type)

The `strong-type` methods are available on the `.is` method.

```js

import VanillaTest from './node_modules/vanilla-test/index.js';

//not actually running tests in this example, just demonstrating
//strong type checking

const test=new VanillaTest;

test.is.string('hello');

test.is.number(1);

test.is.int8Array(new Int8Array(3));

test.is.asyncFunction(async function(){});

test.is.asyncGeneratorFunction(async function*(){});


function* genFunc(){}
let generator=genFunc();

test.is.generatorFunction(genFunc);

test.is.generator(generator);


```

## Basic Examples

These basic examples should be enough to get you started testing right away.

To prepare the browser and node example folder, first run `npm run emulate`. This will make an emulated install with dependancies in the appropriate directories. We need to emulate an actual install for relative paths to work from inside the module itself, as if it was installed in a project on a server somewhere.

```js
//import with relative paths to shim for browser
//this way the same code will work on the web as it does in node
//litteraly the same file without even transpiling,  
//but you can transpile if you want.
import VanillaTest from './node_modules/vanilla-test/index.js';

const test=new VanillaTest;

//setup
const num1=1;
const num2=2;
function sum(a,b){
    return a+b;
}


// 1) expects num1 to be a number
try{
    test.expects('num1 to be a number');    
    test.is.number(num1);
}catch(err){
    console.log(`${err.name} : ${err.message}`);
    test.fail();
}
test.pass();
test.done();



// 2) expects num2 to be a a number
try{
    test.expects('num2 to be a a number');    
    test.is.number(num2);
}catch(err){
    console.log(`${err.name} : ${err.message}`);
    test.fail();
}
test.pass();
test.done();



// 3) expects num1 == num2
//this test should fail for demonstration purposes
try{
    test.expects('num1 == num2');    
    test.compare(num1,num2);
}catch(err){
    console.log(`test.compare(${num1},${num2}); : num1 and num2 were not equal`);
    test.fail();
}
test.pass();
test.done();



// 4) expects num1 == num2
try{
    test.expects('sum(num1,num2) to be equal to num1+num2');    

    test.compare(
        sum(num1,num2),
        num1+num2
    );
}catch(err){
    console.log(`sum(${num1},${num2}); was not equal to num1+num2`);
    test.fail();
}
test.pass();
test.done();



// 5) expects A TypeError when type checks fail
try{
    test.expects('A TypeError when type checks fail');    
    test.boolean(new Array(2));
}catch(err){
    try{
        test.is.typeError(err);
    }catch{
        console.log(`${err.name} : ${err.message}`);
        test.pass();
    }
    test.fail();
}
test.fail();
test.done();



//Lets take a look at the test results and let our CI know 
// that the tests have completed and passed or failed
test.report();


```

## Node example

Since we use the same files for node and the browser, we need to emulate a production `npm i vanilla-test` in the example folder, so be sure to :  

first run `npm run emulate`

then run `node ./example/node/basic.js`

![screen shot of vanilla-test example on node](https://raw.githubusercontent.com/RIAEvangelist/vanilla-test/main/example/img/vanilla-test-node-report.PNG)

## Browser example

run `npm start` then go to [the local example](http://localhost:8000/example/index.html) : http://localhost:8000/example/index.html. It actually imports the node example into the browser and runs it, same exact file, no transpiling or custom code for the browser. If you want to transpile though, you can.

#### Chrome

![screen shot of vanilla-test example on chrome](https://raw.githubusercontent.com/RIAEvangelist/vanilla-test/main/example/img/vanilla-test-chrome-report.PNG)

#### Edge

![screen shot of vanilla-test example on edge](https://raw.githubusercontent.com/RIAEvangelist/vanilla-test/main/example/img/vanilla-test-edge-report.PNG)

## Tests & Reports

You can run the modules tests and see the reports in either node and the browser. Node will run the tests in the node environment, and browsers will actually imort the node code and run it in the browser.

#### Node

run `npm test` you will see the reults right there in the terminal.

#### Browser

run `npm start` then go to [the local test](http://localhost:8000/test/index.html) : http://localhost:8000/example/index.html. It actually imports the node test into the browser and runs it, same exact file, no transpiling or custom code for the browser. If you want to transpile though, you can.

## Local website

`npm start` actually starts a [node-http-server](https://github.com/RIAEvangelist/node-http-server). So if you just want quick links to the example and test web pages, there is a page in the root of this module with links. You can access it by going to the [local homepage](http://localhost:8000) : http://localhost:8000