# Setting up a React, webpack, and Babel project from scratch
This boilerplate covers the following points: 
- how to install and configure webpack
- how to install and configure Babel
- how to install React
- how to create two React components with the Container / Presentational principle
- how to include the resulting bundle into an HTML page
- how to install and configure webpack dev server

Thank you Valentin. https://www.valentinog.com/blog/babel/


1. Create project directory
`mkdir webpack-react-tutorial && cd $_`

2. Create directory for holding the code
`mkdir -p src`

3. Initialize project
`npm init -y`

4. Install webpack & webpack-cli
`npm i webpack --save-dev`
`npm i webpack-cli --save-dev`

5. Add webpack command inside package.json
```json
"scripts": {
  "build": "webpack --mode production"
}
```

6. Install babel & dependencies
`npm i @babel/core babel-loader @babel/preset-env @babel/preset-react --save-dev`

7. Configure babel by creating `.babelrc` file in root folder and include the following preset:
```json 
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

8. Define minimal webpack config in new file `webpack.config.js`
```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      }
    ]
  }
};
```

9. Install React
`npm i react react-dom`

10. Create minimal directory structure
`mkdir -p src/components/{container,presentational}`

11. Create a container component that has its own state and renders an HTML form
`touch src/components/container/FormContainer.jsx`
```javascript
import React, { Component } from "react";
import ReactDOM from "react-dom";
class FormContainer extends Component {
  constructor() {
    super();
    this.state = {
      title: ""
    };
  }
  render() {
    return (
      <form id="article-form">
      </form>
    );
  }
}
export default FormContainer;
```

12. Create a new presentational component that we will wrap our FormCountainer around
`touch src/components/presentational/Input.jsx`

13. It's a good idea to document React components with Prop Types
`npm i prop-types --save-dev`

14. Our Input will look like this:
```javascript
import React from "react";
import PropTypes from "prop-types";
const Input = ({ label, text, type, id, value, handleChange }) => (
  <div className="form-group">
    <label htmlFor={label}>{text}</label>
    <input
      type={type}
      className="form-control"
      id={id}
      value={value}
      onChange={handleChange}
      required
    />
  </div>
);
Input.propTypes = {
  label: PropTypes.string.isRequired,
  text: PropTypes.string.isRequired,
  type: PropTypes.string.isRequired,
  id: PropTypes.string.isRequired,
  value: PropTypes.string.isRequired,
  handleChange: PropTypes.func.isRequired
};
export default Input;
```

15. Update FormContainer to include Input & methods for updating state
```javascript
import React, { Component } from "react";
import ReactDOM from "react-dom";
import Input from "../presentational/Input.jsx";

class FormContainer extends Component {
  constructor() {
    super();
    this.state = {
      seo_title: ""
    };
    this.handleChange = this.handleChange.bind(this);
  }
  handleChange(event) {
    this.setState({ [event.target.id]: event.target.value });
  }
  render() {
    const { seo_title } = this.state;
    return (
      <form id="article-form">
        <Input
          text="SEO title"
          label="seo_title"
          type="text"
          id="seo_title"
          value={seo_title}
          handleChange={this.handleChange}
        />
      </form>
    );
  }
}
export default FormContainer;
```

16. webpack expects the entry point to be `src/index.js` so lets create it and import:
`import FormContainer from "./js/components/container/FormContainer.jsx";`
run `npm run build` and voila the bundle is built and placed in `./dist/app-bundle.js

17. Tell webpack to create a HTML page with our bundle in it
for this webpack needs two plugins:
`npm i html-webpack-plugin html-loader --save-dev`

18. Update webpack configuration to load HTML
```javascript
const HtmlWebPackPlugin = require("html-webpack-plugin");
module.exports = {
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader"
          }
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: "./src/index.html",
      filename: "./index.html"
    })
  ]
};
```

19. Next create the html file in `src/index.html`
```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta.2/css/bootstrap.min.css" >
    <title>How to set up React, Webpack, and Babel</title>
</head>
<body>
    <div class="container">
        <div class="row mt-5">
            <div class="col-md-4 offset-md-1">
                <p>Create a new article</p>
                <div id="create-article-form">
                    <!-- form -->
                </div>
            </div>
        </div>
    </div>
</body>
</html>
```

20. Open `./src/components/container/FormContainer.jsx ` and render the component to the `id="create-article-form"` in out HTML
```javascript
const wrapper = document.getElementById("create-article-form");
wrapper ? ReactDOM.render(<FormContainer />, wrapper) : false;
```

21. Setting up webpack dev server
install `npm i webpack-dev-server --save-dev`
then create start script
```javascript
"scripts": {
  "start": "webpack-dev-server --open --mode development",
  "build": "webpack --mode production"
}
```
run `npm start`