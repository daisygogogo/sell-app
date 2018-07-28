<template>
  <div class="goods">
  	<div class="menu-wrapper" ref="menuWrapper">
  		<ul >
  			<li v-for="(good,index) in goods"  class="menu-item" :class="{'current':currentIndex==index}" @click="selectMenu(index, $event)">
  				<span class="text"><span v-show="good.type>0" class="icon" :class="classMap[good.type]"></span>{{good.name}}</span>
  		    </li>
  		</ul>
  	</div>
  	<div class="foods-wrapper" ref="foodWrapper">
  		<ul>
  			<li v-for="good in goods" class="food-list food-list-hook">
  				<h1 class="title">{{good.name}}</h1>
  				<ul>
  					<li @click="selectFood(food, $event)" v-for="food in good.foods" class="food-item">
  						<div class="icon"><img width="57" height="57" :src="food.icon"></div>
  						<div class="content">
  							<h2 class="name">{{food.name}}</h2>
  							<p class="desc">{{food.description}}</p>
  							<div class="extra">
  								<span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
  							</div>
  							<div class="price">
  								<span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
  							</div>
  							<div class="cartcontrol-wrapper">
  								<cartControl :food="food"></cartControl>
  							</div>
  						</div>
  					</li>
  				</ul>
  			</li>
  		</ul>
  	</div>
  	<shopcart :delivery-price="seller.deliveryPrice"  :min-price="seller.minPrice" :select-foods="selectFoods"></shopcart>
  	<food :food="selectedFood" ref="food"></food>
  
  </div>
</template>

<script >
import BScroll from 'better-scroll'
import Shopcart from '../../components/shopcart/shopcart'
import CartControl from '../../components/cartcontrol/cartControl'
import food from '../../components/food/food'


const ERR_OK = 0

export default {
  name: 'Goods',
  data () {
    return {
    	goods: [],
    	listHeight: [],
    	scrollY: 0,
    	selectedFood: {}
    }
  },
  props: {
  	seller: {
  		type: Object
  	}
  },
  components: {
	Shopcart, CartControl, food
  },
  computed:{
  	currentIndex() {
  		for (let i = 0; i< this.listHeight.length; i++){
  			let height1 = this.listHeight[i];
  			let height2= this.listHeight[i + 1];
        console.log(height1)
  			if(!height2 || (this.scrollY >= height1 && this.scrollY < height2)){
  				return i
  			}
  		}
  		return 0;
  	},
  	selectFoods() {
  		let foods = [];
  		this.goods.forEach((good) => {
  			good.foods.forEach((food) => {
  				if (food.count) {
  					foods.push(food);
  				}
  			})
  		})
  		return foods;
  	}

  },
   created() {
    this.$http.get('/api/goods').then((response) => {
	  response = response.body
	  if(response.errno === ERR_OK) {
	    this.goods = response.data
	    this.$nextTick(() => {
	    	this._initScroll(); // 获取到数据后调用BScroll方法,由于获取数据后更新dom是异步进行的，所以本方法必须要放到nectTick()方法里面才能起效
	    	this._calculateHeight();
	    })
	    
	  }
	}),
	this.classMap = ['decrease','discount','special','invoice','guarantee']
  },
  methods: {
  	selectMenu(index, event) {
  		if(!event._constructed){
  			return;  // 如果不是better scroll 派发的事件，而是原生的点击事件，那么返回
  		}
  		let foodList = this.$refs.foodWrapper.getElementsByClassName('food-list-hook');
  		let el = foodList[index];
  		this.foodScroll.scrollToElement(el, 300);
  		
  	},
  	selectFood(food, event) {
  		if(!event._constructed){
  			return;  // 如果不是better scroll 派发的事件，而是原生的点击事件，那么返回
  		}
  		this.selectedFood = food;
  		this.$refs.food.show();
  	},
  	_initScroll() {
  		this.menuScroll = new BScroll(this.$refs.menuWrapper,{
  			click:true //取消默认阻止事件
  		});// 这里接收两个参数，一个参数是dom对象，一个参数是option
  		this.foodScroll = new BScroll(this.$refs.foodWrapper,{
  			click:true,
  			probeType:3 // 传这个的意思是，希望滚动的时候可以随时告诉我们Y值，实现实时的监听
  		});
  		this.foodScroll.on('scroll', (pos) => {
  			this.scrollY = Math.abs(Math.round(pos.y)); // 利用回调函数设置scrollY的值
  		})
  	},
  	_calculateHeight(){
  		let foodList = this.$refs.foodWrapper.getElementsByClassName('food-list-hook');
  		let height = 0;
  		this.listHeight.push(height);
  		for (let i = 0; i < foodList.length; i++) {
  			let item = foodList[i];
  			height += item.clientHeight;
  			this.listHeight.push(height);
  		}
  	}
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"

	.goods
		display: flex
		position: absolute
		top: 174px
		bottom: 46px
		width: 100%
		overflow: hidden
		.menu-wrapper
			flex: 0 0 80px  // menu这一栏是固定的
			width: 80px
			background: #f3f5f7
			.menu-item
				display: table
				height: 54px
				width: 56px
				line-height: 14px
				padding: 0 12px
				&.current
					position: relative
					z-index: 10
					margin-top: -1px
					background: #fff
					fontweight: 700
				.icon
					display: inline-block
					width:12px
					height:12px
					margin-right: 2px
					background-size: 12px 12px
					background-repeat: no-repeat
					vertical-align:top
					&.decrease
						bg-image('decrease_3')
					&.discount
						bg-image('discount_3')
					&.special
						bg-image('special_3')
					&.invoice
						bg-image('invoice_3')
					&.guarantee
						bg-image('guarantee_3')
				.text
					display: table-ceil
					width: 56px
					vertical-align: middle
					font-size: 12px

		.foods-wrapper
			flex: 1     // foods这一栏会随着屏幕变大而变大， 自适应的布局
			.title
				padding-left: 14px
				font-size: 12px
				color: rgb(147, 153, 159)
				line-height: 26px
				height: 26px
				border-left: 2px solid #d9dde1
				background: #f3f5f7
			.food-item
				display: flex
				position: relative
				margin: 18px
				padding-bottom: 18px // 要注意，因为上下都有margin，那么上下的margin之间会重合，这样需要增加一个padding-bottom
				border-bottom: 1px solid rgba(7, 17, 27, 0.1)
				&:last-child
					margin-bottom: 0
				.icon
					flex: 0 0 57px
					margin-right: 10px
				.content
					flex: 1
					.name
						height: 14px
						line-height: 14px
						margin: 2px 0 8px 0
						font-size: 14px
						color: rgb(7, 17, 27)
					.desc
						margin-bottom: 8px
						line-height: 12px
						font-size: 10px
						color: rgb(147, 153, 159)
					.extra
						line-height: 10px
						font-size: 10px
						color: rgb(147, 153, 159)
						.count
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
						right: 0
						bottom: 12px






</style>
