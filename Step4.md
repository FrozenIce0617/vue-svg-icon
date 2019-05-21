# The finish line!

Awesome, I’m satisfied. I have a fairly clean Vue component for using inline SVGs that’s easily colorable and automatically scales to its context - what more could a guy ask for.

I think I’m finally ready to say farewell to that Font-awesome ```@import``` and usher in the new era of doing icons Right™️.

<b>Usage</b>

```
<h1 class="text-green">  
    <svg-icon icon="announcement"></svg-icon> Some Announcement!
</h1>
```

<b>SvgIcon.vue</b>

```
<template>  
    <div class="inline-block" v-html="require('icon-' + this.icon + '.svg')"></div>
</template>

<style module>  
.svg {
    fill: currentColor;
    height: 1em;
    margin-top: -4px;
    vertical-align: middle;
    width: 1em;
}
</style>

<script>  
export default {  
    props: ['icon'],

    mounted() {
        this.$el.firstChild.classList.add(this.$style.svg)
        this.$el.firstChild.removeAttribute('height')
        this.$el.firstChild.removeAttribute('width')
    }
}
</script>
```

I thought I’d try something new,and I think you can learn a lot more sometimes seeing the process than just seeing the end result.

Thanks for tuning in, happy SVGing!
