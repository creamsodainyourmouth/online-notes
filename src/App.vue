<template>
  <div id="app">

    <router-view/>
  </div>
</template>

<style>
*, *:before, *:after {
  padding: 0;
  margin: 0;
  border: 0;
  box-sizing: border-box;
}
body {
  background-color: #1b1e21;
}

input:focus, select:focus, textarea:focus {
  outline: none;
  background-color: inherit;
}

input, textarea {
  background-color: inherit;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 100vw;
  height: 100vh;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
