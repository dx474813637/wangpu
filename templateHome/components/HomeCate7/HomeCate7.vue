<template>
	<view class="homebg">
		<!-- <template v-if="list.notice.length != 0">
			<u-notice-bar
				type="primary"
				:list="list.notice"
				:moreIcon="false"
			></u-notice-bar>
		</template> -->
		<template v-if="list.online.phone != 0">
			<view class="float-tools" >
				<view class="service-wrapper" @tap="makePhoneCall(list.online.phone)">
					<u-icon name="server-fill" size="38"></u-icon>
				</view>
			</view>
		</template>
		<!-- <view class="header u-margin-bottom-30 u-padding-l-r">

			<view class="search">
				<u-search shape="round" bg-color="#f8f8f8" height="60" color="#000" :show-action="false"
					v-model="searchKw" @search="handlersearch">
				</u-search>
			</view>
			<view class="service-btn" @tap="makePhoneCall(list.online.phone)" v-if="list.online.phone" >
				<u-icon name="server-fill" size="38"></u-icon>
			</view>

		</view> -->
		<view class="swiper u-margin-bottom-20">
			<u-swiper 
			height="900"
			:list="list.swiper"
			@click="swiper_click"
			bg-color="transparent"
			border-radius="0"
			mode="dot"
		></u-swiper>
		</view>
		<view class="nav-wrapper u-padding-l-r u-margin-bottom-20" v-if="list.navbar.length != 0">
			<view class="nav">
				<view class="item-nav" v-for="(ele, i) in list.navbar" :key="i" @click="navTo(ele.url)">
					<image class="nav-icon" :src="ele.image" />
					<view class="nav-title">{{ele.name}}</view>
				</view>
			</view>

		</view>
		
		<image 
			class="bigtitle"
			mode="widthFix"
			v-if="list.images[0]"
			:src="list.images[0].image"  
			@click="navTo(list.images[0].url)"
		/>
		<image 
			class="bigtitle u-padding-left-10 u-padding-right-10"
			mode="widthFix"
			v-if="list.images[1]"
			:src="list.images[1].image"  
			@click="navTo(list.images[1].url)"
		/>
		<view class="img-grid u-padding-left-5 u-padding-right-5"
			v-if="list.images[2] || list.images[3] || list.images[4]">
			<view class="item-grid img-left u-padding-left-5 u-padding-right-5">
				<image
					class="bigtitle"
					mode="scaleToFill"
					:src="list.images[2].image"  
					@click="navTo(list.images[2].url)"
				/>
			</view>
			<view class="item-grid img-right u-padding-5">
				<image
					class="bigtitle u-padding-left-5 u-padding-right-5 u-padding-bottom-5"
					mode="scaleToFill"
					:src="list.images[3].image"  
					@click="navTo(list.images[3].url)"
				/>
				
				<image
					class="bigtitle u-padding-left-5 u-padding-right-5 u-padding-top-5"
					mode="scaleToFill"
					:src="list.images[4].image"  
					@click="navTo(list.images[4].url)"
				/>
			</view>
		</view>
		
		<image 
			class="bigtitle"
			mode="widthFix"
			v-if="list.images[5]"
			:src="list.images[5].image"  
			@click="navTo(list.images[5].url)"
		/>
		
		<image 
			class="bigtitle"
			mode="widthFix"
			:src="list.images[6].image"  
			@click="navTo(list.images[6].url)"
		/>
		<view class="product-wrapper" v-if="list.product.length != 0">
			
			
			<view class="wrapper-list">
				<view class="grid-card" v-for="(item, index) in list.product" :key="index" @click="navTo(item.url)">
					<view class="card-img-w">
						<view class="card-img">
							<u-image width="100%" height="100%" :src="item.image"></u-image>
						</view>
					</view>
					<view class="card-info">
						<view class="info-title">{{item.title}}</view>
						<view class="info-sub">{{item.info}}</view>
						<view class="info-price">
							<view class="price item price-red">
								<text class="cheap-price-dw">??</text>
								<text class="price-big item">{{item.price}}</text>
							</view>
							<view class="in-cart-btn">
								<u-icon name="more-dot-fill"></u-icon>
							</view>
						</view>
					</view>
				</view>
			</view>
		</view>
		
		<view class="news-wrapper u-margin-bottom-40" v-if="list.news.length != 0">
			<view class="wrapper-title">
				<view class="wrapper-left">
					<u-icon name="list-dot" color="#007aff" size="34"></u-icon>
					<view class="">????????????</view>
				</view>
				<view class="wrapper-right" @click="navTo('/pages/news/newsList')">
					<view class="">??????</view>
					<u-icon name="arrow-right-double" size="30"></u-icon>
				</view>
				
			</view>
			<view class="wrapper-news-list">
				<template v-for="(item, index) in list.news">
					<NewsCell
						:title="item.title"
						:image="item.image"
						:url="item.url"
						:posttime="item.ctime"
					></NewsCell>
				</template>
			</view>
		</view>
	</view>
</template>

<script>
	import {
		mapState,
		mapMutations
	} from "vuex"
	import {
		CardProductCell
	} from '@/components/CardProductCell/CardProductCell';
	import {
		NewsCell
	} from '@/components/NewsCell/NewsCell';
	export default {
		name: "HomeCate7",
		props: {
			list: {
				type: Object,
				default: () => {
					return {}
				}
			}
		},
		data() {
			return {
				searchKw: "",
			};
		},
		components: {
			CardProductCell,
			NewsCell,
		},
		computed: {
			...mapState(['theme']),
		},
		onReady() {
			uni.setNavigationBarColor({
				frontColor: "#000000",
				backgroundColor: "#ffffff",
				animation: {
					duration: 400,
					timingFunc: 'easeIn'
				}
			});
		},
		methods: {
			...mapMutations(['changeVoteTheme']),

			swiper_click(index) {
				this.navTo(this.list.swiper[index].url);
				
			},
			makePhoneCall(phone) {
				uni.makePhoneCall({
					phoneNumber: phone+""
				});
			},
			navTo(e) {
				uni.navigateTo({
					url: e
				});
			},
			handlersearch(value) {
				this.navTo('/pages/prodList/prodList?keywords=' + value);
			},
			inCart() {
				this.$store.dispatch('addCart', this.list)
			}
		}
	}
</script>

<style lang="scss" scoped>
	.homebg {
		background-color: #fff;
	}
	.float-tools {
		position: fixed;
		bottom: 160rpx;
		right: 20rpx;
		z-index: 50;
		.service-wrapper {
			width: 80rpx;
			height: 80rpx;
			border-radius: 50%;
			background-color: #fff;
			box-shadow: 0 0 10rpx rgba(0,0,0,0.1);
			display: flex;
			justify-content: center;
			align-items: center;
			border: 1rpx solid #e7e7e7;
		}
	}
	.img-grid {
		display: flex;
		height: 550rpx;
		.item-grid {
			width: 50%;
			image {
				height: 550rpx;
			}
			&.img-right {
				image {
					height: 275rpx;
				}
			}
		}
	}
	.bigtitle {
		width: 100%;
		height: auto;
	}
	.bg {
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		height: 260rpx;

		image {
			position: absolute;
			left: 0;
			bottom: 0;
			width: 100%;
			height: 30rpx;
		}
	}

	.boxShadow {
		box-shadow: 0 0px 10rpx rgba(0, 0, 0, .1);
	}

	.u-padding-l-r {
		padding: 0 25rpx;
	}

	.header {
		display: flex;
		align-items: center;
		padding-top: 20rpx;
		.search {
			position: relative;
			z-index: 20;
			flex: 1;
		}

		.service-btn {
			flex: 0 0 100rpx;
			width: 100rpx;
			display: flex;
			align-items: center;
			padding-right: 10rpx;
			justify-content: flex-end;
		}
	}


	.swiper {
		position: relative;
		z-index: 20;
		// margin: 0 0rpx 20rpx;

	}
	.wrapper-news-list {
		padding: 0 20rpx;
	}
	.nav {
		background-color: #ebebeb;
		border-radius: 8rpx;
		display: flex;
		flex-wrap: wrap;
		padding: 10rpx 0;

		.item-nav {
			flex: 0 0 20%;
			display: flex;
			flex-direction: column;
			align-items: center;
			padding: 14rpx 0;

			.nav-icon-wrapper {
				// width: 70rpx;
				// height: 70rpx;
				display: flex;
				justify-content: center;
				align-items: center;
				margin-bottom: 16rpx;
			}

			.nav-icon {
				width: 70rpx;
				height: 70rpx;
				margin-bottom: 14rpx;
				border-radius: 10rpx;
			}

			.nav-title {
				color: #000;
				font-size: 26rpx;
			}
		}
	}

	.service {
		background-color: #fff;
		border-radius: 8rpx;
		padding: 10rpx 0 10rpx;

		.service-top {
			padding: 0 20rpx;
			display: flex;
			align-items: center;
			height: 140rpx;
		}

		.service-bottom {
			padding: 0 15rpx 5rpx 15rpx;
		}

		.item-avatar {
			flex: 0 0 80rpx;
			width: 80rpx;
			height: 80rpx;
			background-color: #f8f8f8;
			border-radius: 6rpx;
			display: flex;
			justify-content: center;
			align-items: center;
			margin-right: 20rpx;
		}

		.item-desc {
			flex: 1;
			color: #999;
			font-size: 24rpx;

			.title {
				color: #666;
				font-size: 30rpx;
				margin-bottom: 10rpx;
			}
		}

		.item-func {
			flex: 0 0 70rpx;
			width: 70rpx;
			height: 70rpx;
			border: 1rpx solid #007aff;
			border-radius: 50%;
			display: flex;
			justify-content: center;
			align-items: center;

		}

	}
	.wrapper-title {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 0 30rpx;
		height: 90rpx;
		border-top: 1rpx solid #f8f8f8;
		border-bottom: 1rpx solid #f8f8f8;

		.wrapper-left {
			display: flex;
			color: #000;
			font-size: 30rpx;
			font-weight: bold;

			view {
				margin-left: 6rpx;
			}
		}

		.wrapper-right {
			display: flex;
			color: #999;
			font-size: 24rpx;

			view {
				margin-right: 6rpx;
			}
		}
	}

	.product-wrapper {
		background-color: #fff;
		margin-top: -100rpx;
		padding: 0 20rpx;
		
		.wrapper-list {
			padding: 10rpx;
			display: flex;
			border-radius: 10rpx;
			flex-wrap: wrap;
			background-color: #fff;
			position: relative;
			z-index: 20;
		}

	}

	.grid-card {
		margin-bottom: 20rpx;
		flex: 0 0 50%;
		width: 50%;
		background-color: #fff;
		overflow: hidden;
		border-radius: 20rpx;
		padding: 10rpx;

		.card-img-w {
			position: relative;
			width: 100%;
			padding-top: 100%;
			border-radius: 14rpx;
			overflow: hidden;

			&::after {
				content: '';
				position: absolute;
				top: 0;
				left: 0;
				width: 100%;
				height: 100%;
				background-color: rgba(0, 0, 0, .1);
				z-index: 20;
			}

			.card-img {
				position: absolute;
				top: 0;
				left: 0;
				width: 100%;
				height: 100%;
			}
		}

		.card-info {
			padding: 10rpx 6rpx;

			.info-title {
				overflow: hidden;
				text-overflow: ellipsis;
				white-space: nowrap;
				padding-bottom: 10rpx;
				font-size: 30rpx;
			}

			.info-sub {
				color: #ea8d00;
				font-size: 24rpx;
				overflow: hidden;
				text-overflow: ellipsis;
				white-space: nowrap;
			}

			.info-price {
				display: flex;

				justify-content: space-between;

				.in-cart-btn {
					margin-left: 10rpx;
					height: 45rpx;
					background-color: #aa55ff;
					display: flex;
					align-items: center;
					justify-content: center;
					width: 45rpx;
					color: #fff;
					border-radius: 50%;
				}
			}
		}
	}

	.info-price {
		display: flex;
		align-items: flex-end;
		height: 60rpx;
		font-size: 24rpx;
		// flex-wrap: wrap;
		color: #999;

		.price {
			padding: 0 14rpx;
			border-radius: 8rpx;
		}

		.item {
			margin-right: 0rpx;
		}

		.price-red {
			color: #fff;
			background-color: red;
			background: linear-gradient(to right, #aa55ff, #ff00b3);
		}

		.price-big {
			font-size: 1.1rem;
		}

		.cheap-price-dw {
			// color: red;
			font-family: '????????????';
			margin-right: 4rpx;
		}

		.cheap-price {
			// font-size: 34rpx;
			// font-weight: bold;
		}

		.cheap-price-label {
			margin-left: 6rpx;
			font-size: 24rpx;
		}

		.sold-num {
			overflow: hidden;
			white-space: nowrap;
			text-overflow: ellipsis;
		}
	}
</style>
