<template>
  <div class="ratings" ref="ratings">
  	<div class="ratings-content">
  		<div class="overview">
  			<div class="overview-left">
  				<h1 class="score">{{seller.score}}</h1>
  				<div class="title">综合评分</div>
  				<div class="rank">高于周边商家{{seller.rankRate}}%</div>
  			</div>
  			<div class="overview-right">
  				<div class="score-wrapper">
  					<span calss="title">服务态度</span><star :size="36" :score="seller.serviceScore"></star><span class="score">{{seller.serviceScore}}</span>
  				</div>
  				<div class="score-wrapper">
  					<span calss="title">商品评分</span><star :size="36" :score="seller.foodScore"></star><span class="score">{{seller.foodScore}}</span>
  				</div>
  				<div class="delivery-wrapper">
  					<span class="title">送达时间</span>
  					<span class="delivery">{{seller.deliveryTime}}分钟</span>
  				</div>
  			</div>
  		</div>
  		<split></split>
  		<div class="rating-content">
  			<ratingselect :select-type="selectType" :only-content="onlyContent" :desc ="desc" :ratings ="ratings" @select="selectRating" @toggle="toggleContent" ></ratingselect>
  			<div class="rating-wrapper">
  				<ul v-show="ratings && ratings.length">
  					<li v-show="needShow(rating.rateType, rating.text)" v-for="rating in ratings" class="rating-item">
  						<div class="rating-list">
  							<div class="avatar">
  								<img class="avatar" width="28" height="28" :src="rating.avatar">
  							</div>
  							<div class="content">
  								<div>
  									<span class="username">{{rating.username}}</span>
  									<div class="score-wrapper">
  										<star :size="24" :score="rating.score"></star>
  										<span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
  									</div>	
  								</div>
  								<p class="text">
  									<span>{{rating.text}}</span>
  								</p>
  							<div class="time">{{rating.rateTime | formateDate}}</div>	
  							</div>	
  						</div>	
  					</li>
  				</ul>
  				<div class="no-rating" v-show="!ratings || !ratings.length">暂无评价</div>
  			</div>
  		</div>
  	</div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import Star from '../../components/star/star'
import split from '../../components/split/split'
import ratingselect from '../../components/ratingselect/ratingselect'
import {formateDate} from '../../common/js/date'

    const POSITIVE = 0;
	const NEGATIVE = 1;
	const ALL = 2;
	const ERR_OK = 0

export default {
  name: 'Ratings',
  props: {
  	seller :{
  		type: Object
  	}
  },
  components: {
  	Star,
  	split,
  	ratingselect
  },
	data() {
		return {
			ratings: [],
			showFlag: false,
			selectType: ALL,
			onlyContent: true,
			desc: {
				all: '全部',
				positive: '满意',
				negative: '不满意'
			}
		}
	},
	created() {
	    this.$http.get('/api/ratings').then((response) => {
		  response = response.data
		  if(response.errno === ERR_OK) {
		    this.ratings = response.data
		    this.$nextTick(() => {
		    	this._initScroll(); // 获取到数据后调用BScroll方法,由于获取数据后更新dom是异步进行的，所以本方法必须要放到nectTick()方法里面才能起效
		    })
		  }
		})
  },
  filters: {
		formateDate(time) {
			let date = new Date(time);
			return formateDate(date, 'yyyy-MM-dd hh:mm');
		}
	},
  methods: {
  	_initScroll() {
  		if(!this.scroll){
					this.scroll = new BScroll(this.$refs.ratings,{
						click: true
					})
				}else{
					this.scroll.refresh(); // 否则，重新做一次计算，刷新
				}
  	},
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
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus" rel="stylesheet/stylus">
.ratings
	position: absolute
	top: 174px
	bottom: 0
	left: 0
	width: 100%
	overflow: hidden
	.overview
		display:flex
		padding: 18px 0
		.overview-left
			padding: 6px 0
			flex: 0 0 137px
			width: 137px
			border-right: 1px solid rgba(7, 17, 27, 0.1)
			text-align: center
			@media only screen and (max-width: 320px)
				flex:0 0 120px
				width: 120px
			.score
				margin-bottom: 6px
				line-height: 28px
				font-size: 24px
				color: rgb(255, 153, 0)
			.title
				margin-bottom: 8px
				line-height: 12px
				font-size: 12px
				color: rgb(7, 17, 27)
			.rank
				line-height: 10px
				font-size: 10px
				color: rgb(147, 153, 159)
		.overview-right
			flex: 1
			padding: 6px 0 6px 24px
			@media only screen and (max-width: 320px)
				padding-left: 6px
			.score-wrapper
				margin-bottom: 8px
				font-size: 12px
				.star
					line-height: 18px
					display: inline-block
					margin: 0 12px
					vertical-align: top
				.score
					line-height: 18px
					display: inline-block
					vertical-align: top
					font-size: 12px
					color: rgb(255, 153, 0)
				.title
					line-height: 18px
					display: inline-block
					vertical-align: top
					font-size: 12px
			.delivery-wrapper
				font-size: 0
				.title
					margin-bottom: 8px
					line-height: 12px
					font-size: 12px
					color: rgb(7, 17, 27)
				.delivery
					font-size: 12px
					margin-left: 12px
					color: rgb(147, 153, 159)
	.rating-content
		.rating-wrapper
			padding: 0 18px 0 18px
			.rating-list
				display: flex
				border-bottom: 1px solid rgba(7,17,27,0.1)
				position: relative
				padding: 18px 0
				.avatar
					display: 0 0 28px
					width: 28px
					margin-right: 12px
					img
						border-radius: 50%
				.content
					display: 1
					.username
						line-height: 12px
						font-size: 10px
						color: rgb(7, 17, 27)
					.score-wrapper
						padding: 6px 0
						.star
							line-height: 12px
							margin-right: 6px
							display: inline-block
							vertical-align: top
						.delivery
							display: inline-block
							vertical-align: top
							font-size: 10px
							font-weight:200
							line-height: 12px
							color: rgb(147, 153, 159)
					.text
						line-height: 18px
						font-size: 12px
						color: rgb(7, 17, 27)
						margin-bottom: 8px
					.time
						position: absolute
						right: 0
						top: 18px
						line-height: 12px
						font-size: 10px
						font-weight: 200
						color: rgb(147, 153, 159)






</style>
