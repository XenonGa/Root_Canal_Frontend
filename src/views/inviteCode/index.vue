<template>
	<div class="personal" :style="{ height: `calc(100vh - ${setMainHeight}` }">
		<!-- <div class="layout-view-bg-white flex">
			<div class="flex-margin color-primary">personal</div>
		</div> -->
		<el-row :gutter="15">
			<el-col :md="24" :lg="16" :xl="16" class="mb15">
				<el-card shadow="hover">
					<div slot="header">
						<span>{{ $t('message.router.inviteCodeView') }}</span>
					</div>
					<div class="user-item">
						
						<div class="user-item-right overflow">
							<el-row>
								<el-col :span="24" class="right-title mb15 one-text-overflow"
									>{{ $t('message.generateInviteCode.setTime') }}
								</el-col>
								<el-col :span="24" class="mt5">
									<el-input-number v-model="expireTime" :min="1" :max="500" label="邀请码有效时长"></el-input-number>
									<span style="margin-left: 30px; margin-right: 30px;">{{ $t('message.generateInviteCode.hour') }}</span>
									<el-button size="medium" icon="el-icon-key" @click="generateInviteCode">{{ $t('message.router.inviteCodeView') }}</el-button>
								</el-col>	
							</el-row>
							<div class="inviteCode" v-if="isGenerated">
								{{ inviteCode }}
							</div>
						</div>
					</div>
				</el-card>
			</el-col>
			
		</el-row>
	</div>
</template>

<script>
import axios from "axios"
export default {
	name: 'personal',
	data() {
		return {
			expireTime: 1,
			inviteCode: '',
			isGenerated: false
		};
	},
	created() {
		
	},
	computed: {
		// 设置主盒子高度
		setMainHeight() {
			let { isTagsview } = this.$store.state.themeConfig.themeConfig;
			if (isTagsview) return `114px`;
			else return `80px`;
		},
	},
	
	methods: {
		generateInviteCode() {
			let instance = axios.create({
				baseURL: "http://127.0.0.1:8000",
				timeout: 1000,
			})
			instance.post("/api/user/generate_invite_code", 
			{
				is_admin: true,
				expire_time: this.expireTime
			}).then(async res=>{
				console.log(res)
				this.isGenerated = true
				this.inviteCode = res.data.content
				this.$message({
					showClose: true,
					message: res.data.msg,
					type: 'success'
				});			
			});
		},
	}
};
</script>

<style scoped lang="scss">

@import './index.scss';

.inviteCode {
	margin-top: 20px;
	font-size: 20px;
	font-family:Georgia, 'Times New Roman', Times, serif;
	background-color: #2f4c41;
	color: #c6c6c6;
	padding: 10px;
	border-radius: 10px;
	width: 600px;
}
</style>
