<template>
	<div class="home">
		<!-- 用户信息 -->
		<el-row :gutter="15">
			<el-col :md="24" :lg="16" :xl="16" class="mb15">
				<el-card shadow="hover">
					<div slot="header">
						<span>{{ $t('message.card.title1') }}</span>
					</div>
					<div class="user-item">
						<div class="user-item-left">
							<img :src="getUserInfos.photo" />
						</div>
						<div class="user-item-right overflow">
							<el-row>
								<el-col :span="24" class="right-title mb15 one-text-overflow"
									>{{ currentTime }}，{{ getUserInfos.userName }}，{{ dailyMessage }}
								</el-col>
								<el-col :span="24">
									<el-col :xs="12" :sm="12" :md="8" class="right-l-v">
										<div class="right-label">姓名：</div>
										<div class="right-value">{{ getUserInfos.userName }}</div>
									</el-col>
									<el-col :xs="12" :sm="12" :md="16" class="right-l-v">
										<div class="right-label">身份：</div>
										<div class="right-value">{{ userInfo.isAdmin ? '管理医师' : '普通医师' }}</div>
									</el-col>
								</el-col>
								<!-- <el-col :span="24" class="mt5">
									<el-col :xs="12" :sm="12" :md="8" class="right-l-v">
										<div class="right-label one-text-overflow">IP：</div>
										<div class="right-value one-text-overflow">192.168.1.1</div>
									</el-col>
									<el-col :xs="12" :sm="12" :md="16" class="right-l-v">
										<div class="right-label one-text-overflow">时间：</div>
										<div class="right-value one-text-overflow">{{ userInfo.time }}</div>
									</el-col>
								</el-col> -->
								<!-- <el-col :span="24" class="mt15">
									<el-button size="small" icon="el-icon-edit-outline">修改信息 </el-button>
					
								</el-col> -->
							</el-row>
						</div>
					</div>
				</el-card>
			</el-col>
		</el-row>

		<!-- 推荐 -->
		<!-- <el-card shadow="hover">
			<div slot="header">
				<span>{{ $t('message.card.title4') }}</span>
				<el-button class="home-card-more" type="text" @click="onOpenGitee">{{ $t('message.card.title5') }}</el-button>
			</div>
			<el-row :gutter="15" class="home-recommend-row">
				<el-col :sm="24" :md="12" :lg="6" :xl="6" v-for="(v, k) in recommendList" :key="k">
					<div class="home-recommend" :style="{ 'background-color': v.bg }">
						<i :class="v.icon" :style="{ color: v.iconColor }"></i>
						<div class="home-recommend-auto">
							<div>{{ v.title }}</div>
							<div class="home-recommend-msg">{{ v.msg }}</div>
						</div>
					</div>
				</el-col>
			</el-row>
		</el-card> -->

		<!-- charts -->
		<!-- <el-row :gutter="15" class="mt15">
			<el-col :md="24" :lg="8" :xl="8" class="mb15">
				<el-card shadow="hover">
					<div slot="header">
						<span>{{ $t('message.card.title6') }}</span>
					</div>
					<div class="charts">
						<div class="charts-right">
							<div ref="homeStockRef" class="h100"></div>
						</div>
					</div>
				</el-card>
			</el-col>
			<el-col :md="24" :lg="16" :xl="16" class="mb15">
				<el-card shadow="hover">
					<div slot="header">
						<span>{{ $t('message.card.title7') }}</span>
					</div>
					<div class="charts">
						<div class="charts-left mr7">
							<div ref="homeLaboratoryRef" class="h100"></div>
						</div>
					</div>
				</el-card>
			</el-col>
		</el-row> -->

		<!-- 履约超时预警 -->
		<!-- <el-row :gutter="15">
			<el-col :md="24" :lg="16" :xl="16" class="home-lg">
				<el-card shadow="hover">
					<div slot="header">
						<span>{{ $t('message.card.title8') }}</span>
					</div>
					<div class="charts">
						<div class="charts-left mr7">
							<div ref="homeOvertimeRef" class="h100"></div>
						</div>
					</div>
				</el-card>
			</el-col>
			<el-col :md="24" :lg="8" :xl="8">
				<el-card shadow="hover">
					<div slot="header">
						<span>{{ $t('message.card.title9') }}</span>
					</div>
					<div class="home-charts">
						<div class="home-charts-item" v-for="(v, k) in chartsRightList" :key="k">
							<div class="home-charts-item-left">
								<div class="home-charts-item-title">{{ v.title }}</div>
								<div class="home-charts-item-num" :style="{ color: v.color }" :id="`titleNum${k + 1}`"></div>
							</div>
							<div class="home-charts-item-right">
								<i :class="v.icon" :style="{ 'background-color': v.iconBg, color: v.color }"></i>
							</div>
						</div>
					</div>
				</el-card>
			</el-col>
		</el-row> -->
	</div>
</template>

<script>
import * as echarts from 'echarts';
import Scroll from 'vue-seamless-scroll';
import { CountUp } from 'countup.js';
import { Session } from '@/utils/storage';
import { formatAxis, formatDate } from '@/utils/formatTime';
import { recommendList, chartsRightList, newsInfoList, dailyMessage } from './mock';
export default {
	name: 'home',
	components: { Scroll },
	data() {
		return {
			recommendList,
			chartsRightList,
			newsInfoList,
			userInfo: {},
			dailyMessage: {},
			charts: {
				theme: '',
				bgColor: '',
			},
			global: {
				homeChartOne: null,
				homeChartTwo: null,
				homeCharThree: null,
				dispose: [null, '', undefined],
			},
		};
	},
	created() {
		this.initUserInfo();
		this.initDailyMessage();
	},
	computed: {
		currentTime() {
			return formatAxis(new Date());
		},
		optionSingleHeight() {
			return {
				singleHeight: 28,
				limitMoveNum: 8,
				waitTime: 2000,
			};
		},
		getUserInfos() {
			return this.$store.state.userInfos.userInfos;
		},
	},
	mounted() {
		// this.initHomeStock();
		// this.initHomeLaboratory();
		// this.initHomeOvertime();
		// this.initNumCountUp();
	},
	methods: {
		// 初始化数字滚动
		// 随机语录
		initDailyMessage() {
			this.dailyMessage = dailyMessage[Math.floor(Math.random() * dailyMessage.length)];
		},
		// 初始化登录信息
		initUserInfo() {
			if (!Session.get('userInfo')) return false;
			this.userInfo = Session.get('userInfo');
			this.userInfo.time = formatDate(new Date(this.userInfo.time), 'YYYY-mm-dd HH:MM:SS');
		},
		// 消息通知当前项点击
		onNewsInfoListClick(v) {
			window.open(v.link);
		},
		// 跳转到 gitee
		onOpenGitee() {
			window.open('https://gitee.com/lyt-top/vue-next-admin');
		},
	},
};
</script>

<style scoped lang="scss">
@import './index.scss';
</style>
