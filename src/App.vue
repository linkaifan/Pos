<template>
  <div id="app">
    <left-nav></left-nav>
    <div class="main">
      <router-view/>
    </div>
    
  </div>
</template>

<script>
import leftNav from '@/components/common/leftNav'
export default {
  name: 'app',
  components:{
    leftNav
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.main{
  width: 95vw;
  float: left;
  background-color: #EFF2F7;
  height:100%;
  overflow: auto;
}
</style>
