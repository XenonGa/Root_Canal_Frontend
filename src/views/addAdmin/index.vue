<template>
	<div class="addAdmin">
		<!-- <div class="layout-view-bg-white flex">
			<div class="flex-margin color-primary">personal</div>
		</div> -->
		<el-card shadow="hover">
			<div slot="header" class="admin-header">
				<div style="margin-top: 10px;">{{ $t('message.router.addAdmin') }}</div>
				<el-button type="primary" round @click="dialogFormVisible = true">新增医师</el-button>
			</div>
			<el-table :data="tableData" style="width: 100%" max-height="1000">
				<el-table-column prop="id" label="序号" width="150">
				</el-table-column>
				<el-table-column prop="name" label="姓名" width="150">
				</el-table-column>
				<el-table-column prop="phone" label="手机号" width="300">
				</el-table-column>
				<el-table-column prop="mail" label="邮箱" width="300">
				</el-table-column>
				<el-table-column prop="isAdmin" label="身份" width="200">
				</el-table-column>
				<!-- <el-table-column prop="isAdmin" label="身份" width="200"
					:filters="[{ text: '普通医师', value: '普通医师' }, { text: '管理医师', value: '管理医师' }]"
					:filter-method="filterTag" filter-placement="bottom-end">
					<template slot-scope="scope">
						<el-tag effect="plain" :type="scope.row.isAdmin === '管理医师' ? 'warning' : 'success'" disable-transitions>{{
					scope.row.isAdmin }}</el-tag>
					</template>
				</el-table-column> -->
				<el-table-column fixed="right" label="操作" width="300">
					<template slot-scope="scope">
						<el-button @click="setAsAdmin(scope.row)" size="small"
							v-if="scope.row.isAdmin === '普通医师'">设置为管理医师</el-button>
						<el-button @click="setAsAdmin(scope.row)" size="small" v-else
							disabled>设置为管理医师</el-button>
					</template>
				</el-table-column>
			</el-table>
		</el-card>
		<el-dialog title="添加医生" :visible.sync="dialogFormVisible">
			<el-form :model="addDoctorForm">
				<el-form-item label="医生姓名">
					<el-input v-model="addDoctorForm.name" autocomplete="off"></el-input>
				</el-form-item>
				<el-form-item label="手机号">
					<el-input v-model="addDoctorForm.phone" autocomplete="off"></el-input>
				</el-form-item>
				<el-form-item label="邮箱">
					<el-input v-model="addDoctorForm.email" autocomplete="off"></el-input>
				</el-form-item>
				<el-form-item label="密码">
					<el-input v-model="addDoctorForm.password" autocomplete="off"></el-input>
				</el-form-item>
			</el-form>
			<div slot="footer" class="dialog-footer">
				<el-button @click="dialogFormVisible = false">取 消</el-button>
				<el-button type="primary" @click="createDoctor">确 定</el-button>
			</div>
		</el-dialog>
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
			tableData: [],
			dialogFormVisible: false,
			addDoctorForm: {
				name: '',
				phone: '',
				email: '',
				password: ''
			}
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
				timeout: 10000,
			})
			instance.post("/api/user/get_all_doctors").then(async res => {
				console.log(res)
				for (var i = 0; i < res.data.doctors_info.length; i++) {
					var tag = ''
					if (res.data.doctors_info[i].doctor_is_admin) {
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
			instance.post("/api/user/make_doctor_admin", {
				is_admin: true,
				doctor_id: row.id
			}).then(async res => {
				console.log(res)
				this.$message({
					showClose: true,
					message: res.data.msg,
					type: 'success'
				});
				row.isAdmin = '管理医师'
			});
		},
		createDoctor() {
			if (this.addDoctorForm.name === '') {
				this.$message({
					showClose: true,
					message: `请填写医生姓名！`,
					type: 'error'
				});
				return
			}
			else if (this.addDoctorForm.phone === '') {
				this.$message({
					showClose: true,
					message: `请填写医生手机号！`,
					type: 'error'
				});
				return
			}
			else if (this.addDoctorForm.email === '') {
				this.$message({
					showClose: true,
					message: `请填写医生邮箱！`,
					type: 'error'
				});
				return
			}
			else if (this.addDoctorForm.password === '') {
				this.$message({
					showClose: true,
					message: `请填写医生密码！`,
					type: 'error'
				});
				return
			}
			let instance = axios.create({
				baseURL: "http://127.0.0.1:8000",
				timeout: 1000,
			})
			instance.post("/api/user/add_doctor", {
				is_admin: true,
				name: this.addDoctorForm.name,
				phone: this.addDoctorForm.phone,
				mail: this.addDoctorForm.email,
				password: this.addDoctorForm.password
			}).then(async res => {
				console.log(res)
				this.$message({
					showClose: true,
					message: res.data.msg,
					type: 'success'
				});
				this.tableData.push({
					id: '',
					name: this.addDoctorForm.name,
					phone: this.addDoctorForm.phone,
					mail: this.addDoctorForm.email,
					isAdmin: false
				})
				this.addDoctorForm = {
					name: '',
					phone: '',
					email: '',
					password: ''
				}
				this.dialogFormVisible = false;
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
	font-family: Georgia, 'Times New Roman', Times, serif;
	background-color: #2f4c41;
	color: #c6c6c6;
	padding: 10px;
	border-radius: 10px;
	width: 600px;
}

.mb15 {
	width: 80%;
}

.admin-header {
	display: flex;
	justify-content: space-between;
}

//表头背景颜色
::v-deep .el-table th {
	background-color: rgba(1, 0, 75, 0.867);
	height: 30px;
}

//表头字体颜色
::v-deep.el-table thead {
	color: #ffffff;
}

//空表格背景颜色
::v-deep .el-table__empty-block {
	background: #191919;
}

//空表格字体颜色
::v-deep .el-table__empty-text {
	color: #ccc;
}

//修改行内线
::v-deep .el-table td,
.building-top .el-table th.is-leaf {
	border-bottom: 1px solid #7856ff !important;
}

//修改鼠标选中行
::v-deep .el-table__body tr.hover-row>td {
	background: rgba(2, 0, 122, 0.867);
}

//修改普通行
// ::v-deep .el-table tr {
// 	background: rgba(1, 0, 75, 0.867);
// 	height: 20px;
// }

::v-deep .el-table--enable-row-transition .el-table__body td,
.el-table .cell {
	background-color: rgba(1, 0, 75, 0.867);
}


//水平和垂直滚动条
::v-deep .el-table--scrollable-x .el-table__body-wrapper {
	overflow-x: hidden;
}
</style>
