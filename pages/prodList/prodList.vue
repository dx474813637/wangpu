<template>
	<view class="w">
		<u-sticky bg-color="transparent">
			<view class="u-search-box">
				<u-search v-model="term" :show-action="false" action-text="搜索" :animation="true" @search="handleSearch">
				</u-search>
			</view>
			<view class="list-sticky">
				<view class="list-nav">
					<view class="nav" :class="{active: navActive == 0}" @click="handleChangeNav(0)">
						<view>综合排序</view>
					</view>
					<!-- <view class="nav" style="display: none;" :class="{active: navActive == 1}" @click="handleChangeNav(1)"><view>销量优先</view></view>
						<view class="nav" style="display: none;" :class="{active: navActive == 2}" @click="handleChangeNav(2)">
							<view>价格</view>
							<view class="sort">
								<view class="sort-up" :class="{active: priceSort == 'up'}">
									<text class="yticon icon-shang"></text>
								</view>
								<view class="sort-down" :class="{active: priceSort == 'down'}">
									<text class="yticon icon-xia"></text>
								</view>
							</view>
						</view> -->
				</view>
				<view class="list-type">
					<view class="view-type-btn" @click="handleChangeViewType">
						<view v-show="listType == 'dot'">
							<u-icon size="32" name="list" custom-prefix="custom-icon"></u-icon>
						</view>
						<view v-show="listType == 'grid'">
							<u-icon size="32" name="grid" custom-prefix="custom-icon"></u-icon>
						</view>

					</view>
				</view>
			</view>
		</u-sticky>
		<!-- <u-sticky
			bg-color="transparent"
		> -->

		<!-- </u-sticky> -->
		<view class="list">
			<list :list="list" :loadStatus="loadStatus" :listType="listType" emptyText="列表为空" emptyMode="list"
				@goto="handlePathDetail">
				<template v-slot:dot="{item}">
					<CardProductCell :list="item"></CardProductCell>
				</template>
				<template v-slot:grid="{item}">
					<view class="grid-card">
						<view class="card-img-w">
							<view class="card-img">
								<u-image width="100%" height="100%" :src="item.pic1"></u-image>
							</view>

						</view>
						<view class="card-info">
							<view class="info-title">{{item.name}}</view>
							<view class="info-sub">库存:{{item.stock}}{{item.unit}}</view>
							<view class="info-price">
								<!-- <view class="sold-num">库存:{{item.stock}}{{item.unit}}</view> -->
								<view class="price item price-red">
									<template v-if="item.price == '议价' ">
										<text class="price-yj">议价</text>
									</template>
									<template v-else>
										<text class="cheap-price-dw">¥</text>
										<text class="price-big">{{item.price}}</text>
									</template>
									<!-- <text class="cheap-price-dw">券后价</text> -->
								</view>
								<view class="in-cart-btn" @click.stop="inCart(item)">
									<u-icon name="plus"></u-icon>
								</view>
							</view>
						</view>
					</view>
				</template>
			</list>

		</view>
		<nav-bar :index="2" :tabbar="true"></nav-bar>
	</view>

</template>

<script>
	import {
		List
	} from '@/components/list/list.vue';
	import {
		CardProductCell
	} from '@/components/CardProductCell/CardProductCell.vue'

	import mixShareInfo from '@/utils/mixShareInfo.js'
	export default {
		mixins: [mixShareInfo],
		data() {
			return {
				list: [],
				loadStatus: 'loadmore',
				p: 1,
				endFlag: false,
				listType: 'grid',
				priceSort: 'up',
				navActive: 0,
				term: '',
				tags: '',
				shareOptions: {
					pageName: '产品列表'
				}
			}
		},
		components: {
			List,
			CardProductCell
		},
		onShareTimeline() {
			return {
				title: this.shareOptions.pageName,
				query: {}
			}
		},
		onShareAppMessage(res) {
			return {
				title: this.shareOptions.pageName,
				path: '/' + this.$scope.route
			};
		},
		async onLoad(options) {
			console.log(options)
			if (options && options.tags) {
				this.tags = options.tags
			} else if (options && options.term || options.keywords) {
				this.term = options.term || options.keywords
			}
			if (this.tags || this.term) {
				uni.setNavigationBarTitle({
					title: (this.tags || this.term) + '-产品列表'
				})
			}

			uni.showLoading({
				title: '正在加载...'
			})
			await this.getData()
		},
		onReachBottom() {
			this.getNextData()
		},
		async onPullDownRefresh() {
			this.initData()
			await this.getData()
			uni.stopPullDownRefresh()
		},
		watch: {
			navActive(newV) {
				if (newV == 0) {
					console.log('综合')
					this.initData()
					this.getData()
				} else if (newV == 1) {
					console.log('销售')
				} else if (newV == 2) {
					console.log('价格 ' + this.priceSort)
				}
			},
			priceSort(newV) {
				console.log(newV)
			}
		},
		methods: {

			async handleSearch() {
				this.tag = ''
				this.initData()
				await this.getData()
				uni.setNavigationBarTitle({
					title: this.term ? this.term + '-产品列表' : '产品列表'
				})
			},
			initData() {
				this.list = []
				this.p = 1
				this.endFlag = false
				this.loadStatus = 'loadmore'
			},
			getNextData() {
				if (this.endFlag) return
				this.p = this.p + 1
				this.getData()
			},
			async getData(search) {
				this.loadStatus = 'loading'
				let list = await this.$http.get('/product_list', {
					params: {
						p: this.p,
						term: this.term,
						tags: this.tags,
					}
				})
				this.shareOptions.pageName = list.data.share_title
				this.list = [...this.list, ...list.data.list.list_products]
				if (this.p == list.data.list.pw_page_total) {
					this.loadStatus = 'nomore'
					this.endFlag = 'true'
				} else this.loadStatus = 'loadmore'
				uni.hideLoading()


			},
			handleChangeViewType() {
				if (this.listType == 'dot') this.listType = 'grid'
				else this.listType = 'dot'
			},
			handleChangeNav(index) {
				if (this.navActive == index && index == 2) {
					this.handleChangeSort('priceSort')
					return
				}
				if (this.navActive == index) return
				this.navActive = index
			},
			handleChangeSort(sortKey) {
				if (this[sortKey] == 'up') this[sortKey] = 'down'
				else this[sortKey] = 'up'
			},
			handlePathDetail(obj) {
				uni.navigateTo({
					url: `/pages/prodDetail/prodDetail?id=${obj.id}`,
				})
			},
			inCart(item) {
				this.$store.dispatch('addCart', item)
			}
		}
	}
</script>
<style>
	page {
		background-color: #f5f5f5;
	}
</style>
<style scoped lang="scss">
	.u-search-box {
		padding: 10rpx 20rpx;
		background-color: #fff;
	}

	.list-sticky {
		// position: fixed;
		// left: 0;
		// top: 0;
		width: 100%;
		display: flex;
		justify-content: space-between;
		padding: 0 10rpx 10rpx;
		height: 60rpx;
		align-items: center;
		background-color: #fff;
		color: #999;
		z-index: 50;

		.list-nav {
			display: flex;
			align-items: center;

			.nav {
				padding: 0 15rpx;
				// font-size: 26rpx;
				display: flex;
				align-items: center;
				margin-right: 30rpx;

				&:last-child {
					margin-right: 0;
				}

				&.active {
					color: red;
				}

				.sort {
					display: flex;
					flex-direction: column;
					height: 38rpx;
					margin-left: 6rpx;
					position: relative;

					.sort-up,
					.sort-down {
						height: 19rpx;
						display: flex;
						color: #bbb;

						.yticon {
							font-size: 24rpx;
						}

					}
				}

				&.active {

					.sort-up,
					.sort-down {
						&.active {
							color: red;
						}
					}
				}
			}
		}

		.list-type {
			padding-right: 20rpx;
			height: 100%;
			display: flex;
			align-items: center;

			.view-type-btn {}
		}
	}

	.list {

		padding: 10rpx 15rpx 15rpx;

		.grid-card {
			margin-bottom: 20rpx;
			width: 100%;
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
			font-family: '微软雅黑';
			margin-right: 4rpx;
		}
			font-size: 1rem;
		.price-yj {
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
