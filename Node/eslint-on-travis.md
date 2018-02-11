# Run ES Lint on Travis before build

```bash
# intall eslint locally
npm install --save-dev eslint

# install airbnb config
npm install --save-dev eslint-config-airbnb

```



Since I am not using React and JSX, I config my `.eslintrc.js` file as below

```js
const OFF = 0;
const WARN = 1;
const ERROR = 2;

module.exports = {
  'extends': 'airbnb/base', // <-- airbnb config without react and jsx

  "rules": {
    'max-len': [WARN, 120],
    'excludedFiles': [
        ...
    ]
  },
};

```



```bash
# this is not running local eslint
eslint ...

# this is 
./node_modules/.bin/eslint ...
```



So I add this script to my `package.json` file.

```json
"scripts": {
    "lint": "./node_modules/.bin/eslint ./app.js ./routes/ ./lib/",
    ...
  },
```



```bash
# in terminal, I just run 
npm run lint

# or because of the built-in alias of oh-my-zsh
npmR lint
```



It works!



Finally, I add this script to my `.travis.yml` file.

```yaml
# .travis.yml

script:
- npm run lint
- npm test

```

