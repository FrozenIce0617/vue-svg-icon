# Using inline SVGs in Vue components
###### 21 May 2018

From what I’ve heard, using img tags for SVGs is not as cool as inlining the HTML (SVG tag).

<b>Not cool</b>
```
<img src=“/some/image.svg”></img>
```

<b>Super cool</b>
```
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" …
```

Fortunately, Adam Wathan has a nifty little package that makes inlining these bad boys a breeze (Adam’s Blade SVG package). However, because it’s a blade plugin, I would have to do some finagling to use it in my single file Vue components (passing them through as props, or some other fanciness). Because most of the app is written using SFCs (Single File Components), I decide this package isn’t ideal and instead attempt to hand-roll my own Vue component.

I already have an idea in my head of how I want to use the component:

```
<svg-icon icon="trash"></svg-icon>  
```

… and I know the rendered output needs to look something like this:

```
<div>  
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path class="heroicon-ui" d="M8 6V4c0-1.1.9-2 2-2h4a2 2 0 0 1 2 2v2h5a1 1 0 0 1 0 2h-1v12a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V8H3a1 1 0 1 1 0-2h5zM6 8v12h12V8H6zm8-2V4h-4v2h4zm-4 4a1 1 0 0 1 1 1v6a1 1 0 0 1-2 0v-6a1 1 0 0 1 1-1zm4 0a1 1 0 0 1 1 1v6a1 1 0 0 1-2 0v-6a1 1 0 0 1 1-1z"/></svg>
</div>  
```
