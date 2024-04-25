<template>
  <nav class="bg-gray-800 py-4 px-4">
    <div class="container mx-auto flex justify-between items-center">
      <!-- Logo -->
      <p href="#" class="text-white text-xl font-bold">3D Reconstruction</p>
      <!-- Menu -->
      <ul class="flex space-x-4">
        <li><router-link to="/">Home</router-link> |</li>
        <li></li>
        <router-link to="/Result">Result</router-link>
      </ul>
    </div>
  </nav>
  <router-view />
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
