# First step: importing SVGs into .vue files

I'm thinking I will end up using webpack's ```require()``` syntax, but before I jump in, I want to set myself up for proper importing. Relative url's ```(requre('./../../../ugh'))``` are visually repulsive to me and annoying to deal with later if you need to restructure your files

Instead of having to do this (from a Vue component in ```resources/assets/js/components```):

```require('./../../svg/icon-trash.svg')```

…I want to be able to do this:

```require('icon-trash.svg')```

…so I add this kinda-nasty kinda-not bit to my webpack.mix.js file:

```
mix.webpackConfig({  
  resolve: {
    modules: [
        'node_modules',
        path.resolve(__dirname, 'resources/assets/js'),
        path.resolve(__dirname, 'resources/assets/svg')
      ]
  }
});
```

Turns out this works fine when you write ```require(’icon-trash.svg')``` explicitly, but if you use an expression inside ```require()``` like: ```require(‘icon-‘ + icon + ‘.svg')``` everything blows up.
Webpack's "require expressions" only work when you are resolving directories (ex. ```require(‘./../../svg/icon-‘ + icon + ‘.svg')```). As much as I’d like to not use relative directories, this seems to be the only option for now.

Ok, where were we…

The following setup would be ideal, but I’m 99% positive this won’t do what I want it to do out of the box:

```
// SvgIcon.vue
<template>  
   <div v-html="require(`./../../svg/icon-${icon}.svg`)"></div>
</template>
```

…again, this is the desired output:

```
<div>  
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path class="heroicon-ui" d="M8 6V4c0-1.1.9-2 2-2h4a2 2 0 0 1 2 2v2h5a1 1 0 0 1 0 2h-1v12a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V8H3a1 1 0 1 1 0-2h5zM6 8v12h12V8H6zm8-2V4h-4v2h4zm-4 4a1 1 0 0 1 1 1v6a1 1 0 0 1-2 0v-6a1 1 0 0 1 1-1zm4 0a1 1 0 0 1 1 1v6a1 1 0 0 1-2 0v-6a1 1 0 0 1 1-1z"/></svg>
</div>
```

As expected, this does not work out of the box. When I compile and load in the browser, I get the following rendered output:

```
<div>/images/icon-trash.svg?cdd2695e96c36122af7cb47739a28cea</div>
```

So I visit that endpoint for kicks (```my-finance-app.boots/images/icon-trash.svg?cdd2695e96c36122af7cb47739a28cea```) and my browser actually displays the SVG! This is confusing to me because I put the original SVG in a folder called ```resources/assets/svg```.

Turns out, during the build, Laravel Mix actually created a folder called ```public/images``` and put ```icon-trash.svg``` inside it.

So clearly there is some magic being performed by Laravel Mix behind the scenes. Normally I would be delighted by this sorcery, but in this case, I want to import the raw text content of a file, not a public URL.

After a bit of googling, I discover a webpack loader called ```html-loader``` (html-loader GitHub repo) which seems to do exactly what I want: allow the importing of a file’s HTML content.

So I pull it into my project via ```npm install --save-dev html-loader.```
