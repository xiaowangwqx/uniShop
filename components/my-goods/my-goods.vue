<template>
	<view>
		<view class="goods-item">
			<view class="goods-item-left">
				<image class="goods-pic" :src="goods.goods_small_logo||defailtPic"></image>
			</view>
			<view class="goods-item-right">
				<view class="goods-name">{{goods.goods_name}}</view>
				<!-- <view class="goods-info-box"> -->
					<view class="goods-price">￥{{goods.goods_price|tofixed}}</view>
				<!-- </view> -->
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name:"my-goods",
		props:{
			goods:{
				type:Object,
				default:{}
			}
		},
		filters:{
			// 将价格处理未带两位小数点的价格
			tofixed(num){
				return Number(num).toFixed(2)
			}
		},
		data() {
			return {
				defailtPic:'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
			};
		},
		
	}
</script>

<style lang="scss">
	.goods-list{
		.goods-item{
			display: flex;
			padding: 10px 5px;
			border-bottom: 1px solid #F0F0F0;
			.goods-item-left{
				margin-right: 10px;
				.goods-pic{
					width: 100px;
					height: 100px;
					display: block;
				}
			}
			.goods-item-right{
				display: flex;
				flex-direction: column;
				justify-content: space-between;
				.goods-name{
					font-size: 13px;
				}
			
				.goods-price{
					font-size: 16px;
					color: #c00000;
				}
			}
		}
	}
</style>
