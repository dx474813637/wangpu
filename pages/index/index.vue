<template>
	<view>
		<view v-for="(item, index) in homelist.list" :key="index">
			<!-- diy元素: 图片轮播 -->
			<view v-if="item.type == 'swiper'">
				<u-swiper
					@change="swiper_change"
					:height="item.height"
					:list="item.list"
					:title="item.title"
					:effect3d="item.effect3d"
					:indicator-pos="item.indicatorPos"
					:mode="item.mode"
					:sindex="index"
					:interval="item.interval"
					@click="swiper_click"
				></u-swiper>
			</view>

			<!-- diy元素: 线条 -->
			<view v-if="item.type == 'line'" :style="'padding-top:' + item.paddtop + 'px' + ';' + 'padding-bottom:' + item.paddbottom + 'px'">
				<u-line
					class="u-line"
					:border-style="item.borderStyle"
					color="red"
					:color="item.color"
					:length="item.length"
					:direction="item.direction"
					:hair-line="item.hairLine"
				></u-line>
			</view>

			<!-- diy元素: 间隔槽 -->
			<view v-if="item.type == 'gap'"><u-gap :bg-color="item.bgColor" :height="item.height" :margin-top="item.marginTop" :margin-bottom="item.marginBottom"></u-gap></view>

			<!-- diy元素: 搜索 -->
			<view v-if="item.type == 'search'" class="padding-sm">
				<u-search
					v-model="item.value"
					@change="search_change"
					@custom="search_custom"
					@search="search_search"
					:shape="item.shape"
					:clearabled="item.clearabled"
					:show-action="item.showAction"
					:input-align="item.inputAlign"
					@clear="search_clear"
				></u-search>
			</view>

			<!-- diy元素: 标题 -->
			<view class="u-config-wrap" v-if="item.type == 'title'">
				<view class="u-config-item">
					<u-row>
						<u-col span="10">
							<view class="u-item-title">{{ item.title }}</view>
						</u-col>
						<u-col span="2" v-if="item.more"><view class="u-item-more" @click="go_to(item.url)">{{item.more_title}}</view></u-col>
					</u-row>
				</view>
			</view>

			<!-- diy元素: 滚动通知 -->
			<view v-if="item.type == 'notice'">
				<u-notice-bar
					:autoplay="item.autoplay"
					:playState="item.playState"
					:speed="item.speed"
					@getMore="notice_getMore"
					:mode="item.mode"
					@end="notice_end"
					@close="notice_close"
					@click="notice_click"
					:show="item.show"
					:type="item.noticetype"
					:list="item.list"
					:moreIcon="item.moreIcon"
					:volumeIcon="item.volumeIcon"
					:duration="item.duration"
					:isCircular="item.isCircular"
				></u-notice-bar>
			</view>

			<!-- diy元素: 富文本 -->
			<view v-if="item.type == 'parse'">
				<view class="padding-sm"><u-parse :html="item.content"></u-parse></view>
			</view>

			<!-- diy元素: 单图片 -->
			<view v-if="item.type == 'images'">
				<view :style="item.style" @click="go_to(item.url)" >
					<image
						:src="item.src"
						mode="widthFix"
						:lazy-load="true"
						:style="'border-radius:' + item.radius + 'px;' + 'background-color:' + item.bgcolor + ';width:100%;'"
					></image>
				</view>
			</view>

			<!-- diy元素: 导航组 -->
			<view v-if="item.type == 'navbar'">
				<u-row>
					<u-col :span="item.col" v-for="(item1, index1) in item.list" :key="index1">
						<view v-show="item1.src" :style="item.style" @click="go_to(item1.url)">
							<image
								:src="item1.src"
								mode="widthFix"
								:lazy-load="true"
								:style="'border-radius:' + item.radius + 'px;' + 'background-color:' + item.bgcolor + ';'"
							></image>
							<view v-show="item1.name" class="text-center text-df padding-top-sm">{{ item1.name }}</view>
						</view>
					</u-col>
				</u-row>
			</view>

			<!-- diy元素: 图片资讯 -->
			<view class="newslist" v-if="item.type == 'news'">
				<view v-for="(item1, index1) in item.list" :key="index1">
					<view class="padding radius shadow -blue " :style="item.stylea">
						<view class="margin-top cf flex" v-if="item.left == 1" @click="go_to(item1.url)">
							<view class="text-lg text-left text-cut text-black title flex-sub" :style="item.styleb">
								{{ item1.title }}
								<view class="text-sm text-left text-cut title flex-sub padding-top text-gray" :style="item.stylec">{{ item1.ctime }}</view>
							</view>
							<image lazy-load="true" mode="scaleToFill" class="fl margin-right radius" :src="item1.image" style="height: 100upx; width: 200upx;" :style="item.styled"></image>
						</view>

						<view class="margin-top cf flex" v-if="item.left == 2" @click="go_to(item1.url)">
							<image lazy-load="true" mode="scaleToFill" class="fl margin-right radius" :src="item1.image" style="height: 100upx; width: 200upx;" :style="item.styled"></image>
							<view class="text-lg text-left text-cut text-black title flex-sub">
								{{ item1.title }}
								<view class="text-sm text-left text-cut title flex-sub padding-top text-gray">{{ item1.ctime }}</view>
							</view>
						</view>
					</view>
				</view>
			</view>

			<!-- diy元素: 商品 -->
			<view class="padding-tb-sm" v-if="item.type == 'product'">
				<view v-if="item.cate == 1">
					<view v-for="(item1, index1) in item.list" :key="index1">
						<view v-show="item1.image" class="padding radius shadow" @click="go_to(item1.url)" :style="item.stylea">
							<view class="margin-top cf flex">
								<image lazy-load="true" mode="scaleToFill" class="fl margin-right radius" :src="item1.image" style="height: 200upx; width: 200upx;" :style="'border-radius:' + item.radius + 'px;'"></image>
								<view class="text-xl text-left  text-cut  text-black title flex-sub" :style="item.styleb">
									{{ item1.title }}
									<view v-show="item1.info" class="text-sm text-left text-cut title flex-sub padding-top text-gray" :style="item.stylec">{{ item1.info }}</view>
									<view v-show="item1.price" class="text-lg text-left text-cut title flex-sub padding-top text-red" :style="item.styled">¥{{ item1.price }}</view>
								</view>
							</view>
						</view>
					</view>
				</view>

				<view v-if="item.cate == 2">
					<u-row>
						<u-col span="6" v-for="(item1, index1) in item.list" :key="index1">
							<view v-show="item1.image" @click="go_to(item1.url)" style="line-height: 1.5em;" class="padding-lr-sm" :style="item.stylea">
								<image
									:src="item1.image"
									mode="widthFix"
									:lazy-load="true"
									class="radius"
									:style="'border-radius:' + item.radius + 'px;' + 'background-color:' + item.bgcolor + ';'"
								></image>
								<view class="text-lg text-left text-cut padding-top" :style="item.styleb">{{ item1.title }}</view>
								<view v-show="item1.info" class="text-df text-left text-cut text-gray" :style="item.stylec">{{ item1.info }}</view>
								<view v-show="item1.price" class="text-lg text-left text-cut text-red padding-bottom" :style="item.styled">¥{{ item1.price }}</view>
							</view>
						</u-col>
					</u-row>
				</view>

				<view v-if="item.cate == 3">
					<u-row>
						<u-col span="4" v-for="(item1, index1) in item.list" :key="index1">
							<view v-show="item1.image" @click="go_to(item1.url)" style="line-height: 1.5em;" class="padding-lr-sm" :style="item.stylea">
								<image
									:src="item1.image"
									mode="widthFix"
									:lazy-load="true"
									class="radius"
									:style="'border-radius:' + item.radius + 'px;' + 'background-color:' + item.bgcolor + ';'"
								></image>
								<view class="text-lg text-left text-cut padding-top" :style="item.styleb">{{ item1.title }}</view>
								<view v-show="item1.info" class="text-df text-left text-cut text-gray" :style="item.stylec">{{ item1.info }}</view>
								<view v-show="item1.price" class="text-lg text-left text-cut text-red padding-bottom" :style="item.styled">¥{{ item1.price }}</view>
							</view>
						</u-col>
					</u-row>
				</view>
			</view>

			<!-- diy元素: 在线客服 -->
			<view v-if="item.type == 'online'">
				<view
					:style="'bottom:' + item.bottom + 'px' + ';' + 'right:' + item.right + 'px;background-color:' + item.bgColor + ';'"
					class="wode item-center"
					@tap="makePhoneCall(item.phone)"
				>
					<u-icon name="server-fill" :color="item.color" size="56"></u-icon>
				</view>
			</view>
		</view>
		
		
		<nav-bar :index="memu_id" :tabbar="true" :name="homelist.title"></nav-bar>
	</view>
</template>

<script>
export default {
	data() {
		return {
			homelist: {},
			memu_id:1
		};
	},
	onShareTimeline(){
		return{
			title: this.homelist.share_title,
			query: `login=${uni.getStorageSync('sharelogin')}`,
			imageUrl:this.homelist.imageurl
		}
	},
	onShareAppMessage(res) {
		return {
			title: this.homelist.share_title,
			path: '/' + this.$scope.route + '?login=' + uni.getStorageSync('sharelogin'),
			imageUrl: this.homelist.imageurl
		};
	},
	onLoad(options) {
		console.log(options)
		options.memu_id ? this.memu_id=options.memu_id : 1;
		options.login ? uni.setStorageSync('sharelogin', options.login) : {};
		this.homeUrl();
	},
	onshow(options){
		
	},
	methods: {
		homeUrl() {
			this.$http
				.get('home', {
					
				})
				.then(res => {
					if (res.data.code == 1) {
						
						this.homelist = res.data.list;
						uni.setStorageSync('home', {
							title: res.data.list.title,
							img: res.data.list.imageurl
						})
						uni.setNavigationBarTitle({
						    title: this.homelist.title
						});
						uni.setNavigationBarColor({
						    frontColor: this.homelist.frontcolor,
						    backgroundColor: this.homelist.bgcolor,
						    animation: {
						        duration: 400,
						        timingFunc: 'easeIn'
						    }
						});
					}
				});
		},
		navTo(e) {
			uni.redirectTo({
				url: e
			});
		},
		swiper_click(index,sindex) {
			// console.log(sindex);
			// console.log(index);
			// this.$u.toast('轮播点击跳转：' + this.homelist['list'][index].list[sindex].url);
			this.navTo(this.homelist['list'][sindex].list[index].url);
		},
		swiper_change(index) {
			// console.log(index);
			// this.swiper_index = index;
		},
		search_change(value) {
			// 搜索框内容变化时，会触发此事件，value值为输入框的内容
			//console.log(value);
		},
		search_custom(value) {
			//console.log(value);
			// this.$u.toast('输入值为：' + value);
			this.navTo('/pages/prodList/prodList?keywords='+value);
		},
		search_search(value) {
			// this.$u.toast('搜索内容为：' + value);
		},
		search_clear() {
			// console.log(this.value);
		},
		go_to(url) {
			// this.$u.toast('跳转链接为：' + url);
			uni.redirectTo({
				url: url
			});
		},
		notice_close() {
			// this.$u.toast(`点击了关闭图标`);
		},
		notice_click(index) {
			// if (this.mode == 'horizontal' && this.isCircular) {
			// 	this.$u.toast('此模式无法获取Index值');
			// } else {
			// 	this.$u.toast(`点击了第${index + 1}条消息`);
			// }
		},
		notice_getMore() {
			// this.$u.toast(`点击了更多图标`);
		},
		notice_end() {
			// console.log('end');
		},
		makePhoneCall(phone) {
			uni.makePhoneCall({
				phoneNumber: phone
			});
		},
	}
};
</script>

<style lang="scss">
/* #ifndef APP-NVUE */
view,
text {
	box-sizing: border-box;
}
/* #endif */
.u-config-title {
	text-align: center;
	font-size: 16px;
	font-weight: bold;
	margin-bottom: 20px;
}

.u-config-item {
	margin-top: 25px;
}
.u-config-wrap {
	padding-left: 8px;
	padding-right: 1px;
}
.u-config-title {
	margin-top: 20px;
	padding-bottom: 5px;
}

.u-item-title {
	position: relative;
	font-size: 15px;
	padding-left: 8px;
	line-height: 1;
	margin-bottom: 11px;
}

.u-item-more {
	position: relative;
	font-size: 14px;
	padding-left: 8px;
	line-height: 1;
	margin-bottom: 11px;
}

.u-item-title:after {
	position: absolute;
	width: 4px;
	top: -1px;
	height: 16px;
	content: '';
	left: 0;
	border-radius: 10px;
	background-color: #007aff;
}

.wode {
	position: fixed;
	bottom: 150rpx;
	right: 50rpx;
	width: 100rpx;
	height: 100rpx;
	padding: 0;
	text-align: center;
	line-height: 100rpx;
	margin: 0;
	border-radius: 50%;
	z-index: 999;
	opacity: 0.9;
}
</style>
