# vue-canvas-loading
## Use
```javascript
<template>
  <div id="app">
    <loading></loading>
  </div>
</template>

<script>
import Loading from './components/Loading.vue'

export default {
  name: 'app',
  components: {
    Loading
  }
}
</script>
```
## Change the color
You can change the color by pass parameters to the component

```javascript
props: {
      circleColor: {  //The circular arc
        type: Object,
        default: () =>{
          return {
            start: `rgba(137, 190, 178, .25)`,
            end: `rgba(69, 137, 148, 0)`
          }
        }
      },
      tailBorderColor: {  //The border of the arc
        type: Object,
        default: () => {
          return {
            start: `rgba(69, 137, 148, 0)`,
            middle: 'rgba(137, 190, 178, .7)',
            end: `rgba(137, 190, 178, 0)`
          }
        }
      },
      circleFlareColor: {   //The outer light circle of the head of an arc
        type: Object,
        default: () => {
          return {
            start: `rgba(254, 67, 101, .35)`,
            end: `rgba(252, 157, 154, 0)`
          }
        }
      },
      circleFlareInColor: {  //The inner circle of the head of an arc
        type: Object,
        default: () => {
          return {
            start: `rgba(130, 57, 53, .6)`,
            end: `rgba(252, 157, 154, 0)`
          }
        }
      }
    },
```
