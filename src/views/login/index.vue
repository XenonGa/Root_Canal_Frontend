<template>
	<div class="login">
		<div class="login-weaper">
			<div class="login-left">
				<div class="login-time">{{ time.txt }}</div>
				<div class="login-left-box">
					<div>
						<div class="logo">{{ getThemeConfig.globalViceTitle }}</div>
						<h2 class="title">{{ getThemeConfig.globalViceDes }}</h2>
						<div class="msg">
							<div class="msg-author">
								<span>{{ quotations.name }}</span>
								<span>{{ quotations.comeFrom }}</span>
							</div>
							<div class="msg-txt">{{ quotations.content }}</div>
						</div>
					</div>
				</div>
			</div>
			<div class="login-right">
				<div class="login-main">
					<h4 class="login-title" v-if="isRegister === false">{{ getThemeConfig.globalTitle }}</h4>
					<Transition name="slide-right">
						<el-form class="el-form login-form" v-if="isRegister === false" appear>
							<el-form-item style="margin-left: 0px" prop="account">
								<el-input
									type="text"
									:placeholder="$t('message.login.placeholder1')"
									prefix-icon="el-icon-user"
									v-model="ruleForm.account"
									clearable
									autocomplete="off"
								>
								</el-input>
							</el-form-item>
							<el-form-item style="margin-left: 0px" prop="password">
								<el-input
									type="password"
									:placeholder="$t('message.login.placeholder2')"
									prefix-icon="el-icon-lock"
									v-model="ruleForm.password"
									autocomplete="off"
									:show-password="true"
								>
								</el-input>
							</el-form-item>
							<el-form-item style="margin: 40px 0px 0" class="login-register">
								<el-button type="primary" class="login-submit" @click="submitForm" :loading="submit.loading">
									<span>{{ $t('message.login.btnText') }}</span>
								</el-button>
								<el-button style="margin: 0px 0px 0px 30px" class="login-submit" @click="isRegister = true" :loading="submit.loading">
									<span>{{ $t('message.login.btnTextRegister') }}</span>
								</el-button>
							</el-form-item>
						</el-form>
						<el-form class="el-form login-form" v-else>
							<el-form-item style="margin-left: 0px" prop="inviteCode">
								<el-input
									type="text"
									:placeholder="$t('message.login.registerPlaceholder1')"
									prefix-icon="el-icon-user"
									v-model="registerForm.inviteCode"
									clearable
									autocomplete="off"
								>
								</el-input>
							</el-form-item>
							<el-form-item style="margin-left: 0px" prop="name">
								<el-input
									type="text"
									:placeholder="$t('message.login.registerPlaceholder2')"
									prefix-icon="el-icon-user"
									v-model="registerForm.name"
									clearable
									autocomplete="off"
								>
								</el-input>
							</el-form-item>
							<el-form-item style="margin-left: 0px" prop="phone">
								<el-input
									type="text"
									:placeholder="$t('message.login.registerPlaceholder3')"
									prefix-icon="el-icon-user"
									v-model="registerForm.phone"
									clearable
									autocomplete="off"
								>
								</el-input>
							</el-form-item>
							<el-form-item style="margin-left: 0px" prop="mail">
								<el-input
									type="text"
									:placeholder="$t('message.login.registerPlaceholder4')"
									prefix-icon="el-icon-user"
									v-model="registerForm.mail"
									clearable
									autocomplete="off"
								>
								</el-input>
							</el-form-item>
							<el-form-item style="margin-left: 0px" prop="password">
								<el-input
									type="password"
									:placeholder="$t('message.login.placeholder2')"
									prefix-icon="el-icon-lock"
									v-model="registerForm.password"
									autocomplete="off"
									:show-password="true"
								>
								</el-input>
							</el-form-item>
							<el-form-item style="margin-left: 0px" prop="password">
								<el-input
									type="password"
									:placeholder="$t('message.login.registerPlaceholder5')"
									prefix-icon="el-icon-lock"
									v-model="registerForm.confirmPassword"
									autocomplete="off"
									:show-password="true"
								>
								</el-input>
							</el-form-item>
							<el-form-item style="margin: 40px 0px 0" class="login-register">
								<el-button type="primary" class="login-submit" @click="handleRegister" :loading="submit.loading">
									<span>{{ $t('message.login.btnTextRegister') }}</span>
								</el-button>
								<el-button style="margin: 0px 0px 0px 30px" class="login-submit" @click="isRegister = false" :loading="submit.loading">
									<span>{{ $t('message.login.btnTextReturnToLogin') }}</span>
								</el-button>
							</el-form-item>
						</el-form>
					</Transition>
					
					<!-- <div class="login-menu">
						<a href="javascript:;">{{ $t('message.login.link.one1') }}</a>
						<a href="javascript:;">{{ $t('message.login.link.one2') }}</a>
					</div> -->
				</div>
			</div>
		</div>
		<div class="vue-particles">
			<vue-particles color="#dedede" shapeType="star" linesColor="#dedede" hoverMode="grab" clickMode="push" style="height: 100%"></vue-particles>
		</div>
	</div>
</template>

<script>
import { Session } from '@/utils/storage';
import { formatDate, formatAxis } from '@/utils/formatTime';
import { PrevLoading } from '@/utils/loading.js';
import { quotationsList } from './mock';
import axios from "axios"
export default {
	name: 'login',
	data() {
		return {
			quotationsList,
			isRegister: false,
			quotations: {},
			isView: false,
			submit: {
				loading: false,
			},
			registerForm: {
				inviteCode: '',
				name: '',
				phone: '',
				mail: '',
				password: '',
				confirmPassword: ''
			},
			ruleForm: {
				account: '',
				password: ''
			},
			time: {
				txt: '',
				fun: null,
			},
		};
	},
	computed: {
		// 获取当前时间
		currentTime() {
			return formatAxis(new Date());
		},
		// 获取布局配置信息
		getThemeConfig() {
			return this.$store.state.themeConfig.themeConfig;
		},
	},
	created() {
		this.initTime();
	},
	mounted() {
		this.initRandomQuotations();
	},
	methods: {
		// 随机语录
		initRandomQuotations() {
			this.quotations = this.quotationsList[Math.floor(Math.random() * this.quotationsList.length)];
		},
		// 初始化左上角时间显示
		initTime() {
			this.time.txt = formatDate(new Date(), 'YYYY-mm-dd HH:MM:SS WWW QQQQ');
			this.time.fun = setInterval(() => {
				this.time.txt = formatDate(new Date(), 'YYYY-mm-dd HH:MM:SS WWW QQQQ');
			}, 1000);
		},
		// 登录按钮点击
		submitForm() {
			if(this.ruleForm.account === '' && this.ruleForm.password === '') {
				this.$message({
					showClose: true,
					message: `${this.$t('message.login.pleaseFillin1')}`,
					type: 'error'
				});
				return
			}
			else if(this.ruleForm.account === '') {
				this.$message({
					showClose: true,
					message: `${this.$t('message.login.pleaseFillin2')}`,
					type: 'error'
				});
				return
			}
			else if(this.ruleForm.password === '') {
				this.$message({
					showClose: true,
					message: `${this.$t('message.login.pleaseFillin3')}`,
					type: 'error'
				});
				return
			}
			this.submit.loading = true;
			setTimeout(() => {
				let defaultRoles = [];
				let defaultAuthBtnList = [];
				// admin 页面权限标识，对应路由 meta.roles
				let adminRoles = ['admin'];
				// admin 按钮权限标识
				let adminAuthBtnList = ['btn.add', 'btn.del', 'btn.edit', 'btn.link'];
				// common 页面权限标识，对应路由 meta.roles
				let testAuthPageList = ['common'];
				// test 按钮权限标识
				let testAuthBtnList = ['btn.add', 'btn.link'];

				let instance = axios.create({
				baseURL: "http://127.0.0.1:8000",
				timeout: 1000,
				})
				instance.post("/api/user/login", 
				{
					account: this.ruleForm.account,
					password: this.ruleForm.password
				}).then(async res=>{
					console.log(res)
					console.log(res.data.msg)
					if(res.data.msg !== '登陆成功') {
						this.$message({
							showClose: true,
							message: res.data.msg,
							type: 'error'
						});
						this.submit.loading = false
						return
					}

					if (res.data.doctor_info.doctor_is_admin === true) {
						defaultRoles = adminRoles;
						defaultAuthBtnList = adminAuthBtnList;
					} else {
						defaultRoles = testAuthPageList;
						defaultAuthBtnList = testAuthBtnList;
					}
					const userInfos = {
						userName: res.data.doctor_info.doctor_name,
						id: res.data.doctor_info.doctor_id,
						// userName: 'admin',
						photo:
							res.data.doctor_info.doctor_is_admin === true
								? 'https://img0.baidu.com/it/u=1833472230,3849481738&fm=253&fmt=auto?w=200&h=200'
								: 'https://img2.baidu.com/it/u=2187913762,2708298335&fm=253&fmt=auto&app=138&f=JPEG?w=200&h=200',
						time: new Date().getTime(),
						isAdmin: res.data.doctor_info.doctor_is_admin,
						roles: defaultRoles,
						authBtnList: defaultAuthBtnList,
					};
					// 存储 token 到浏览器缓存
					Session.set('token', Math.random().toString(36).substr(0));
					// 存储用户信息到浏览器缓存
					Session.set('userInfo', userInfos);
					// 存储用户信息到vuex
					this.$store.dispatch('userInfos/setUserInfos', userInfos);
					PrevLoading.start();
					this.$router.push('/');
					setTimeout(() => {
					this.$message.success(`${this.currentTime}，${this.$t('message.login.signInText')}`);
					}, 300);
				});
			}, 500);
		},
		changeToRegister() {
			this.isRegister = true
		},
		handleRegister() {
			if(this.registerForm.inviteCode === '') {
				this.$message({
					showClose: true,
					message: `${this.$t('message.login.registerPleaseFillin1')}`,
					type: 'error'
				});
				return
			}
			else if(this.registerForm.name === '') {
				this.$message({
					showClose: true,
					message: `${this.$t('message.login.registerPleaseFillin2')}`,
					type: 'error'
				});
				return
			}
			else if(this.registerForm.phone === '') {
				this.$message({
					showClose: true,
					message: `${this.$t('message.login.registerPleaseFillin3')}`,
					type: 'error'
				});
				return
			}
			else if(this.registerForm.mail === '') {
				this.$message({
					showClose: true,
					message: `${this.$t('message.login.registerPleaseFillin4')}`,
					type: 'error'
				});
				return
			}
			else if(this.registerForm.password === '') {
				this.$message({
					showClose: true,
					message: `${this.$t('message.login.registerPleaseFillin5')}`,
					type: 'error'
				});
				return
			}
			else if(this.registerForm.confirmPassword === '') {
				this.$message({
					showClose: true,
					message: `${this.$t('message.login.registerPleaseFillin6')}`,
					type: 'error'
				});
				return
			}
			else if(this.registerForm.confirmPassword !== this.registerForm.password) {
				this.$message({
					showClose: true,
					message: `${this.$t('message.login.registerNotConfirm')}`,
					type: 'error'
				});
				return
			}
			this.submit.loading = true
			setTimeout(() => {
				let instance = axios.create({
					baseURL: "http://127.0.0.1:8000",
					timeout: 1000,
				})
				instance.post("/api/user/register", 
				{
					code_content: this.registerForm.inviteCode,
					name: this.registerForm.name,
					phone: this.registerForm.phone,
					mail: this.registerForm.mail,
					password: this.registerForm.password
				}).then(async res=>{
					console.log(res)
					if(res.data.msg !== '注册成功') {
						this.$message({
							showClose: true,
							message: res.data.msg,
							type: 'error'
						});
						this.submit.loading = false
						return
					}
					else {
						this.$message({
							showClose: true,
							message: res.data.msg,
							type: 'success'
						});
						this.submit.loading = false
					}
				});
			}, 500)
			this.submit.loading = false
		}
	},
	destroyed() {
		clearInterval(this.time.fun);
	},
};
</script>

<style scoped lang="scss">
.login {
	height: 100%;
	width: 100%;
	overflow: hidden;
	display: flex;
	position: relative;
	.vue-particles {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background: radial-gradient(ellipse at top left, rgba(105, 155, 200, 1) 0%, rgba(181, 197, 216, 1) 57%);
	}
	.login-weaper {
		margin: auto;
		height: 500px;
		display: flex;
		box-sizing: border-box;
		position: relative;
		z-index: 1;
		border: none;
		box-shadow: 0 1px 4px rgba(0, 21, 41, 0.08);
		.login-left {
			width: 491px;
			padding: 20px;
			font-size: 16px;
			color: var(--prev-color-text-white);
			position: relative;
			background-color: var(--prev-color-primary);
			display: flex;
			flex-direction: column;
			border-top-left-radius: 4px;
			border-bottom-left-radius: 4px;
			.login-time {
				width: 100%;
				color: var(--prev-color-text-white);
				opacity: 0.9;
				font-size: 14px;
				overflow: hidden;
			}
			.login-left-box {
				flex: 1;
				overflow: hidden;
				position: relative;
				z-index: 1;
				display: flex;
				align-items: center;
				padding: 80px 45px;
				.logo {
					font-size: 22px;
					margin-bottom: 15px;
				}
				.title {
					color: var(--prev-color-text-white);
					font-weight: 300;
					letter-spacing: 2px;
					font-size: 16px;
				}
				.msg {
					color: var(--prev-color-text-white);
					font-size: 13px;
					margin-top: 35px;
					.msg-author {
						opacity: 0.6;
						span:last-of-type {
							margin-left: 15px;
						}
					}
					.msg-txt {
						margin-top: 15px;
						line-height: 22px;
					}
				}
			}
		}
		.login-right {
			width: 491px;
			padding: 20px;
			position: relative;
			align-items: center;
			display: flex;
			background-color: var(--prev-bg-white);
			border-top-right-radius: 4px;
			border-bottom-right-radius: 4px;
			.login-main {
				margin: 0 auto;
				width: 70%;
				.login-title {
					color: var(--prev-color-text-primary);
					margin-bottom: 40px;
					font-weight: 500;
					font-size: 22px;
					text-align: center;
					letter-spacing: 4px;
				}
				.login-form {
					margin: 10px 0;
					i {
						color: var(--prev-color-text-primary);
					}
					.el-form-item {
						margin-bottom: 20px !important;
						.login-code {
							display: flex;
							align-items: center;
							justify-content: space-around;
							margin: 0 0 0 10px;
							user-select: none;
							.login-code-img {
								margin-top: 2px;
								width: 100px;
								height: 38px;
								border: 1px solid var(--prev-border-color-base);
								color: var(--prev-color-text-primary);
								font-size: 14px;
								font-weight: 700;
								letter-spacing: 5px;
								line-height: 38px;
								text-indent: 5px;
								text-align: center;
								cursor: pointer;
								transition: all ease 0.2s;
								border-radius: 4px;
								&:hover {
									border-color: var(--prev-border-color-hover);
									transition: all ease 0.2s;
								}
							}
						}
						.login-submit {
							width: 45%;
							letter-spacing: 2px;
						}
					}
				}
				.login-menu {
					margin-top: 30px;
					width: 100%;
					text-align: left;
					a {
						color: var(--prev-color-text-secondary);
						font-size: 12px;
						margin: 0 8px;
						text-decoration: none;
						&:hover {
							color: var(--prev-color-primary);
							text-decoration: underline;
						}
					}
				}
			}
		}
	}
}
.slide-right-enter-active,
.slide-right-leave-active {
	will-change: transform;
	transition: all 0.3s ease;
}
// slide-right
.slide-right-enter {
	opacity: 0;
	transform: translateX(-20px);
}
.slide-right-leave-active {
	opacity: 0;
	transform: translateX(20px);
}

.opacitys-enter-active,
.opacitys-leave-active {
	will-change: transform;
	transition: all 0.3s ease;
}
.opacitys-enter,
.opacitys-leave-active {
	opacity: 0;
}
</style>
