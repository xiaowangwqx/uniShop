<template>
	<view>
		<view class="search-box">
			<uni-search-bar :radius="100" cancelButton="none" @input="input"></uni-search-bar>
		</view>
		
		<!-- 搜索建议列表 -->
		<view class="sugg-list" v-if="searchResults.length!==0">
			<view class="sugg-item" v-for="(item,index) in searchResults" :key="index" @click="gotoDetail(item)">
				<view class="goods-name">{{item.goods_name}}</view>
				<uni-icons type="arrowright" size="16"></uni-icons>
			</view>
		</view>
		
		<!-- 搜索历史 -->
		<view class="history-box" v-else>
			<!-- 标题区域 -->
			<view class="history-title">
				<text>搜索历史</text>
				<uni-icons type="trash" size="17" @click="cleanHistory"></uni-icons>
			</view>
			<!-- 列表区域 -->
			<view class="history-list">
				<uni-tag :text="item" v-for="(item,index) in histories" :key="index" @click="gotoGoodsList(item)"></uni-tag>
			</view>
		</view>
		
	</view>
</template>

<script>
export default {
	data() {
		return {
			timer: null, //延时器
			kw: '', //搜索关键词
			searchResults: [],
			historyList:[],
		};
	},
	onLoad() {
		this.historyList=JSON.parse(uni.getStorageSync('kw')||'[]')
	},
	computed:{
		histories(){
			return [...this.historyList].reverse()
		}
	},
	methods: {
		input(e) {
			clearTimeout(this.timer);
			this.timer = setTimeout(() => {
				// 如果500毫秒内没有触发新的输入事件 则为搜索关键词赋值
				this.kw = e;
				this.getSearchList();
				console.log(this.kw);
			}, 500);
		},
		async getSearchList() {
			if (this.kw.length === 0) {
				this.searchResults = [];
				return;
			}
			const { data: res } = await uni.$http.get('/api/public/v1/goods/qsearch', { query: this.kw });
			if (res.meta.status !== 200) return uni.$showMsg();
			this.searchResults = res.message;
			this.saveSearchHistory()
		},
		gotoDetail(item){
			console.log(item,'item')
			uni.navigateTo({
				url:'/subpkg/goods_detail/goods_detail?goods_id='+item.goods_id
			})
		},
		// 保存历史搜索记录
		saveSearchHistory(){
			// this.historyList.push(this.kw)
			// 1、将Array数组转化为set 对象
			const set =new Set(this.historyList)
			// 2、调用set对象的delete方法 移除对应的元素
			set.delete(this.kw)
			// 3、调用set 对象的add 方法 向set中添加元素
			set.add(this.kw)
			// 4、将set 对象转化为Array数组
			this.historyList=Array.from(set)
			
			// 将搜索历史记录持久化到本地存储中
			uni.setStorageSync('kw',JSON.stringify(this.historyList))
		},
		cleanHistory(){
			this.historyList=[]
			uni.setStorageSync('kw','[]')
		},
		gotoGoodsList(item){
			uni.navigateTo({
				url:'/subpkg/goods_list/goods_list?query='+item
			})
		}
	}
};
</script>

<style lang="scss">
.search-box {
	position: sticky;
	top: 0;
	z-index: 999;
}
.uni-searchbar {
	background-color: #c00000;
}

.sugg-list{
	padding: 0 10px;
	.sugg-item{
		font-size: 12px;
		padding: 10px 0;
		border-bottom: 1px solid #efefef;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.goods-name{
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
		margin-right: 3px;
	}
}

.history-box{
	padding: 10px;
	.history-title{
		display: flex;
		justify-content: space-between;
		height: 40px;
		align-items: center;
		font-size: 13px;
		border-bottom: 1px solid #efefef;
		};
	.history-list{
		display: flex;
		flex-wrap: wrap;
		.uni-tag{
			margin-top: 5px;
			margin-right: 5px;
		}
	}
}
</style>
