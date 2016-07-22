<template>
  <div id="app">
    <h1>{{ msg }}</h1>
    <ul id="example-1">
      <li v-for="item in items">
      {{msg}} - {{$index}} - {{item.message}}
      </li>
    </ul>
    <div>
      <span v-for="n in 10">{{ n }} </span>
    </div>
    <input type="checkbox" id="checkbox" v-model="checked">
    <label for="checkbox">{{ checked }}</label>
    <input v-model="age" number>
    <span>{{age}}</span>
  </div>
  <div v-if="show" :transition="transitionName">hello</div>

  <button @click="changeShow">toggle</button>
  <leaflet message="chuan"></leaflet>
</template>

<script>
import leaflet from './leaflet.vue';
export default {
  data () {
    return {
      // note: changing this line won't causes changes
      // with hot-reload because the reloaded component
      // preserves its current state and we are modifying
      // its initial state.
      msg: 'Hello Vue!',
      items:[
      {message:'Foso'},
      {message:'Bar'}
      ],
      checked: true,
      age:8,
      show:false,
      transitionName: 'fade'
    }
  },
  methods:{
    changeShow:function(){
      this.show = !this.show;
    }
  },
  components: { leaflet }
}



</script>

<style>
body {
  font-family: Helvetica, sans-serif;
}
</style>
