<template>
  <div v-show="showFlag" class="food" ref="food">
  	<div class="food-content">
  		<div class="image-header">
  			<img :src="food.image">
  			<div @click="hide" class="back"><i class="icon-arrow_lift"></i></div>
  		</div>
  		<div class="content">
  			<h1 class="title">{{food.name}}</h1>
  			<div class="detail">
  				<span class="sell-count">月售{{food.sellCount}}份</span>
  				<span class="rating">好评率{{food.rating}}%</span>
  			</div>
  			<div class="price">
  				<span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
  			</div>
  		<div class="cartcontrol-wrapper">
  			<cartControl :food="food"></cartControl>
  		</div>
  		<div @click="addFirst" class="buy" v-show="!food.count || food.count===0">加入购物车</div>
  		</div>
  		<split v-show="food.info"></split>
  		<div class="info" v-show="food.info">
  			<h1 class="title">商品信息</h1>
  			<p class="text">{{food.info}}</p>
  		</div>
  		<split></split>
  		<div class="rating">
  			<h1 class="title">商品评价</h1>
  			<ratingselect :select-type="selectType" :only-content="onlyContent" :desc ="desc" :ratings ="food.ratings" @select="selectRating" @toggle="toggleContent"></ratingselect>
  			<div class="rating-wrapper">
  				<ul v-show="food.ratings && food.ratings.length">
  					<li v-show="needShow(rating.rateType, rating.text)" v-for="rating in food.ratings" class="rating-item">
  						<div class="user">
  							<span class="name">{{rating.username}}</span>
  							<img class="avatar" width="12" height="12" :src="rating.avatar">
  						</div>
  						<div class="time">{{rating.rateTime | formateDate}}</div>
  						<p class="text">
  							<span>{{rating.text}}</span>
  						</p>
  					</li>
  				</ul>
  				<div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
  			</div>
  		</div>
  	</div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import CartControl from '../../components/cartcontrol/cartControl'
import split from '../../components/split/split'
import ratingselect from '../../components/ratingselect/ratingselect'
import Vue from 'vue';
import {formateDate} from '../../common/js/date'

	const POSITIVE = 0;
	const NEGATIVE = 1;
	const ALL = 2;

export default {
	props: {
		food: {
			type: Object
		}
	},
	 components: {
	CartControl, split, ratingselect
  },
	data() {
		return {
			showFlag: false,
			selectType: ALL,
			onlyContent: true,
			desc: {
				all: '全部',
				positive: '推荐',
				negative: '吐槽'
			}
		}
	},
	methods: {
		selectRating(type) {
			this.selectType = type;
			this.$nextTick(() =>{ //重新计算高度
				this.scroll.refresh;
			})
		},
		toggleContent() {
			this.onlyContent = !this.onlyContent;
			this.$nextTick(() =>{
				this.scroll.refresh;
			})
		},
		show() { // 父组件可以调用子组件的方法，子组件不能调用父组件的方法，通常前面不带_的方法，外部可以调用
			this.showFlag = true;
			this.selectType = ALL; // 保持每次初始化的时候状态都是一样的
			this.onlyContent = true; 

			this.$nextTick(() =>{
				if(!this.scroll){
					this.scroll = new BScroll(this.$refs.food,{
						click: true
					})
				}else{
					this.scroll.refresh(); // 否则，重新做一次计算，刷新
				}
			})
		},
		hide() {
			this.showFlag = false;
		},
		addFirst() {
			if(!event._constructed){
  				return;  // 如果不是better scroll 派发的事件，而是原生的点击事件，那么返回
  			}
  			Vue.set(this.food, 'count', 1)
		},
		needShow(type, text) {
			if (this.onlyContent && !text){
				return false; // 把函数表达式的结果返回给v-show指令
			}
			if (this.selectType == ALL){
				return true;
			} else {
				return type === this.selectType;
			}
		}
	},
	filters: {
		formateDate(time) {
			let date = new Date(time);
			return formateDate(date, 'yyyy-MM-dd hh:mm');
		}
	}
}

</script>

<!-- padding-bottom: 100% -->
<style lang="stylus" rel="stylesheet/stylus">
.food
	position: fixed
	left: 0
	top: 0
	bottom: 48px
	z-index: 30
	width: 100%
	background: #fff
	.image-header
		position: relative
		width: 100%
		height: 0
		padding-top: 100%
		img
			position: absolute
			top: 0
			left: 0
			width: 100%
			height: 100%
		.back
			position: absolute
			top: 10px
			left: 0
			.icon-arrow_lift
				display: block
				padding: 10px
				font-size: 20px
				color: #fff
	.content
		padding: 18px
		position:relative
		display: block
		.title
			line-height: 14px
			margin-bottom: 8px
			font-size: 14px
			font-weight: 700
			color: rgb(7, 17, 27)
		.detail
			margin-bottom: 18px
			line-height: 10px
			height: 10px
			font-size: 0
			.sell-count, .rating
				font-size: 10px
				color: rgb(147, 153, 159)
			.sell-count
				margin-right: 12px
		.price
			font-weight: 700
			line-height: 24px
			.now
				margin-right: 8px
				font-size: 14px
				color: rgb(240, 20, 20)
			.old
				text-decoration: line-through
				font-size: 10px
				color: rgb(147, 153, 159)
		.cartcontrol-wrapper
			position: absolute
			right: 12px
			bottom: 12px
		.buy
			position: absolute
			right: 18px
			bottom: 18px
			z-index: 10
			height: 24px
			line-height: 24px
			padding: 0 12px
			box-sizing: border-box
			font-size: 10px
			border-radius: 12px
			color: #fff
			background: rgb(0, 160, 220)
	.info
		padding: 18px
		.title
			line-height: 14px
			margin-bottom: 6px
			font-size: 14px
			color: rgb(7, 17, 27)
		.text
			line-height: 24px
			padding: 0 8px
			font-size: 12px
			color: rgb(77, 85, 93)
	.rating
		padding-top: 18px
		.title
			line-height: 14px
			margin-left: 18px
			font-size: 14px
			color: rgb(7, 17, 27)
		.rating-wrapper
			padding: 0 18px
			.rating-item
				position: relative
				padding: 16px 0
				border-bottom: 1px solid rgba(7, 17, 27, 0.1)
				.user
					position: absolute
					right: 0
					top: 16px
					line-height:12px
					font-size: 0
					.name
						display: inline-block
						font-size: 10px
						margin-right: 6px
						vertical-align: top
						color: rgb(147, 153, 159)
					.avatar
						border-radius: 50%
				.time
					line-height: 12px
					font-size: 10px
					color: rgb(147, 153, 159)
					margin-bottom: 6px
					.text
						line-height: 16px
						font-size: 12px
						color: rgb(7, 17, 27)
			.no-rating
				padding: 16px 0
				font-size: 12px
				color: rgb(147, 153, 159)










</style>
