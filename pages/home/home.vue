<template>
	<view>
		<view class="search-box">
			<my-search @search="goSearch"></my-search>
		</view>
		<!-- 轮播图 -->
		<swiper :indicator-dots="true" autoplay="true" interval="3000" :duration="2000" :circular="true">
			<!-- 循环渲染轮播图item项 -->
			<swiper-item v-for="(item, index) in swiperList" :key="index">
				<navigator :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id" class="swiper-item">
					<image :src="item.image_src"></image>
				</navigator>
			</swiper-item>
		</swiper>

		<!-- 分类导航区域 -->
		<view class="nav-list">
			<view class="nav-item" v-for="(item, index) in navList" :key="index" @click="navClickHandler(item)">
				<image :src="item.image_src" class="nav-img"></image>
			</view>
		</view>

		<!-- 楼层区域 -->
		<view class="floor-list">
			<view class="floor-item" v-for="(item, index) in floorList" :key="index">
				<!-- 楼层标题 -->
				<image :src="item.floor_title.image_src" class="floor_title"></image>

				<!-- 楼层图片区域 -->
				<view class="floor-img-box">
					<navigator class="left-img-box" :url="item.product_list[0].url">
						<image
							mode="widthFix"
							:style="{ width: item.product_list[0].image_width + 'rpx' }"
							:src="item.product_list[0].image_src"
						></image>
					</navigator>
					<view class="right-img-box">
						<navigator
							class="right-img-item"
							v-for="(v, i) in item.product_list"
							:key="i"
							v-if="i !== 0"
							:url="v.url"
						>
							<image :src="v.image_src" mode="widthFix" :style="{ width: v.image_width + 'rpx' }"></image>
						</navigator>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			swiperList: [],
			navList: [],
			floorList: []
		};
	},
	onLoad() {
		this.getSwiperList();
		this.getNavList();
		this.getFloorList();
	},
	methods: {
		async getSwiperList() {
			const { data: res } = await uni.$http.get('/api/public/v1/home/swiperdata');
			console.log(res);
			if (res.meta.status !== 200) return uni.$showMsg();
			this.swiperList = res.message;
		},

		async getNavList() {
			const { data: res } = await uni.$http.get('/api/public/v1/home/catitems');
			console.log(res);
			if (res.meta.status !== 200) return uni.$showMsg();
			this.navList = res.message;
		},

		navClickHandler(item) {
			if (item.name === '分类') {
				uni.switchTab({
					url: '/pages/cate/cate'
				});
			}
		},

		async getFloorList() {
			const { data: res } = await uni.$http.get('/api/public/v1/home/floordata');
			console.log(res);
			if (res.meta.status !== 200) return uni.$showMsg();

			// 通过双层forEach循环 处理URL地址
			res.message.forEach(floor => {
				floor.product_list.forEach(prod => {
					prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1];
				});
			});

			this.floorList = res.message;
		},

		goSearch() {
			uni.navigateTo({
				url: '../../subpkg/search/search'
			});
		}
	}
};
</script>

<style lang="scss">
	.search-box{
		position: sticky;
		top: 0;
		z-index: 999;
	}
swiper {
	height: 330rpx;

	.swiper-item,
	image {
		width: 100%;
		height: 100%;
	}
}

.nav-list {
	display: flex;
	justify-content: space-around;
	margin: 10px 0;

	.nav-img {
		width: 128rpx;
		height: 140rpx;
	}
}

.floor_title {
	width: 100%;
	height: 60rpx;
}

.floor-img-box {
	display: flex;
	padding-left: 10rpx;

	.right-img-box {
		display: flex;
		flex-wrap: wrap;
		justify-content: space-around;
	}
}
</style>
