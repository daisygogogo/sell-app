<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
	    <img width="64" height="64" :src="seller.avatar">
      </div>
	  <div class="content">
	    <div class="title">
          <span class="brand"></span>
		  <span class="name">{{seller.name}}</span>
		</div>
		<div class="description">
		  {{seller.description}}/{{seller.deliveryTime}}分钟送达
		</div>
		<div v-if="seller.supports" class="support">
		  <span class="icon" :class="classMap[seller.supports[0].type]"></span><span class="text">{{seller.supports[0].description}}</span>
		</div>
      </div>
	  <div v-if="seller.supports"class="supports-count" @click="showDetail">
		<span class="count">{{seller.supports.length}}个</span>
		<i class="icon-keyboard_arrow_right"></i>
	  </div>
    </div>	
    <div class="bulletin-wrapper" @click="showDetail">
		<span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
		<i class="icon-keyboard_arrow_right"></i>
	</div>
	<div class="background">
		 <img width="100%" height="100%" :src="seller.avatar">
	</div>
	<div v-show="show" class="detail">
		<div class="detail-wrapper clearfix">
			<div class="detail-main">
				 <h1 class="name">{{seller.name}}</h1>
				 <div class="star-wrapper"><star :size="48" :score="seller.score"></star></div>
				 <div class="title">
					<div class="line"></div>
					<div class="text">优惠信息</div>
					<div class="line"></div>
				 </div>
			</div>
		</div>
	<div class="detail-close">
		<i class="icon-close"></i>
	</div>
	
	</div>
  </div>
</template>

<script type="text/ecmascript-6">
import Star from '../../components/star/star'

export default {
  props: {
    seller: {
	  type: Object
	}
  },
  created() {
	this.classMap = ['decrease','discount','special','invoice','guarantee']
  },
  components: {
	Star
  },
  name: 'Header',
  data () {
    return {
	  show: false
    }
  },
  methods: {
     showDetail: function(){
	   this.show = true
	 }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus" rel="stylesheet/stylus" >
@import "../../common/stylus/mixin.styl"


.header
  position: relative
  overflow: hidden
  color: #fff
  background: rgba(7, 17, 27, 0.5)
  .detail
    position: fixed
    left: 0
    top: 0
    width: 100%
    height: 100%
    background-color: rgba(7, 17, 27, 0.8)
	.content-wrapper
	    position: relative
	    padding: 24px 12px 18px 24px
		.supports-count
	      position: absolute
	      right: 12px
	      bottom: 14px
	      padding: 0 8px
	      line-height: 24px
	      height: 24px
	      border-radius: 14px
	      background: rgba(0, 0, 0, 0.2)
	      text-align: center
		  .count
	        font-size: 10px
	      .icon-keyboard_arrow_right
		    height: 24px
			line-height: 24px
	        margin-left: 2px
	        font-size: 10px
	        vertical-align: top
		.avatar
		  display: inline-block
		  vertical-align: top
		  img
		    border-radius: 2px
		.content
		  margin-left:16px
		  display: inline-block
		  .title
		    margin: 2px 0 8px 0
			.brand
			  display: inline-block
			  vertical-align:top
			  width:30px
			  height:18px
			  bg-image('brand')
			  background-size: 30px 18px  // 设置了背景图片之后，记得设置背景尺寸
			  background-repeat: no-repeat
			.name
			  margin-left: 6px
			  line-height: 18px
			  font-size: 16px
			  font-weight: bold
		  .description
		    margin-bottom: 10px
		    font-size:12px
			font-weight:200
			line-height:12px
	  .detail
	    .detail-wrapper   // 为了当内容不满屏的时候也能撑开满屏，由于x按钮dom与之平级，并且用负的margin给提上来了，所以就会固定在底部
	      min-height: 100%
	      .detail-main
	        margin-top: 64px
	        padding-bottom: 64px // 当内容满屏的时候要padding-bottom给x按钮留出空间，否则内容会覆盖x按钮
	        .name
	          font-size: 16px
			  text-align: center
			  font-weight: 700
			  line-height: 16px
			.star-wrapper
			  margin-top:18px
			  padding: 2px 0
			  text-align: center
			.title
	          display: flex
	          width:80%
	          margin: 28px auto 24px auto
	          .line
	            flex: 1
	            position: relative
	            top: -6px
	            border-bottom: 1px solid rgba(255, 255, 255, 0.2)
	          .text
	            padding: 0 12px
				font-weight:700
	            font-size: 12px

.detail
    .detail-close
      position: relative
      width: 32px
      height: 32px
      margin: -64px auto 0 auto // 因为内容满屏的话detail-close是不占空间的，所以要给一个负的margin，否则没法显示出来
      clear: both
      font-size: 32px	

.background
    position: absolute
    top: 0
    left: 0
    width: 100%
    height: 100%
    z-index: -1
    filter: blur(10px)

.bulletin-wrapper
    position: relative
    height: 28px
    line-height: 28px
    text-align: center
    padding: 0px 22px 0px 12px
    background-color: rgba(7, 17, 27, 0.2)
    overflow:hidden
    white-space: nowrap
    text-overflow: ellipsis
	.bulletin-title
	  display: inline-block
	  vertical-align: top
	  margin-top: 8px
	  width: 22px
	  height: 12px
	  bg-image('bulletin')
	  background-size: 22px 12px
	  background-repeat: no-repeat
	.bulletin-text
	  vertical-align: top
	  font-size: 10px
	  margin: 0px 4px
	  font-weight: 200
	.icon-keyboard_arrow_right
	  position: absolute
	  font-size: 10px
	  right: 12px
	  top: 10px
.support
	.icon
		display: inline-block
		width:12px
		height:12px
		margin-right: 4px
		background-size: 12px 12px
		background-repeat: no-repeat
		vertical-align:bottom
		&.decrease
			bg-image('decrease_1')
		&.discount
			bg-image('discount_1')
		&.special
			bg-image('special_1')
		&.invoice
			bg-image('invoice_1')
		&.guarantee
			bg-image('guarantee_1')
	.text
		font-size:10pxcontent
		line-height: 12px
		font-weight:200
</style>
