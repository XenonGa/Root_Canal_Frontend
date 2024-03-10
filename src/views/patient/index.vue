<template>
	<div class="addAdmin">
		<!-- <div class="layout-view-bg-white flex">
			<div class="flex-margin color-primary">personal</div>
		</div> -->
				<el-card shadow="hover">
					<div slot="header" class="patient-header">
						<div style="margin-top: 10px;">{{ $t('message.router.patient') }}</div>
						<el-button type="primary" round @click="dialogFormVisible = true">新增患者</el-button>
					</div>
					<el-table
					:data="tableData"
					style="width: 100%"
					max-height="1000">
					<el-table-column
					prop="name"
					label="姓名"
					width="120">
					</el-table-column>
					<el-table-column
					prop="description"
					label="描述"
					width="300">
					</el-table-column>
					<el-table-column
					fixed="right"
					label="操作"
					width="160">
					<template slot-scope="scope">
						<el-button @click="deletePatient(scope.row, scope.$index)" type="danger" size="small">删除患者</el-button>
					</template>
					</el-table-column>
				</el-table>
				</el-card>
				
				<el-dialog title="添加患者" :visible.sync="dialogFormVisible">
					<el-form :model="form">
						<el-form-item label="患者姓名">
							<el-input v-model="form.name" autocomplete="off"></el-input>
						</el-form-item>
						<el-form-item label="诊断描述">
							<el-input
							type="textarea"
							:rows="3"
							placeholder="请输入描述"
							v-model="form.description">
							</el-input>
						</el-form-item>
					</el-form>
					<div slot="footer" class="dialog-footer">
						<el-button @click="dialogFormVisible = false">取 消</el-button>
						<el-button type="primary" @click="createPatient">确 定</el-button>
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
			form: {
				name: '',
				description: '',
			}
		};
	},
	created() {
		this.getPatientInfo()
	},
	computed: {
		// 设置主盒子高度
		setMainHeight() {
			let { isTagsview } = this.$store.state.themeConfig.themeConfig;
			if (isTagsview) return `114px`;
			else return `80px`;
		},
		getUserInfos() {
			return this.$store.state.userInfos.userInfos;
		},
	},
	
	methods: {
		getPatientInfo() {
			let data = this.$store.state.userInfos.userInfos;
			// console.log(data)
			let instance = axios.create({
				baseURL: "http://127.0.0.1:8000",
				timeout: 1000,
			})
			instance.post("/api/user/get_patient_of_doctor", {
				doctor_id: data.id
			}).then(async res=>{
				console.log(res)
				for(var i = 0; i < res.data.patients_info.length; i++) {
					this.tableData.push({
						id: res.data.patients_info[i].patient_id,
						name: res.data.patients_info[i].patient_name,
						description: res.data.patients_info[i].description,
					})
				}
			});
			console.log(this.tableData)
		},
		createPatient() {
			if(this.form.name === '') {
				this.$message({
					showClose: true,
					message: `请填写患者姓名！`,
					type: 'error'
				});
				return
			}
			else if(this.form.description === '') {
				this.$message({
					showClose: true,
					message: `请填写诊断描述！`,
					type: 'error'
 				});
				return
			}
			let instance = axios.create({
				baseURL: "http://127.0.0.1:8000",
				timeout: 1000,
			})
			instance.post("/api/user/create_patient", {
				doctor_id: this.$store.state.userInfos.userInfos.id,
				patient_name: this.form.name,
				patient_description: this.form.description
			}).then(async res=>{
				console.log(res)
				this.$message({
					showClose: true,
					message: res.data.msg,
					type: 'success'
				});
				this.tableData.push({
					name: this.form.name,
					description: this.form.description
				})
				this.form.name = ''
				this.form.description = ''
				this.dialogFormVisible = false
			});
		},
		deletePatient(row, index) {
			let instance = axios.create({
				baseURL: "http://127.0.0.1:8000",
				timeout: 1000,
			})
			instance.post("/api/user/delete_patient",{
				patient_id: row.id
			}).then(async res=>{
				console.log(res)
				this.$message({
					showClose: true,
					message: res.data.msg,
					type: 'success'
				});
				this.tableData.splice(index, 1);
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
.patient-header {
	display: flex;
	justify-content: space-between;
}
</style>
