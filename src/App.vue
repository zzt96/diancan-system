<template>
  <div id="app">
    <div class="container">
      <app-header></app-header>
    </div>
    <div class="container">
      <router-view></router-view>
      <router-view></router-view>
    </div>
    <div class="container">
      <div class="row">
        <div class="col-sm-12 col-md-4">
          <router-view name="orderingGuide"></router-view>
        </div>
        <div class="col-sm-12 col-md-4">
          <router-view name="Delivery"></router-view>
        </div>
        <div class="col-sm-12 col-md-4">
          <router-view name="History"></router-view>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import header from './components/Header.vue'

  export default {
    name: "App",
    components: {appHeader: header}
  }
</script>

<style>

</style>
