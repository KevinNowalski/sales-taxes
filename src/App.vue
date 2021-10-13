<template>
  <div id="nav">
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link> |
    <router-link to="/basket">Basket</router-link>
  </div>
  <router-view/>
</template>

<style type="text/css">
  @import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@300;400;600;700&display=swap');
  @import './assets/global.css';
</style>