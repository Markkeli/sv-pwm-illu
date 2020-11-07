<html>
<h1>Space vector pulse-width modulation illustration</h1>

![alt text](https://ovaskainen.fi/screenshot_sv_pwm_illu.png)
<p>
I created this application to help illustrate how space vector pulse-width modulation works on power electronic devices, and also to get to know to Vue.js. During my electrical engineering studies, it was a little difficult to visualize how all this works, and how the space vector theory helps in implementing digital control of a switch matrix. My hope is that this application helps someone else to understand space vector theory.
</p>
<p>
The interactive &alpha;&beta;-coordinate canvas should help in illustrating how the reference vector is projected to the switch vectors, and how that created a specific switch sequence. The reference three-phase voltages are updated in a graph interactively as you move the cursor in the coordinate canvas. All switch sequences are also drawn as AC-side voltage outputs.
</p>
<p>
Try it out! You can find a deployed version of the application here: https://ovaskainen.fi/sv-pwm-illu/
</p>
<h3>Created with Vue.js and HTML5.</h3>

</html>

# sv-pwm-illu

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
