<template>
	<view class="content">
		<view class="head-box">
			<view>
				<!--<button @click="showActionSheet">{{(currentRoom && currentRoom.courtName) || '未关联房间'}}</button>-->
				<change-room-btn :readOnly="true"></change-room-btn>
			</view>
		</view>
		<view class="content">
			<view class="common-repair-card-item" v-for="(item, index) in listModal" :key="index">
				<view class="title">
					<image mode="aspectFit" class="img" src="../../static/img/homeHL.png" alt=""/>
					<text>{{item.roomName}}</text>
				</view>
				<view class="content-box">
					<view class="head">
						<view>【{{servicesTypeEnum[item.servicesType]}}】 {{getTime(item.createDate)}}</view>
						<view class="btn-text">{{getStatus(item.state)}}></view>
					</view>
					<view class="body">
						<view class="content-info">{{item.contentInfo}}</view>
						<view v-if="item.servicePhotos && item.servicePhotos.length">
							<image @click="lookImg(index,item.servicePhotos)" v-for="(img, index) in item.servicePhotos" :key="item" mode="aspectFit" class="img" :src="getSrc(img)" alt=""/>
						</view>
					</view>
					<view class="foot">
						<view></view>
						<view class="right">
						</view>
					</view>
				</view>
			</view>
			<view class="comment-box">
				<view class="rate-item">
					<text class="label">整体评价</text>
					<uni-rate v-model="value1"/>
				</view>
				<view class="area-box">
					<textarea placeholder="可输入300个字符"
							  :maxlength="300"
							  @blur="bindTextAreaBlur"
							  v-model="textareaValue"/>
				</view>
				<view class="rate-item">
					<text class="label">上门时间</text>
					<uni-rate v-model="value2"/>
				</view>
				<view class="rate-item">
					<text class="label">服务态度</text>
					<uni-rate v-model="value3"/>
				</view>
				<view class="rate-item">
					<text class="label">服务质量</text>
					<uni-rate v-model="value4"/>
				</view>
			</view>
			<view>
				<button type="primary" class="primary submit-btn" @tap="submit">提交</button>
			</view>
		</view>
	</view>
</template>

<script>
	import * as api from '../../api/api'
	import * as common from '../../common/common'
	import * as util from '../../common/util'
	import UniSegmentedControl from '../../components/uni-segmented-control/uni-segmented-control'
	import UniList from '../../components/uni-list/uni-list'
	import UniListItem from '../../components/uni-list-item/uni-list-item'
	import NoData from '../../components/my-components/no-data'
	import ChangeRoomBtn from '../../common/change-room-btn'
	import {uniRate} from '@dcloudio/uni-ui'
	import {
		mapState,
		mapMutations
	} from 'vuex'

	export default {
		components: {
			UniSegmentedControl,
			UniList,
			UniListItem,
			ChangeRoomBtn,
			NoData,
			uniRate
		},
		computed: {
			...mapState(['primaryColor', 'serviceTypeList', 'hasLogin', 'userName', 'roomList', 'currentRoom']),
			listModal() {
				if(this.dataList && this.dataList.length) {
					return this.dataList
				}
				return []
			},
			items () {
				// （0＝待受理，1＝处理中，2＝待确认，3 4 7＝已完成(3是用户完成 4是已经评价 7是物业完成)，5＝已退回，6＝已取消）
				// -1-全部,10-已完成,0-待处理,1-处理中,2-待确认,5-已退回,6-已取消
				return [
					{
						label: '全部',
						value: '',
						state: '',
						isShow: true
					},
					{
						label: '待处理',
						state: 0,
						isShow: true
					},
					{
						label: '处理中',
						state: 1,
						isShow: true
					},
					{
						label: '待确认',
						state: 2,
						isShow: true
					},
					{
						label: '已完成',
						state: 10,
						isShow: true
					},
					{
						label: '已退回',
						state: 5,
						isShow: false
					},
					{
						label: '已取消',
						state: 6,
						isShow: false
					},
					{
						label: '待评价',
						state: 3,
						isShow: false
					},
					{
						label: '已评价',
						state: 4,
						isShow: false
					},
					{
						label: '物业完成',
						state: 7,
						isShow: false
					}
				]
			}
		},
		watch: {
			currentRoom () {
			}
		},
		data() {
			return {
				servicesTypeEnum:{
					1: '报修',
					2: '投诉'
				},
				serviceId: '',
				dataList: [],
				value1: 5,
				value2: 5,
				value3: 5,
				value4: 5,
				textareaValue: ''
			}
		},
		methods: {
			...mapMutations(['setStateData']),
			bindTextAreaBlur () {
				console.log(this.textareaValue)
			},
			getSrc(src) {
				return `${this.$filePrefix}/${src.slice(1)}`
			},
			getTime (e) {
				return util.dateFormat(new Date(e).getTime())
			},
			getTime2 (e) {
				return util.dateFormat(new Date(e).getTime(),'yyyy-MM-dd hh:mm')
			},
			getStatus(state) {
				let item = this.items.find(e => {
					return e.state === state
				})
				return item && item.label
			},
			lookImg (index,list) {
				uni.previewImage({
					current: this.getSrc(list[index]),
					urls: list.map(e => this.getSrc(e))
				})
			},
			async getServiceGetDetail () {
				let res = await api.getServiceGetDetail({
					serviceId: this.serviceId
				})
				if(res && res.success) {
					this.dataList = res.data?[res.data]:[]
					console.log(this.dataList)
					console.log(this.listModal)
				}
			},
			async submit () {
				let item = this.dataList[0] || {}
				let res = await api.setServiceStatus({
					courtId: this.currentRoom.courtId,
					stateId: item.state,
					serviceId: item.serviceId,
					contentInfo: this.textareaValue,
					changeStateId: 4,
					starLevel: this.value1,
					arrivalTime: this.value2,
					serviceAttitude: this.value3,
					serviceQuality: this.value4
				})
				if(res && res.success) {
					uni.showToast({
						icon: 'none',
						title: '提交成功！',
						duration: 3000
					});
				}
				uni.navigateBack({
					delta: 1
				});
			}
		},
		onLoad(e) {
			this.serviceId = e.serviceId
			this.getServiceGetDetail()
		}
	}
</script>

<style scoped lang="scss">
	.head-box {
		background-color: #fff;
	}
	.list-right-box{
		display: flex;
		align-items: center;
		color: $uni-color-subtitle;
		.dot{
			color: $uni-color-primary;
			font-size: 70px;
			height: 8px;
			margin-left: 5px;
			line-height: 0;
		}
	}
	.card-item{
		.title{
			display: flex;
			line-height: 32px;
			.img{
				width: 30px;
				height: 30px;
			}
			align-items: center;
		}
		.content-box{
			background-color: #fff;
			padding: 5px;
			.head{
				line-height: 50px;
				border-bottom: 1px solid $uni-border-color;
				display: flex;
				justify-content: space-between;
				.btn-text{
					color: $uni-color-primary;
				}
			}
			.body{
				padding: 5px;
				.img{
					width: 80px;
					height: 80px;
				}
			}
			.foot{
				padding: 5px 0;
				border-top: 1px solid $uni-border-color;
				display: flex;
				justify-content: space-between;
				.right{
					display: flex;
				}
				.btn{
					width: 100px;
					margin: 0;
					margin-left: 10px;
					font-size: 14px;
					color: #fff;
				}
			}

		}
	}
	 .comment-box{
		 margin-top: 20px;
		 background-color: #fff;
		 padding: 10px;
		 .rate-item {
			 display: flex;
			 line-height: 40px;
			 align-items: center;
			 .label {
				 margin-right: 10px;
			 }
		 }
		 .area-box {
			 display: block;
			 textarea {
				 padding: 5px;
				 width: 100%;
				 display: block;
				 height: 120px;
				 border-radius: 10px;
				 border: 1px solid $uni-border-color;
			 }
		 }
	 }
	.submit-btn {
		margin: 10px;
	}
</style>
