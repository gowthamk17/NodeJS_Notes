# NPM - Javascript package manager
+ used to install, publish, discover, manage packages
+ `npm install` to install packages from registry
+ `npm search` to check for packages in regeistry
+ `npm ls` to show all the current repo's installed packages
+ by default installing node package will put inside ./node_modules directory
+ if used with -g flag it will install the package in a npm specific directory
+ `npm link` links the current working directory to nodes global path so we don't need to install on everytime
+ `npm publish` to upload the package to registry
+ `npm access` set access level on published packages
+ `npm diff` shows diff between pulished package with current package
+ `npm init` creates package.json file
+ `npm uninstall` removes a package from both package.json, package-lock.json, and ./node_modules
+ `npx <command>` runs a command from local or remote npm package
+ `.npmrc` npm config file placed in $HOME folder