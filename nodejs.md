# NodeJS Notes

### NPM Intro
#### Intialize
+ ```npm init``` will create package.json file
+ ```npm install``` will install dependency in package.json file
+ ```npm install <package-name>``` will install the named package and add it to package.json dependecies
    + ```--save``` not mandatory, install and adds the pakcage to dependencies, shorthand **-S**
    + ```--save-dev``` install and adds the package to devDependencies, shorthand **-D**
    + ```--no-dev``` install but doesn't add to package.json
    + ```--save-optional``` install and adds the package to optionalDependencies, shorthand **-O**
    + ```no-optional``` skip installing optional packages
    + difference between devDependency and dependency is that devDependency conatains testing and development tools and dependency contains pakckage needed for production

#### Update
+ ```npm update``` for update all packages
+ ```npm update <pacakge-name>``` for updating specific package

#### Versioning
+ npm follows semantic versioning **semvar**
+ ```npm install <package-name>@<version>``` for installing a package with specific version

#### Running Tasks
+ npm provides a way to running taks with ```npm run <task-name>```
+ the tasks should be created in package.json under "scripts" property

### Node and ES6
+ ```node -p process.versions.v8``` to check v8 version of the node

## Node environments
+ node alwasy consider it's running on development mode
+ in order to set node to production mode set **NODE_ENV=production**
+ `npx` node package execute - helps us to run prebuild commands

## Best Practices
+ keep `.npmignore` or `.gitignore` file in project to prevent unnessery files to upload to npm when publish
    + also before pulish alwasy run `npm pulish --dry-run` to check all the files to be published
+ handle on error events to prevent server from crashing when create server
+ configure both proxy server and back-end server to handle ambiguous http request

## Asynchronous Work
+ 3 async pattern for node

### 1. In series
this is same like running for loop and executing functions in a async manner example code below
```javascript
// these operations were defiend else where 
const operations = [
    {func: function1, arg: arg1},
    {func: function2, arg: arg2},
    {func: function3, arg: arg3},
    ...
];

function executeFunctionWithArgs(operation, callback) {
    const {func, arg} = operation;
    func(arg, callback);
}

function serialProcedure(operation) {
    if (!operatio) process.exit(0);
    executeFunctionWithArgs(operation, (result) => {
        // after the current operation is done call the current operation
        serialProcedure(operations.shift());
    });
}

serialProcedure(operations.shift()); // first operation call here
```

### 2. In Parallel
If the order is not an issue scenarios, like sending mail to 100k recipients. an example code is below,
```javascript
let count = 0;
let success = 0;
let failed = [];

const recipients = [
    {name: user1, mail: user1@mail.com},
    {name: user2, mail: user2@mail.com},
    {name: user3, mail: user3@mail.com},
    {name: user4, mail: user4@mail.com},
    ...
];

recipients.forEach(user => {
    dispatch(user, (err) => {
        if (!err) success++
        else failed.push(user.name);
        count++;
        if (count === recipients.length) {
            showResults(
                count, 
                success,
                failed
            )
        }
    })
});

function dispatch(user, callback) {
    sendMail(
        {
            subject: "mail subject",
            message: "message of the mail",
            smtp: user.mail,
        },
        callback
    );
}

function showResult(count, success failed) {
    console.log(`All Mail count ${count}, successful mails: ${success} \n and failed to send to ${failed.join('\n')}\n`);
}
```

### 3. Limited Parallel
this is a combination of In parallel inside an In series
Example case: emailing 1,000,000 recipients from a list of 10E7 users
```javascript
let successCount = 0;
function final() {
  console.log(`dispatched ${successCount} emails`);
  console.log('finished');
}
function dispatch(recipient, callback) {
  // `sendEmail` is a hypothetical SMTP client
  sendMail(
    {
      subject: 'Dinner tonight',
      message: 'We have lots of cabbage on the plate. You coming?',
      smtp: recipient.email,
    },
    callback
  );
}
function sendOneMillionEmailsOnly() {
  getListOfTenMillionGreatEmails(function (err, bigList) {
    if (err) throw err;
    function serial(recipient) {
      if (!recipient || successCount >= 1000000) return final();
      dispatch(recipient, function (_err) {
        if (!_err) successCount += 1;
        serial(bigList.pop());
      });
    }
    serial(bigList.pop());
  });
}
sendOneMillionEmailsOnly();
```

### Overview of Blocking vs Non-Blocking
+ Blocking code stops the execution of javascript threat until a non js operations is finished like file reading, fetching etc,.
+ concurrency in js: js is a single threaded language so concurrency means here is the ability of event loop to run callbacks
+ avoid mixing async and sync methods

### Javascript async programming
+ javascirpt in single threaded and synchronous by default
+ callbacks are extensively used in js, timer functions etc,.
+ as a good practice all callback have first paramter for hanlding errors
+ encourage to use promises and async/await keywords

### The NodeJS EventLoop
+ **Timers phase**
  + timer callback functions are executed here, after timeout happend the callbacks are added here by 
  + timer phase controls when the timer function are called
+ **pending callbacks**
  + this phase is for callback from the system
+ **poll**
  + in this phase if poll is not empty all the callbacks in queue are executed till all are completed or system limit reached
  + it will check any new timers are ready to execute if yes then start from firsrt phase
+ **check**
  + this phase allows a person to immediately run a callback after poll phase
  + if the poll phase is empty and there's no setImmediate timer this phase is next
+ **close callback**
  + case where process.destroy() called or socket or handle closed 'close' event emitted via process.nextTick()

### The NodeJS EventEmitter
+ creating an eventEmitter
```javascript
const EventEmitter = require('node:events');
const eventEmitter = new EventEmitter();
```
+ adding an event and a handler
```javascript
eventEmitter.on('start', () => console.log('start event'));
```
+ starting the event
```javascript
eventEmitter.emit('start');
```
+ there are also other methds as `off` and `removeListener` to remove a specific handler for an event and `removeAllHandler` to remove all hadler for an event and `once` for running a hanlder only once.

### process.nextTick()
+ the one round of node event phase is called one tick, so calling nextTick for a callback is called at the end of the tick
+ nextTick takes precedence before setTimeout
+ the ranking list of precedence in event loop 
  1. nextTick 
  2. setTimout
  3. setImmediate (in some cases as where it is called it would be before timer methods)

### setImmediate
+ setImmediate is the same as setTimeout(() => {}, 0) both are called at the next tick process.
+ there are four asyn wasy to run a function
  + timer method (setTimeout)
  + setImmediate
  + process.nextTick
  + Promise.then
+ so the timer methods and setImmediate are added to the macrotask queue
+ and the promise will be added to the microtask queue
+ and process.nextTick will be added to the nextTick queue
+ on execution first nextTick queue is ran, then microtask queue which is promise, then macrotask queue timer function are called

### tips for not blocking event loop
+ avoid using complex regex which may lead to redos attack
+ avoid using the librarires
  + compression
  + encryption
  + file system
  + child precesses
+ also json parsing which may take longer time for larger input event if it is a O(n) operation
+ in case of running intesive computation move tha taks to worker pool

### Manipulation files
+ fs.stat: this method returns the meta data about an file like is it a file or directory or symlink or get it's size
+ fs.path: contains the methods for managing paths in our application
+ fs.open: avails us options to open a file with a descriptor (r, r+, w+, a, a+)
+ fs.readFile: i/o api for reading file it also has promise and sync verions
+ fs.writeFile: i/o api for writing file it also has promise and sync verions and by default it clears the file before writing use 'a' flag for avoiding that
+ fs also has methods for handling folder
  + fs.access() check a directory exists
  + fs.readdir() reads a directory
  + fs.rename() for renaming folder
  + fs.rmdir() for removing folder

## command line
+ use `node app.js` command to run js files
+ or make the file executable and add a shitbang at the top the file
+ use `--watch` parameter to automatically restart server whenver the file changes
+ send env variable's for node app by like this
`USER_ID:1234 node app.js`
+ this variable will avialabe at `process.env.USER_ID`
+ there's a experimental flas `--env-file=.env` which will add var's in .evn file automatically
+ more than one env file can be added
