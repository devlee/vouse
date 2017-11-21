<template>
  <div id="app">
    <header class="header">
      <span class="projectName">vouse</span>
      <router-link to="home" class="link">Home</router-link>
      <router-link to="about" class="link">About</router-link>
    </header>
    <main>
      <router-view></router-view>
    </main>
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>

<style>
body {
  margin: 0;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #222;
}

.header {
  height: 50px;
  background-color: #0078d7;
  color: #fff;
}

.projectName {
  font-size: 24px;
  padding: 7px 17px;
  display: inline-block;
  height: 48px;
  margin: 1px;
  box-sizing: border-box;
  vertical-align: top;
}

.link {
  color: #fff;
  padding: 0 26px;
  font-size: 15px;
  display: inline-block;
  height: 48px;
  margin: 1px;
  box-sizing: border-box;
  vertical-align: top;
  line-height: 48px;
}
</style>
