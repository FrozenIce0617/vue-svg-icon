# Quick webpack trick I learned along the way:

Before I roll up my sleeves and attempt to configure web pack to use this loader (which tires me out just thinking about it), I discover a cool nifty way to use webpack loaders without messing with any configuration, saweet!

<b>The tiring way:</b>

```
// webpack.mix.js
mix.webpackConfig({  
  module: {
    rules: [{
      test: /\.svg$/,
      use: [{ loader: 'html-loader' }]
    }]
  }
});

// Some .vue file
require('./../../svg/icon-trash.svg');
```

<b>The cool new quick way</b>

```
// Some .vue file
require('html-loader!./../../svg/icon-trash.svg');
```

# Back to work

By now, I’m excited and ready to use my handy new html-loader to pull SVGs into my Vue component.

I add the new syntax to the require statement:

```
// SvgIcon.vue
<template>  
   <div v-html="require('html-loader!./../../svg/icon-trash.svg')"></div>
</template>
```

...and much to my dismay, this is the rendered output I receive now:

```
<div class="text-grey">module.exports = "/images/icon-trash.svg?869001c277c880324f6ebcfb92ab5d71";</div>  
```

Note the new module.exports = string in the output. This tells me the loader worked, but Laravel Mix did it’s transformation before html-loader got to it.

After a good amount of source diving, I come up for air and start searching the Laravel Mix GitHub repository to see if other people have encountered this problem. Luckily they have! But the solution is pretty hairy. I decide to just go with it for the sake of time. Also, I’m no stranger to a messy webpack.mix.js file.

I add the following code to my webpack.mix.js file. This tells Laravel Mix to not touch .svgs and let  html-loader handle everything.

```
// Make Laravel Mix ignore .svgs
Mix.listen('configReady', function (config) {  
  const rules = config.module.rules;
  const targetRegex = /(\.(png|jpe?g|gif)$|^((?!font).)*\.svg$)/;

  for (let rule of rules) {
    if (rule.test.toString() == targetRegex.toString()) {
      rule.exclude = /\.svg$/;
      break;
    }
  }
});

// Hande .svgs with html-loader instead
mix.webpackConfig({  
  module: {
    rules: [{
      test: /\.svg$/,
      use: [{
        loader: 'html-loader',
        options: {
          minimize: true
        }
      }]
    }]
  }
});
```

# Did we fix everything?

After completely wrecking my webpack.mix.js file, I’m eager to test this baby out and see if it did the trick. (A small part of me has that bad feeling that comes with anything I ever do in webpack - the feeling of knowing it will definitely not work and I will lose at least a half day of work to debugging the darn tootin’ thing - only to revert my attempt entirely and quit programming)

Despite my gut feeling, it worked!

<b>My Vue component:</b>

```
// SvgIcon.vue
<template>  
   <div v-html="require(`./../../svg/icon-${icon}.svg`)"></div>
</template>
```

<b>Rendered output:</b>

```
<div><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="1em" height="1em" fill="rgb(184, 194, 204)" style="vertical-align: middle; display: inline-block; margin-top: -4px;"><path class="heroicon-ui" d="M8 6V4c0-1.1.9-2 2-2h4a2 2 0 0 1 2 2v2h5a1 1 0 0 1 0 2h-1v12a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V8H3a1 1 0 1 1 0-2h5zM6 8v12h12V8H6zm8-2V4h-4v2h4zm-4 4a1 1 0 0 1 1 1v6a1 1 0 0 1-2 0v-6a1 1 0 0 1 1-1zm4 0a1 1 0 0 1 1 1v6a1 1 0 0 1-2 0v-6a1 1 0 0 1 1-1z"></path></svg></div>  
```

# Finishing the component

The hard part’s over, now it’s just a matter of slapping on a prop to pass in the icon name and calling it a day.

<b>SvgIcon.vue</b>

```
<template>  
    <div v-html="require(`./../../svg/icon-${icon}.svg`)"></div>
</template>

<script>  
export default {  
    props: ['icon']
}
</script>
```

<b>Usage</b>

```
<svg-icon icon="trash"></svg-icon>  
```

Now I have a handy Vue component to use inline SVGs effortlessly in my single file components.

For some, this may be enough. If that’s the case, you can stop reading now and go implement this for yourself!

For the rest of us, we need to color and size these babies. (out of the box, the SVGs are a fixed size and color)
