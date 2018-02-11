# Use ES6 feature in Node with the help of Babel.

- [Babel][https://babeljs.io/]

  ​

## Getting start

```bash
# install babel and preset
npm install --save-dev babel-cli babel-preset-env   
```



```bash
# create a babelrc file
touch .babelrc
```



use preset

```json
// .babelrc
{
  "presets": ["env"]
}
```



Setup node app

```bash
# replace all of node
node ...

# to 
node -r babel-regeister ...
```



```json
// package.json
...
"scripts": {
    "start": "node -r babel-register ./bin/www",
    "test": "mocha --require babel-register",
    "build": "babel src -d lib"
  },
...
```



#### Other things

I realize that babel dependency have to be in "dependencies" block for heroku to use. Otherwise my code cannot be build because Heroku's node environment is not dev/debug.