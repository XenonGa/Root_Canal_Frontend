<template>
	<div class="addAdmin">
		<!-- <div class="layout-view-bg-white flex">
			<div class="flex-margin color-primary">personal</div>
		</div> -->
				<el-card shadow="hover">
					<div slot="header">
						<span>{{ $t('message.router.addAdmin') }}</span>
					</div>
					<el-table
					:data="tableData"
					style="width: 100%"
					max-height="1000">
					<el-table-column
					prop="id"
					label="序号"
					width="120">
					</el-table-column>
					<el-table-column
					prop="name"
					label="姓名"
					width="120">
					</el-table-column>
					<el-table-column
					prop="phone"
					label="手机号"
					width="300">
					</el-table-column>
					<el-table-column
					prop="mail"
					label="邮箱"
					width="300">
					</el-table-column>
					<el-table-column
					prop="isAdmin"
					label="身份"
					width="100"
					:filters="[{ text: '普通医师', value: '普通医师' }, { text: '管理医师', value: '管理医师' }]"
					:filter-method="filterTag"
					filter-placement="bottom-end">
					<template slot-scope="scope">
						<el-tag
						:type="scope.row.isAdmin === '管理医师' ? 'primary' : 'success'"
						disable-transitions>{{scope.row.isAdmin}}</el-tag>
					</template>
					</el-table-column>
					<el-table-column
					fixed="right"
					label="操作"
					width="160">
					<template slot-scope="scope">
						<el-button @click="setAsAdmin(scope.row)" type="text" size="small" v-if="scope.row.isAdmin === '普通医师'">设置为管理医师</el-button>
						<el-button @click="setAsAdmin(scope.row)" type="text" size="small" v-else disabled>设置为管理医师</el-button>
					</template>
					</el-table-column>
				</el-table>
				</el-card>
	</div>
</template>

<script>
import axios from "axios"
export default {
	name: 'addAdmin',
	data() {
		return {
			expireTime: 1,
			inviteCode: '',
			isGenerated: false,
			tableData: []
		};
	},
	created() {
		this.getDoctorInfo()
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
		getDoctorInfo() {
			let instance = axios.create({
				baseURL: "http://127.0.0.1:8000",
				timeout: 1000,
			})
			instance.post("/api/user/get_all_doctors").then(async res=>{
				console.log(res)
				for(var i = 0; i < res.data.doctors_info.length; i++) {
					var tag = ''
					if(res.data.doctors_info[i].doctor_is_admin) {
						tag = '管理医师'
					}
					else {
						tag = '普通医师'
					}
					this.tableData.push({
						id: res.data.doctors_info[i].doctor_id,
						name: res.data.doctors_info[i].doctor_name,
						phone: res.data.doctors_info[i].doctor_phone,
						mail: res.data.doctors_info[i].doctor_mail,
						isAdmin: tag
					})
				}
			});
		},
		setAsAdmin(row) {
			let instance = axios.create({
				baseURL: "http://127.0.0.1:8000",
				timeout: 1000,
			})
			instance.post("/api/user/make_doctor_admin",{
				is_admin: true,
				doctor_id: row.id
			}).then(async res=>{
				console.log(res)
				this.$message({
					showClose: true,
					message: res.data.msg,
					type: 'success'
				});
				row.isAdmin = '管理医师'
			});
		}
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
.mb15 {
	width: 80%;
}
</style>
