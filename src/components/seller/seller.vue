<template>
  <div class="seller" ref="seller">
  	<div class="seller-content">
  		<div class="overview">
  			<h1 class="title">{{seller.name}}</h1>
  			<div class="desc">
  				<star :size="36" :score="seller.serviceScore"></star>
  				<span class="text">({{seller.ratingCount}})</span>
  				<span class="text">月售{{seller.sellCount}}单</span>
  			</div>
  			<ul class="remark">
  				<li class="block">
  					<h2>起送价</h2>
  					<div class="content1">
  						<span class="stress">{{seller.minPrice}}<span class="text">元</span></span>
  					</div>
  				</li>
  				<li class="block">
  					<h2>商家配送</h2>
  					<div class="content1">
  						<span class="stress">{{seller.deliveryPrice}}<span class="text">元</span></span>
  					</div>
  				</li>
  				<li class="block">
  					<h2>平均配送时间</h2>
  					<div class="content1">
  						<span class="stress">{{seller.deliveryTime}}<span class="text">分钟</span></span>
  					</div>
  				</li>
  			</ul>
  			<div class="favorite" @click="toggleFavorite">
  				<span class="icon-favorite" :class="{isActive:favorite}"></span>
  				<span class="text">{{favoriteText}}</span>
  			</div>
  		</div>
  		<split></split>
  		<div class="bulletin">
  			<h1 class="title">公告与活动</h1>
  			<div class="content-wrapper1">
  				<p class="content1">{{seller.bulletin}}</p>
  			</div>
  			<ul v-if="seller.supports" class="supports">
  				<li class="support-item" v-for="item in seller.supports">
  					<span class="icon" :class="classMap[item.type]"></span>
  					<span class="text">{{item.description}}</span>
  				</li>
  			</ul>
  		</div>
  		<split></split>
  		<div class="pics">
  			<h1 class="title">商家实景</h1>
  			<div class="pic-wrapper" ref="picWrapper">
  				<ul class="pic-list" ref="picList">
  					<li class="pic-item" v-for="pic in seller.pics">
  						<img width="120" height="90" :src="pic">
  					</li>  				
  				</ul>
  			</div>	
  		</div>
  		<split></split>
  		<div class="info">
  			<h1 class="title">商家信息</h1>
  			<ul>
  				<li class="info-item" v-for="info in seller.infos">{{info}}</li>
  			</ul>
  		</div>
  		<split></split>
  	</div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import Star from '../../components/star/star'
import split from '../../components/split/split'
import {saveToLocal} from '../../common/js/store'
import {loadFromLocal} from '../../common/js/store'

export default {
  name: 'Seller',
  props: {
  	seller: {
  		type: Object
  	}
  },
  components: {
  	Star,split
  },
  data () {
    return {
    	favorite: (() =>{
    		return loadFromLocal(this.seller.id, 'favorite', false);
    	})()
    }
  },
  created() {
	this.classMap = ['decrease','discount','special','invoice','guarantee'];
  },
  computed: {
  	favoriteText() {
  		return this.favorite?'已收藏':'收藏';
  	}
  },
  watch: {
  	/*watch监听seller这个数据，一旦seller数据发生变化，就立即调用seller内部定义的方法，这里是this._initScroll();和this._initPics();*/
  	'seller'() {
  		this.$nextTick(() => {
  			this._initScroll();
  			this._initPics();
  			}) 
  	}
  },
/*  ready(){ // 在这个组件里，BS的初始化要在ready函数里初始化，因为如果要跟以往一样，放到created里的话，在这里并不能保证DOM已经被渲染了，因为数据并不是在这里初始化的，所以我们要使用ready函数，这个函数的意思就是dom已经被渲染好了
  	this._initScroll();
  },*/
  mounted() { //nounted在模板渲染成html后调用，通常是初始化页面完成后，再对html的dom节点进行一些需要的操作。
      this.$nextTick(() => {
        this._initScroll();
        this._initPics();
      });
    },
  methods: {
  	toggleFavorite($event) {
  		if(!event._constructed){
  			return;
  		}
  		this.favorite = !this.favorite;
  		saveToLocal(this.seller.id, 'favorite', this.favorite);
  	},
  	_initScroll() {
  			if (!this.scroll) {
					this.scroll = new BScroll(this.$refs.seller,{
						click: true
					})
				} else {
					this.scroll.refresh(); // 否则，重新做一次计算，刷新
				}
  	},
  	_initPics(){
  		if (this.seller.pics){
  			let picWidth = 120;
  			let margin = 6;
  			let width = (picWidth + margin)*this.seller.pics.length - margin;
  			this.$refs.picList.style.width = width + 'px'; // ref 对应的dom的书写方法，直接是picList，而不是pic-list  			
  			 	if (!this.picScroll) {
	              this.picScroll = new BScroll(this.$refs.picWrapper, {
	                scrollX: true,
	                eventPassthrough: 'vertical'
	              });
	            } else {
	              this.picScroll.refresh();
	            } 
  		}
  	}
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"
.seller
	position: absolute
	top: 174px
	bottom: 0
	left: 0
	width: 100%
	overflow: hidden
	.overview
		padding: 18px
		position: relative 
		.title
			line-height: 14px
			font-size: 14px
			color: rbg(7, 17, 27)
			padding-bottom: 8px
		.desc
			padding-bottomx: 18px
			font-size: 0
			border-bottom: 1px solid rgba(7, 17, 27, 0.1)
			.star
				display: inline-block
				vertical-align: top
				margin-right: 8px
			.text
				display: inline-block
				vertical-align: top
				margin-right: 12px
				line-height: 18px
				font-size: 10px
				color: rgb(77, 85, 93)
		.remark
			display: table
			width: 100%
			padding-top: 18px
			.block
				display: table-cell
				text-align: center
				border-right: 1px solid rgba(7, 17, 27, 0.1)
				&:last-child
					border: none
				h2
					font-size: 10px
					line-height: 10px
					color: rgb(147, 153, 159)
					padding-bottom: 4px
				.content1
					line-height: 14px
					font-size: 24px
					font-weight: 200
					color: rgb(7, 17, 27)
					.text
						font-size: 10px
		.favorite
			position: absolute
			right: 11px
			top: 18px
			width: 50px
			text-align: center
			.icon-favorite
				display: block
				margin-bottom: 4px
				line-height: 24px
				font-size: 24px
				color: #d4d6d9
				&.isActive
					color: rgb(240, 20, 20)
			.text
				line-height: 10px
				font-size: 10px
				color: rgb(77, 85,93)
	.bulletin
		padding: 18px 18px 0 18px
		.title
			margin-bottom: 8px
			line-height: 14px
			font-size: 14px
			color: rgb(7, 17, 27)
		.content-wrapper1
			padding: 0 12px 16px 12px
			border-bottom: 1px solid rgba(7, 17, 27, 0.1)
			.content1
				line-height: 24px
				font-size: 12px
				color: rgb(240, 20, 20)
		.supports
			.support-item
				padding: 16px 12px 
				font-size: 0
				border-bottom: 1px solid rgba(7, 17, 27, 0.1)
				&:last-child
					border: none
				.icon
					display: inline-block
					width:16px
					height:16px
					margin-right: 6px
					background-size: 16px 16px
					background-repeat: no-repeat
					vertical-align:top
					&.decrease
						bg-image('decrease_4')
					&.discount
						bg-image('discount_4')
					&.special
						bg-image('special_4')
					&.invoice
						bg-image('invoice_4')
					&.guarantee
						bg-image('guarantee_4')
				.text
					line-height: 16px
					font-size: 12px
					color: rgb(7, 17, 27)
	.pics
		padding: 18px
		.title
			margin-bottom: 12px
			line-height: 14px
			font-size: 14px
			color: rgb(7, 17, 27)
		.pic-wrapper
			width: 100%
			overflow: hidden
			white-space: nowrap
			.pic-list
				font-size: 0
				.pic-item
					display: inline-block
					margin-right: 6px
					width: 120px
					height: 90px
					&:last-child
						margin: 0
	.info
		padding: 18px 18px 0 18px
		color: rgb(7, 17, 27)
		.title
			padding-bottom: 12px
			line-height: 14px
			font-size: 14px
			border-bottom: 1px solid rgba(7, 17, 27, 0.1)
		.info-item
			padding: 16px 12px
			line-height: 16px
			border-bottom: 1px solid rgba(7, 17, 27, 0.1)
			font-size: 12px
			&:last-child
				border: none







	

</style>
