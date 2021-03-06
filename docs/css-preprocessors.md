# CSS Preprocessors

```js
mix.sass('src', 'output', pluginOptions);
mix.less('src', 'output', pluginOptions);
mix.stylus('src', 'output', pluginOptions);
```

A single method call allows you to compile your Sass, Less, or Stylus files, while applying automatic CSS3 prefixing.

Though Webpack can inline all of your CSS directly into the bundled JavaScript, Laravel Mix automatically performs the necessary steps to extract it to your desired output path.

### Multiple Builds

Should you need to compile more than one root file, you may call `mix.sass()` (or any of the preprocessor variants) as many as times as is needed. For each call, Webpack will output a new file with the relevant contents.

```js
mix.sass('src/app.scss', 'dist/') // creates 'dist/app.css'
   .sass('src/forum.scss', 'dist/'); // creates 'dist/forum.css'
```

### Example

Let's review a quick example:

**webpack.mix.js**

```js
let mix = require('laravel-mix');

mix.sass('resources/assets/sass/app.sass', 'public/css');
```

**./resources/assets/sass/app.sass**

```sass
$primary: grey

.app
    background: $primary
```

> Tip: For Sass compilation, you may freely use the `.sass` and `.scss` syntax styles.

Compile this down as usual \(`npm run webpack`\), and you'll find a `./public/css/app.css` file that contains:

```css
.app {
  background: grey;
}
```

### Plugin Options

Behind the scenes, Laravel Mix of course defers to Node-Sass, Less, and Stylus to compile your Sass and Less files, respectively. From time to time, you may need to override the default options that we pass to them. You may provide these as the third argument to `mix.sass()`, `mix.less()`, and `mix.stylus()`.

- **Node-Sass Options:** https://github.com/sass/node-sass#options
- **Less Options:** https://github.com/webpack-contrib/less-loader#options

```js
mix.sass('src', 'destination', { outputStyle: 'nested' });
```

#### Stylus Plugins

If using Stylus, you may wish to install extra plugins, such as [Rupture](https://github.com/jescalan/rupture). No problem. Simply install the plugin in question through NPM (`npm install rupture`), and then require it in your `mix.stylus()` call, like so:

```js
mix.stylus('resources/assets/stylus/app.styl', 'public/css', {
    use: [
        require('rupture')()
    ]
});
```

Should you wish to take it further, and automatically import plugins globally, you may use the `import` option. Here's an example:

```js
mix.stylus('resources/assets/stylus/app.styl', 'public/css', {
    use: [
        require('rupture')(),
        require('nib')(),
        require('jeet')()
    ],
    import: [
        '~nib/index.styl',
        '~jeet/jeet.styl'
    ]
});
```

That's all there is to it!


### CSS `url()` Rewriting

One key Webpack concept to understand is that it will rewrite any `url()`s within your stylesheets. While this might initially sound strange, it's an incredibly powerful piece of functionality.

#### An Example

Imagine that we want to compile a bit of Sass that includes a relative url to an image.

```scss
.example {
    background: url('../images/thing.png');
}
```

> **Tip:** Absolute paths for `url()`s will of course be excluded from url-rewriting. As such, `url('/images/thing.png')` or `url('http://example.com/images/thing.png')` won't be touched.

Notice that relative URL? By default, Laravel Mix and Webpack will find `thing.png`, copy it to your `public/images` folder, and then rewrite the `url()` within your generated stylesheet. As such, your compiled CSS will be:

```css
.example {
  background: url(/images/thing.png?d41d8cd98f00b204e9800998ecf8427e);
}
```

This, again, is a very cool feature of Webpack's. However, it does have a tendency to confuse those who don't understand how Webpack and the css-loader plugin works. It's possible that your folder structure is already just how you want it, and you'd prefer that Mix not modify those `url()`s. If that's the case, we do offer an override:

```js
mix.sass('src/app.scss', 'dist/')
   .options({
      processCssUrls: false
   });
```

With this addition to your `webpack.mix.js` file, we will no longer match `url()`s or copy assets to your public directory. As such, the compiled CSS will remain exactly as you typed it:

```css
.example {
  background: url("../images/thing.png");
}
```
