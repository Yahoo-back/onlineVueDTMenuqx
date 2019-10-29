<template>
  <div id="app" class="app-main">
    <!-- <transition name="fade" mode="out-in"> -->
			<router-view v-if="isRouterAlive"></router-view>
		<!-- </transition> -->
  </div>
</template>

<script>
export default {
  name: 'app',
  provide() {
    return {
      reload: this.reload
    }
  },
  data(){
    return {
      isRouterAlive: true
    }
  },
  methods: {
    reload() {
      this.isRouterAlive = false;
      // setTimeout(() => {
      //   this.isRouterAlive = true
      // }, 200);
      this.$nextTick(function() {
        this.isRouterAlive = true
      })
    }
  },
}
</script>

<style lang="less">
  @import "./styles/main.less";
</style>



<!--<template>
  <div id="app" class="app-main">
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>

<style lang="less">
  @import "./styles/main.less";
</style>
