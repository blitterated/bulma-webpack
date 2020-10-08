# Why no @import "~bulma/bulma"?

## the rules

* css

        ```javascript
        {
            test: /\.css$/,
            use: [
                MiniCssExtractPlugin.loader,
                'css-loader',
            ]
        },
        ```

* sass/scss

        {
            test: /\.s[ac]ss$/,
            use: [
                MiniCssExtractPlugin.loader,
                {
                    loader: 'css-loader'
                },
                {
                    loader: 'sass-loader',
                    options: {
                        sourceMap: true,
                    }
                }
            ]
        }


## with just the sass rule and `@import "~bulma/bulma";` or "~bulma/bulma.sass";`

    ```
    ERROR in ./src/mystyles.css 1:0
    Module parse failed: Unexpected character '@' (1:0)
    You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
    > @charset "utf-8";
    | @import "~bulma/bulma";
     @ ./src/index.js 1:0-25
    ```

    ```
    ERROR in ./src/mystyles.css 1:0
    Module parse failed: Unexpected character '@' (1:0)
    You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
    > @charset "utf-8";
    | @import "~bulma/bulma.sass";
     @ ./src/index.js 1:0-25
    ```

It's not even trying the `mystyles.css` file as css, which makes sense since I disabled the css rule. Duh.

The sass rule starts with sass-loader, not css-loader
    
    
## with css and sass rules and `@import "~bulma/bulma";`:

    ```
    Error: Can't resolve '~bulma/bulma' in '/Users/peteyoung/src/ipb/mmblog/bulma-webpack/src'
    ```
    
This is the css rule trying to find a literal `bulma/bulma` file in the project root b/c a literal  `bulma/bulma` doesn't exist in `node_modules`


## with css and sass rules and `@import "~bulma/bulma.sass";`:

    ```
    Error: Can't resolve '~bulma/bulma.sass' in '/Users/peteyoung/src/ipb/mmblog/bulma-webpack/src'
    ```
    
This is the css rule trying to find a literal `bulma/bulma` file in the project root b/c a literal  `bulma/bulma` doesn't exist in `node_modules`


## with css and sass rules and `@import "../node_modules/bulma/bulma";`:

    ```
    Error: Can't resolve '../node_modules/bulma/bulma' in '/Users/peteyoung/src/ipb/mmblog/bulma-webpack/src'
    ```
    

## with css and sass rules and `@import "../node_modules/bulma/bulma.sass";`:

    ```
    Error: Can't resolve '../node_modules/bulma/bulma.sass' in '/Users/peteyoung/src/ipb/mmblog/bulma-webpack/src'
    ```
    
Hmm,

    ```
    > ls -l /Users/peteyoung/src/ipb/mmblog/bulma-webpack/src/../node_modules/bulma/bulma.sass
    -rw-r--r--  1 peteyoung  staff  300 Oct 26  1985 /Users/peteyoung/src/ipb/mmblog/bulma-webpack/src/../node_modules/bulma/bulma.sass
    ```


## with css and sass rules and `@import "/Users/peteyoung/src/ipb/mmblog/bulma-webpack/node_modules/bulma/bulma.sass";`:


    ```
    Error: Can't resolve '/Users/peteyoung/src/ipb/mmblog/bulma-webpack/node_modules/bulma/bulma.sass' in '/Users/peteyoung/src/ipb/mmblog/bulma-webpack/src'
    ```

WTAF?

Same error with a simpler rule


    ```javascript
    {
        test: /\.s[ac]ss$/,
        use: ['style-loader', 'css-loader', 'sass-loader']
    },
    ```

Upon closer inspection of the stack trace, it's css-loader, not sass-loader screwing up the @import


## Googled "css-loader not resolving sass file in node_modules"]

Saw two examples of css and s[ac]ss being processed by the same rule


Dammit. css, sass, and scss need to be processed by the same rule.


I thought I'd be tricky and instead of following directions and using `mystyles.scss`, I used `mystyles.css`.
Well, I did want to learn how css and sass played together in webpack. I learned!



# References

* The article that started all this
    * [Use Bulma with webpack](https://bulma.io/documentation/customize/with-webpack/)
* Digging into webpack, sass, css, resolving, @import at-rules, etc.
    * [Resolving import at-rules (sass-loader docs)](https://github.com/webpack-contrib/sass-loader#resolving-import-at-rules)
    * [webpack alias with tilde not working for imports](https://github.com/webpack-contrib/sass-loader/issues/566)
    * [Custom import example](https://stackoverflow.com/a/37118406)
    * [webpack resolve.extensions](https://webpack.js.org/configuration/resolve/#resolveextensions)
    * [webpack sass-loader](https://webpack.js.org/loaders/sass-loader/)
* After noticing that is was css-loader failing:
    * [Google "css-loader not resolving sass file in node_modules"](https://www.google.com/search?client=firefox-b-1-d&q=css-loader+not+resolving+sass+file+in+node_modules)
    * [Unable to load node_modules lib with import](https://github.com/yibn2008/fast-sass-loader/issues/40)
    * [Loading css, css-modules, and Sass with webpack](https://adamrackis.dev/css-modules/)

