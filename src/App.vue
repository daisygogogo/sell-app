<template>
  <div id="app">
  <v-header :seller="seller"></v-header>
  <div class="tab">
  <div class="tab-item"><router-link to="/goods">商品</router-link></div>
  <div class="tab-item"><router-link to="/ratings">评论</router-link></div>
  <div class="tab-item"><router-link to="/seller">商家</router-link></div>
  </div>
  <router-view :seller="seller"></router-view>
  </div>
</template>

<script type="text/ecmascript-6">
import Header from './components/header/header.vue'
import {urlParse} from './common/js/urlParse.js'


const ERR_OK = 0

export default {
  data() {
    return {
	  seller: {
      id: (() =>{   // id通过这个立即执行函数来获得，然后在后面请求的时候带上这个id
        let queryParam = urlParse();
        console.log(queryParam);
        return queryParam.id;
    })()
  }
	}
  },
  created() {
    this.$http.get('/api/seller?id='+this.seller.id).then((response) => {
	  response = response.body
	  if(response.errno === ERR_OK) {
	    this.seller = Object.assign({}, this.seller, response.data); // es6里面的方法assign，相当于扩展的方法
	  }
	})
  },
  name: 'App',
  components: {
    'v-header': Header
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import "./common/stylus/mixin.styl"


#app
 .tab
   display: flex
   width: 100%
   height: 40px
   line-height: 40px
   border-1px(rgba(7, 17, 27, 0.1))
   .tab-item
    flex:1
    text-align: center
	
.tab-item > a
	  display: block
	  text-decoration: none
	  font-size: 14px
	  color: rgb(77,85,93)
	  &.router-link-active
	   color: rgb(240,20,20)

</style>
