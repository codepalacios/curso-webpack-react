# curso-webpack-react

## Instalación y configuración de React

Descargar y clonar el repositorio de GitHub 

```bash
git clone https://github.com/platzi/curso-webpack-react.git
```

Ingresar a la carpeta del proyecto
```bash
cd curso-webpack-read
```

Inicializar npm

```bash
npm init -y
```

Instalar dependencias de React
```bash
npm install react react-dom -S
```

Abrir VS Code
```bash
code .
```

Crear en el proyecto la carpeta y archivo **src/index.js**
```javascript
import React from "react";
import ReactDOM from "react-dom";
import App from "./components/App.jsx";

ReactDOM.render(<App />, document.getElementById('app'));
```

Crear la carpeta y archivo **src/components/App.jsx**
```javascript
import React from "react";

const App = () => <h1>Hello React!</h1>;

export default App;
```

Por último crear la carpeta y archivo **public/index.html**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app"></div>
</body>
</html>
```

---

## Configuración de Webpack 5 para React.js

Instalar dependencias de Babel

```bash
npm install @babel/core @babel/preset-env @babel/preset-react babel-loader -D
```

Crear el archivo **.babelrc**

```javascript
{
  "presets": [
    "@babel/preset-env",
    "@babel/preset-react"
  ]
}
```

Instalar dependencias de Webpack

```bash
npm install webpack webpack-cli webpack-dev-server -D
```

Crear el archivo **webpack.config.js**

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bundle.js",
  },
  resolve: {
    extensions: ["js", "jsx"]
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
        }
      }
    ]
  },
  devServer: {
    static: path.resolve(__dirname, "dist"),
    compress: true,
    port: 3006,
    open: true
  }
};
```

---

## Configuración de plugins y loaders para React

Instalación de dependencias

```bash
npm install html-loader html-webpack-plugin -D
```

Agregar la configuración en el archivo **webpack.config.js**

```javascript
...
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  ...
  mode: "development",
  module: {
    rules: [
      ...
      {
        test: /\.html$/,
        use: [
          { loader: "html-loader" }
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./public/index.html",
      filename: "./index.html"
    })
  ],
  ...
};
```

Creación de scripts en archivo **package.json**

```javascript
{
  ...
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack serve",
    "build": "webpack --mode production"
  },
  ...
}
```

Finalmente probar iniciando el servidor local y realizar un cambio para observar si actualiza el estado del proyecto

---

## Configuración de Webpack para CSS en React

Instalación de dependencias

```bash 
npm install mini-css-extract-plugin css-loader style-loader sass sass-loader -D
```

Agregar la configuración en el archivo **webpack.config.js**

```javascript
...
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  ...
  module: {
    rules: [
      ...
      {
        test: /\.s[ac]ss$/,
        use: [
          "style-loader",
          "css-loader",
          "sass-loader"
        ]
      }
    ]
  },
  plugins: [
    ...
    new MiniCssExtractPlugin({
      filename: "[name].css"
    })
  ],
  ...
};
```

Crear el archivo **src/styles/global.scss**

```css
$base-color: #c6538c;
$color: rgba(black, 0.88);

body {
    background-color: $base-color;
    color: $color;
}
```

Importar los estilos en archivo **src/index.js**

```javascript
import "./styles/global.scss";
```

Finalmente probar si todo funciona correctamente iniciando el servidor local

---

## Optimización de Webpack para React

Instalación de dependencias 

```bash
npm install css-minimizer-webpack-plugin terser-webpack-plugin clear-webpack-plugin -D
```

Crear el archivo **webpack.config.dev.js** 

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bundle.js",
  },
  resolve: {
    extensions: [".js", ".jsx"]
  },
  mode: "development",
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
        }
      },
      {
        test: /\.html$/,
        use: [
          { loader: "html-loader" }
        ]
      },
      {
        test: /\.s[ac]ss$/,
        use: [
          "style-loader",
          "css-loader",
          "sass-loader"
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./public/index.html",
      filename: "./index.html"
    }),
    new MiniCssExtractPlugin({
      filename: "[name].css"
    })
  ],
  devServer: {
    static: path.resolve(__dirname, "dist"),
    compress: true,
    port: 3006,
    open: true
  }
};
```

Editar/agregar la configuración en el archivo **webpack.config.js**

```javascript
...
const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
const TerserPlugin = require("terser-webpack-plugin");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");

module.exports = {
  ...
  output: {
    ...
    publicPath: "/",
  },
  resolve: {
    ...
    alias: {
      "@components": path.resolve(__dirname, "src/components/"),
      "styles": path.resolve(__dirname, "src/styles/")
    }
  },
  mode: "production",
  ...
  plugins: [
    ...
    new CleanWebpackPlugin(),
  ],
  optimization: {
    minimize: true,
    minimizer: [
      new CssMinimizerPlugin(),
      new TerserPlugin(),
    ]
  }
};
```

Crear/editar scripts en archivo **package.json**

```javascript
{
  ...
  "scripts": {
    ...
    "start": "webpack serve --config webpack.config.dev.js",
    "build": "webpack --config webpack.config.js"
  },
  ...
}
```
