<template>
  <div class="cartcontrol"> 
  	<div class="cart-descrease icon-remove_circle_outline" v-show="food.count>0" @click.stop.click="descreaseCart" transition="move">
      <span class="inner icon-remove_circle_outline"></span>
    </div>
  	<div class="cart-count"  v-show="food.count>0">{{food.count}}</div>
  	<div class="cart-add icon-add_circle" @click.stop.click="addCart"> 
    </div>
  </div>
</template>

<script >
import Vue from 'vue';

export default {
  name: 'CartControl',
  props: {
  	food: {
  		type: Object
  	}
  },
  methods: {
    addCart() {
      if(!this.food.count) {
        Vue.set(this.food,'count', 1); // 通过vue.set给对象增加属性，这样属性才能被检测到
      } else {
        this.food.count++;
      }
    },
    descreaseCart() {
      if(this.food.count) {
        this.food.count--;
      }
    }
  }
}
</script>

<!-- 可以通过padding去增加可点击区域-->
<style lang="stylus" rel="stylesheet/stylus">

.cartcontrol
  font-size: 0
  .cart-descrease
    display: inline-block
    padding: 6px
    transition: all 0.4s linear
    &.move-transition
      opacity: 1
      transform: translate3D(0, 0, 0)
    .inner                     
      line-height: 24px
      font-size: 24px
      color: rgb(0, 160, 220)
    &.move-enter, &.move-leave
      opacity: 0
      transform: translate3D(24px, 0, 0)
  .cart-count
    display: inline-block
    vertical-align: top
    width: 12px
    padding-top: 6px
    line-height: 24px
    text-align: center
    font-size: 10px
    color: rgb(147, 153, 159)
  .cart-add
    display: inline-block
    padding: 6px                     
    line-height: 24px
    font-size: 24px
    color: rgb(0, 160, 220)
  .cart-add
    display: inline-block
    padding: 6px
    line-height: 24px
    font-size: 24px
    color: rgb(0, 160, 220)

  
   


</style>
