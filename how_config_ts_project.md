# Как сконфигурировать typescript проект с использование Webpack, Eslint (с правилами Airbnb-base), Prettier
Создаем папку проекта, к примеру ```async-race```
Внутри папки создаем папку ```src```
Внутри папки ```src``` создаем файлы ```index.ts```, ```index.html```, ```style.css``` (index.html содержит минимальный шаблон HTML-документа !+tab для vs code)

## Начинаем конфигурацию
Создадим автоматически стандартный приватный файл "package.json"
```
npm init -y  
```
Устанавливаем webpack и webpack-cli в качестве зависимостей для разработки:
webpack — сборщик модулей и ресурсов
webpack-cli — интерфейс командной строки для вебпака
```
npm i -D webpack webpack-cli
```
### Базовая настройка
Приступим к настройке сборщика. Создаем файл ```webpack.config.js``` в корневой директории проекта
#### Точка входа
Прежде всего, необходимо определить точку входа приложения, т.е. то, какие файлы вебпак будет компилировать. В приведенном примере мы определяем точку входа как ```src/index.ts```:
```
const path = require("path");

module.exports = {
  entry: {
    main: path.resolve(__dirname, "./src/index.ts"),
  },
};
```
#### Точка выхода
Точка выхода — это директория, в которую помещаются скомпилированные вебпаком файлы. Установим точку выхода как ```dist```:
```
output: {
    path: path.resolve(__dirname, "dist"),
  },
```
Минимальная настройка для сборки проекта готова. Добавляем скрипт ```build``` в файл ```package.json```, запускающий команду «webpack»:
```
// package.json
"scripts": {
    "build": "webpack --mode=production --node-env=production"
}
```
Запускаем вебпак:
```
npm run build
```
Состояние файлов на этом этапе ```package.json```:
```
{
  "name": "async-race",
  "version": "1.0.0",
  "description": "",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack --mode=production --node-env=production"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^5.91.0",
    "webpack-cli": "^5.1.4"
  }
}
```
```webpack.config.js```
```
const path = require("path");

module.exports = {
  entry: path.resolve(__dirname, "src/index.ts"),
  output: {
    path: path.resolve(__dirname, "dist"),
  },
};
```
## Плагины
#### Плагин создания HTML
html-webpack-plugin — создает HTML-файл на основе шаблона https://github.com/jantimon/html-webpack-plugin
```
npm i --save-dev html-webpack-plugin
```
Добавляем в настройки вебпака свойство ```plugins```, где определяем плагин, название выходного файла (index.html) и шаблон:
```
// webpack.config.js
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    // ...
    plugins: [
        new HtmlWebpackPlugin({
            template: path.resolve(__dirname, "src/index.html"),
      	    filename: "index.html",
        }),
    ],
}
```
#### Дополнение
Чтобы добавить фавикон для страницы, необходимо положить ```favicon.ico``` рядом с ```index.html``` и в настройках вебпака HtmlWebpackPlugin добавить следующее:
```
favicon: path.join(__dirname, 'src', 'favicon.ico'),
```
т.е.:
```
 plugins: [
        new HtmlWebpackPlugin({
            template: path.resolve(__dirname, "src/index.html"),
      	    filename: "index.html",
      	    favicon: path.join(__dirname, 'src', 'favicon.ico'),
        }),
    ],
```
Запускаем сборку, убеждаемся, что нет ошибок (напоминаю ```npm run build```)

#### Установка и настройка DevServer
```
npm i -D webpack-dev-server
```
Откроем файл webpack.config.js и добавим настройки для веб-сервера:
```
devServer: {
    open: true,
    host: "localhost",
  },
```
В package.json к скрпитам добавляем команду start
```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack --mode=production --node-env=production",
    "start": "webpack serve --open --mode=development"
  },
```
serve означает запустить веб сервер.
--open автоматически запускает браузер, который в системе установлен по умолчанию.
--mode development включает режим разработки
Переходим в терминал и и запускаем команду ```npm run start```, убеждаемся, что в браузере открылся файл. (Команды в скриптах можно называть по своему усмотрению)

#### Очистка
Установим clean-webpack-plugin, очищающий директорию ```dist``` при каждой сборке проекта. Это позволяет автоматически удалять старые файлы, ставшие ненужными. https://github.com/johnagan/clean-webpack-plugin
```
npm install --save-dev clean-webpack-plugin
```
Откроем файл ```webpack.config.js``` и добавим настройки:
```
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
```
```
plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, "src/index.html"),
      filename: "index.html",
    }),
+   new CleanWebpackPlugin(),
  ],
```
#### Изображения
Откроем файл ```index.ts``` и добавим импорт картинки для проверки:
```
import "./components/view/img/Dino.svg";
```
Для возможности импорта изображений добавляем настройки в файле ```webpack.config.js```:
```
module: {
         rules: [
	   {
             test: /\.(jpg|png|svg|jpeg|gif)$/,
             type: 'asset/resource',
           },
         ],
        },
```
Если сразу добавить импорт какой нибудь картинки и попробовать запустить сервер, то будет ошибка "Module not found: Error: Can't resolve..."
##### Устанавливаем плагин для копирования файлов
https://webpack.js.org/plugins/copy-webpack-plugin/ 
```
npm install copy-webpack-plugin --save-dev
```
Откроем файл ```webpack.config.js``` и добавим настройки:
```
const CopyPlugin = require("copy-webpack-plugin");
```
```
new CopyPlugin({
      patterns: [
        {
          from: path.resolve(__dirname, "src/components/view/img"),	//путь к папке, где лежат картинки
          to: path.resolve(__dirname, "dist/img"),			//куда будут копированы
        },
      ],
    }),
```
```
resolve: {
    alias: {
      img: path.join(__dirname, "src", "components", "view", "img"),
    },
  },
```
Теперь поднимаем сервер и убеждаемся, что нет ошибок.

#### Стили
В зависимости от того, какие стили будут использоваться, такие плагины и подключать. Для CSS:
https://webpack.js.org/loaders/css-loader/
https://webpack.js.org/loaders/style-loader/
```
npm install --save-dev style-loader
```
```
npm install --save-dev css-loader
```
Откроем файл ```webpack.config.js``` и добавим настройки:
```
 rules: [
      {
        test: /\.css$/i,
        use: ["style-loader", "css-loader"],
      },
      ...
```
Добавляем импорт в index.ts, в самом файле  ```style.css``` можно добавить стили для проверки:
```
import "./style.css";
```
Запускаем, проверяем, что все работает.

### Typescript
Добавляем пакет typescript
```
npm i -D typescript
```
Для работы webpack с ts-файлами устанавливаем лоадер:
```
npm i -D ts-loader
```
Откроем файл ```webpack.config.js``` и добавим настройки:
```
rules: [
      ...
      {
        test: /\.ts$/i,
        use: "ts-loader",
      },
    ],
```
Создаем файл ```tsconfig.json```. Можно автоматически, можно ручками и самим добавить самое необходимое
```
npx tsc --init
```
Внутри файла tsconfig.json
Выключить "module": "commonjs" (т.к. сборкой будет заниматься Webpack)
Включить "strict": true (требование таски)
Включить "noImplicitAny": true (требование таски)
#### Парсер TS для ESlint 
Устанавливаем парсер TS для ESlint - чтобы ESlint умел правильно парсить .ts файлы
```
npm i -D @typescript-eslint/parser
```
#### Плагин TS для ESlint
Плагин TS для ESlint - правила, по которым ESlint будет проверять .ts файлы
```
npm i -D @typescript-eslint/eslint-plugin 
```
#### Плагин ESlint - для работы ESlint вместе с Webpack
```
npm i -D eslint-webpack-plugin
```
Импортируем плагин ESlint (чтобы он работал во время работы Webpack)
```
const EslintPlugin = require('eslint-webpack-plugin');
```
Добавляем экземпляр плагина ESlint в массив "plugins" (в его настройках указываем тип файлов .ts)
```
new EslintPlugin({ extensions: ['ts'] }),
```
Добавляем в массив "resolve.extensions" расширение .ts-файлов (это определяет порядок поиска файлов в импортах)
```
extensions: ['.ts', '.js']
```
#### Eslint
установим ESLint в качестве зависимости для разработчиков
```
npm i -D eslint
```
Инициализируем eslint, отвечая на вопросы в терминале
```
npx eslint --init
```
Уточнение: в интернете пишут, что отвечая на вопросы, можно выбрать использование популярных правил Airbnb, но у меня этого не было, поэтому действуем далее
#### Airbnb
Устанавливаем необходимые для задания плагины airbnb, airbnb-base
```
npm install eslint-config-airbnb-typescript --save-dev
```
```
npm install --save-dev eslint-config-airbnb-base // По сути без реакта, нам нужно только base
```
Открываем файл ```.eslintrc.js``` (лучше сперва поменять на ```.json```, тогда странная ошибка пропадет) и добавляем следующее:
```
extends: [
  "eslint-config-airbnb-base",
   "airbnb-typescript/base",
]
```
```
parserOptions: {
   project: './tsconfig.json'
 }
```
Если хотим использовать вывод в консоль (у меня была эта первая базовая проверка работоспособности .ts файлов), то добавляем:
```
rules: {
    "no-console": 0,
  },
```
Открываем файл ```tsconfig.json``` и добавляем следующее:
```
"include": ["src/**/*", ".eslintrc.js"]
```
#### Плагин import/export
Устанавливаем плагин, который отвечает за import/export синтаксис
```
npm install eslint-plugin-import --save-dev
```
#### Prettier
Устанавливаем prettier и плагины для совместной работы с eslint
```
npm i prettier --save-dev --save-exact
```
```
npm i eslint-config-prettier --save-dev
```
Проверяем, чтобы ```eslintrc.js``` extends, plugins содержали:
```
extends: [
    "eslint-config-airbnb-base",
    "airbnb-typescript/base",
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:prettier/recommended",
    "prettier"
  ],
```
```
plugins: ["prettier", "import", "@typescript-eslint"],
```
Открываем файл ```package.json```, добавляем скрипты:
```
"format": "npx prettier --write .",
"ci:format": "npx prettier --check .",
"lint": "npx eslint src/**/*.ts",
```
Запускаем сервер ```npm run start```, убеждаемся, что все работает.
