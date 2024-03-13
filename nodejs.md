# NodeJS Nodes

## NPM Intro
### Intialize
+ ```npm init``` will create package.json file
+ ```npm install``` will install dependency in package.json file
+ ```npm install <package-name>``` will install the named package and add it to package.json dependecies
    + ```--save``` not mandatory, install and adds the pakcage to dependencies, shorthand **-S**
    + ```--save-dev``` install and adds the package to devDependencies, shorthand **-D**
    + ```--no-dev``` install but doesn't add to package.json
    + ```--save-optional``` install and adds the package to optionalDependencies, shorthand **-O**
    + ```no-optional``` skip installing optional packages
    + difference between devDependency and dependency is that devDependency conatains testing and development tools and dependency contains pakckage needed for production

### Update
+ ```npm update``` for update all packages
+ ```npm update <pacakge-name>``` for updating specific package

### Versioning
+ npm follows semantic versioning **semvar**
+ ```npm install <package-name>@<version>``` for installing a package with specific version

### Running Tasks
+ npm provides a way to running taks with ```npm run <task-name>```
+ the tasks should be created in package.json under "scripts" property

## Node and ES6
+ ```node -p process.versions.v8``` to check v8 version of the node

## Node environments
+ node alwasy consider it's running on development mode
+ in order to set node to production mode set **NODE_ENV=production**
+ `npx` node package execute - helps us to run prebuild commands

## Best Practices
+ keep `.npmignore` or `.gitignore` file in project to prevent unnessery files to upload to npm when publish
+ handle on error events to prevent server from crashing when create server