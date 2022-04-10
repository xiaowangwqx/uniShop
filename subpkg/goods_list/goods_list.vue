<template>
	<view>
		<view class="goods-list">
			<view v-for="(item, index) in goodsList" :key="index" @click="gotoDetail(item)">
				<my-goods :goods="item"></my-goods>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			// 请求长寿对象
			queryObj: {
				query: '',
				cid: '',
				pagenum: 1,
				pagesize: 10
			},
			goodsList: [],
			total: 0,
			isLoading: false
		};
	},

	onLoad(options) {
		console.log(options, 'options');
		this.queryObj.query = options.query || '';
		this.queryObj.cid = options.cid || '';
		this.getGoodsList();
	},
	methods: {
		async getGoodsList(cb) {
			this.isLoading = true;
			const { data: res } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj);
			console.log(res,'resres')
			this.isLoading=false
			cb&&cb()
			if (res.meta.status !== 200) return uni.$showMsg();
			this.goodsList = [this.goodsList, ...res.message.goods];
			this.total = res.message.total;
		},
		gotoDetail(item){
			console.log(item,'item')
			uni.navigateTo({
				url:'/subpkg/goods_detail/goods_detail?goods_id='+item.goods_id
			})
		}
	},
	onReachBottom() {
		if(this.queryObj.pagenum*this.queryObj.pagesize>=this.total) return uni.$showMsg('数据加载完毕')
		if(this.isLoading) return
		// 页码值+1
		this.queryObj.pagenum += 1;
		// 重新获取列表数据
		this.getGoodsList();
	},
	onPullDownRefresh() {
		this.queryObj.pagenum=1
		this.total=0
		this.isLoading=false
		this.goodsList=[]
		this.getGoodsList(()=>{uni.stopPullDownRefresh()})
	}
};
</script>

<style lang="scss"></style>
