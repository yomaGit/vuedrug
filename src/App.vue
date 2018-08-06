<template>
  <div class="body">

    <router-view ref="routerview"/>
  </div>
</template>

<script>
export default {
  name: 'App',
  data(){
    return {

    }
  },
  methods:{//- 方法

  },
  computed: {

  },
  watch:{//- 监听

  },
  created(){//- 创建 常用

  },
  updated(){//- 组件更新之后

  },
  destroyed(){//- 销毁

  },
  beforeRouteEnter(to, from, next){//- 进入路由，无this

    next(true);
  },
  beforeRouteUpdate(to, from, next){//- 路由改变，可this

    next(true);
  },
  beforeRouteLeave(to, from, next){//- 离开路由，可this

    next(true);
  }
}
</script>

<style lang="sass">
  @import "css/app.scss"
</style>
