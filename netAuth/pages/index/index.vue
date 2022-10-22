<template>
	<view>
		<canvas canvas-id="captcha" id="captcha"></canvas>
		<uni-popup ref="message" type="message">
			<uni-popup-message :type="popType" :message="popMsg" :duration="2000"></uni-popup-message>
		</uni-popup>
		<img src="static/logo.png" alt="logo" class="logo">
		<view class="form">
			<uni-easyinput suffixIcon="person" v-model="username" placeholder="请输入账号" class="easyinput"
				@change="saveConfig('username', username)"></uni-easyinput>
			<uni-easyinput type="password" v-model="password" placeholder="请输入密码" class="easyinput"
				@change="saveConfig('password', password)"></uni-easyinput>
			<uni-data-checkbox v-model="channel" :localdata="channelRange" @change="saveConfig('channel', channel)">
			</uni-data-checkbox>
		</view>
		<button type="primary" plain="ture" @click="clickLogin" class="login-bt">{{loginBtMsg[step + 1]}}</button>
		<uni-link href="https://chowluking.com/share?target=netAuth" text="关于软件" showUnderLine="false" class="btm-url">
		</uni-link>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				header: {
					'Cookie': '',
					'Content-Type': 'application/x-www-form-urlencoded',
					'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36'
				},
				username: '',
				password: '',
				channel: 'default',
				channelRange: [{
					value: 'default',
					text: '校园内网'
				}, {
					value: '@cmcc',
					text: '中国移动'
				}, {
					value: '@telecom',
					text: '中国电信'
				}],
				lt: '',
				numberLs: [
					[255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,
						255, 255, 255, 255,
						255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 236, 247,
						250, 250, 244, 255,
						245, 236, 242, 251, 225, 240, 235, 216, 182, 127, 114, 174, 219, 237, 180, 203, 199, 158, 66,
						22, 42, 122, 188,
						216, 160, 168, 160, 106, 67, 41, 58, 132, 196, 198, 143, 145, 128, 109, 85, 57, 65, 135, 183,
						185, 153, 168, 160,
						145, 119, 75, 83, 142, 190, 192, 173, 188, 200, 196, 138, 87, 85, 141, 195, 201, 191, 227, 235,
						202, 131, 73, 81,
						154, 203, 205, 187, 221, 233, 195, 118, 67, 80, 146, 201, 204, 176, 211, 227, 200, 128, 67, 76,
						141, 195, 201, 178,
						207, 226, 201, 136, 81, 80, 137, 189, 202, 160, 166, 174, 158, 117, 69, 57, 94, 130, 147, 159,
						168, 143, 99, 44,
						16, 11, 36, 69, 123, 171, 165, 151, 130, 69, 67, 64, 72, 138, 133, 207, 206, 215, 209, 199,
						157, 191, 191, 148,
						164
					],
					[255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,
						255, 255, 255, 255,
						255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 244, 250,
						233, 214, 201, 214,
						181, 179, 212, 246, 231, 213, 183, 139, 62, 20, 49, 113, 162, 198, 181, 164, 103, 54, 35, 42,
						26, 35, 82, 143, 137,
						102, 73, 81, 145, 158, 126, 67, 68, 103, 102, 73, 76, 130, 203, 228, 179, 94, 47, 55, 151, 156,
						160, 187, 235, 230,
						169, 87, 53, 64, 200, 205, 225, 242, 240, 202, 135, 78, 71, 103, 217, 245, 251, 240, 209, 151,
						80, 72, 114, 154,
						209, 240, 226, 184, 132, 73, 70, 114, 161, 193, 193, 211, 178, 128, 68, 71, 129, 185, 208, 200,
						171, 162, 131, 80,
						70, 127, 171, 190, 189, 190, 136, 114, 63, 24, 64, 88, 98, 96, 103, 120, 130, 83, 31, 15, 7, 0,
						25, 23, 72, 122,
						149, 124, 56, 69, 28, 63, 65, 79, 144, 164, 201, 185, 191, 191, 191, 139, 191, 192, 154, 187
					],
					[255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,
						255, 255, 255, 255,
						255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 243, 248,
						226, 207, 201, 217,
						184, 185, 224, 247, 227, 209, 175, 129, 50, 19, 55, 122, 167, 212, 175, 153, 85, 41, 22, 29,
						35, 48, 105, 160, 125,
						101, 58, 75, 133, 146, 109, 59, 79, 120, 123, 126, 137, 169, 212, 218, 148, 76, 46, 74, 184,
						187, 194, 213, 214,
						195, 117, 45, 55, 98, 213, 231, 216, 168, 111, 51, 34, 42, 97, 148, 212, 230, 204, 141, 57, 21,
						17, 53, 98, 155,
						207, 232, 221, 204, 185, 127, 66, 45, 85, 127, 175, 164, 200, 241, 247, 235, 152, 78, 46, 79,
						128, 138, 165, 185,
						226, 228, 175, 96, 50, 69, 88, 71, 59, 86, 158, 173, 129, 59, 40, 77, 140, 107, 62, 42, 26, 31,
						45, 37, 75, 124,
						199, 180, 148, 115, 58, 40, 73, 134, 166, 195, 231, 236, 213, 204, 189, 182, 201, 195, 219, 240
					],
					[255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,
						255, 255, 255, 255,
						255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 247, 253,
						254, 254, 243, 254,
						240, 210, 216, 251, 241, 251, 253, 242, 212, 146, 107, 141, 196, 227, 221, 246, 247, 217, 149,
						71, 39, 66, 143,
						210, 215, 239, 224, 166, 100, 32, 20, 79, 166, 214, 211, 221, 180, 105, 42, 11, 22, 90, 163,
						204, 205, 191, 141,
						79, 34, 33, 45, 101, 171, 209, 194, 157, 93, 86, 78, 68, 66, 100, 172, 211, 157, 101, 78, 113,
						123, 103, 76, 106,
						173, 214, 103, 60, 37, 56, 104, 64, 26, 62, 119, 179, 45, 28, 15, 30, 4, 0, 5, 21, 72, 145, 85,
						69, 63, 58, 40, 29,
						16, 26, 71, 127, 156, 152, 159, 206, 166, 91, 49, 60, 125, 160, 221, 243, 243, 223, 151, 102,
						43, 72, 134, 188,
						228, 249, 251, 238, 192, 126, 95, 118, 190, 217, 240, 249, 251, 243, 227, 211, 208, 204, 193,
						223
					],
					[255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,
						255, 255, 255, 255,
						255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 239, 219,
						193, 172, 193, 192,
						191, 135, 147, 225, 215, 199, 163, 103, 10, 1, 58, 70, 110, 176, 161, 135, 77, 33, 4, 0, 29,
						39, 73, 134, 140, 111,
						67, 71, 111, 137, 185, 155, 150, 169, 129, 91, 55, 89, 156, 206, 227, 234, 233, 214, 129, 91,
						61, 45, 25, 57, 93,
						145, 182, 187, 139, 98, 40, 14, 1, 0, 22, 61, 117, 161, 139, 113, 85, 88, 120, 100, 84, 56, 75,
						125, 168, 161, 161,
						198, 240, 219, 154, 80, 58, 95, 172, 153, 191, 235, 248, 235, 177, 94, 52, 71, 132, 139, 151,
						184, 220, 224, 169,
						93, 54, 86, 99, 75, 61, 88, 159, 165, 102, 54, 44, 90, 141, 107, 66, 47, 31, 30, 48, 50, 99,
						143, 204, 179, 149,
						126, 65, 48, 83, 143, 176, 203, 230, 236, 216, 205, 190, 186, 205, 199, 223, 241
					],
					[255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,
						255, 255, 255, 255,
						255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 245, 251,
						238, 220, 212, 216,
						188, 176, 210, 244, 235, 229, 197, 162, 83, 34, 52, 110, 156, 205, 193, 184, 132, 66, 26, 13,
						23, 41, 85, 154, 157,
						134, 83, 67, 110, 133, 110, 86, 103, 138, 113, 76, 58, 100, 165, 233, 218, 188, 155, 154, 99,
						66, 49, 64, 101, 103,
						109, 162, 184, 194, 96, 52, 19, 2, 0, 0, 14, 71, 138, 180, 87, 47, 27, 46, 77, 90, 63, 54, 84,
						142, 81, 45, 47,
						103, 185, 195, 140, 67, 62, 107, 70, 51, 76, 149, 218, 229, 173, 90, 53, 79, 87, 61, 73, 123,
						208, 217, 165, 89,
						56, 90, 118, 85, 60, 75, 123, 145, 109, 52, 54, 93, 173, 143, 90, 46, 29, 15, 46, 56, 105, 147,
						215, 208, 177, 133,
						86, 48, 88, 141, 177, 207, 236, 242, 227, 209, 198, 191, 209, 196, 222, 244
					],
					[255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,
						255, 255, 255, 255,
						255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 239, 214,
						184, 161, 191, 191,
						191, 130, 137, 213, 206, 194, 154, 91, 8, 0, 55, 65, 91, 154, 159, 116, 55, 19, 1, 0, 20, 14,
						26, 78, 144, 135,
						107, 116, 154, 97, 65, 36, 61, 93, 165, 188, 199, 213, 212, 185, 106, 87, 99, 126, 191, 225,
						244, 242, 198, 137,
						98, 117, 163, 185, 207, 238, 246, 218, 161, 100, 102, 152, 199, 205, 205, 239, 236, 194, 138,
						90, 113, 190, 223,
						215, 198, 230, 221, 158, 89, 92, 149, 211, 232, 214, 182, 217, 208, 142, 90, 96, 162, 220, 230,
						211, 182, 210, 192,
						125, 78, 96, 167, 224, 232, 211, 181, 212, 199, 131, 81, 105, 179, 230, 233, 208, 197, 224,
						201, 143, 83, 110, 176,
						233, 241, 219, 210, 237, 226, 187, 149, 168, 209, 244, 249, 235, 231, 242, 241, 227, 224, 231,
						237, 249, 252, 246
					],
					[255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,
						255, 255, 255, 255,
						255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 245, 248,
						223, 208, 202, 207,
						184, 183, 223, 250, 225, 209, 177, 134, 54, 23, 53, 121, 162, 215, 172, 153, 86, 50, 21, 28,
						35, 49, 97, 160, 133,
						101, 61, 70, 124, 153, 119, 63, 70, 124, 105, 66, 53, 95, 172, 197, 161, 80, 55, 86, 123, 91,
						56, 52, 95, 105, 73,
						42, 64, 110, 160, 109, 62, 27, 2, 0, 19, 46, 112, 169, 153, 110, 55, 18, 3, 1, 9, 54, 113, 174,
						124, 89, 59, 54,
						68, 60, 40, 47, 96, 154, 66, 51, 71, 125, 200, 210, 110, 50, 53, 103, 63, 54, 99, 162, 226,
						221, 173, 85, 50, 88,
						74, 53, 57, 94, 153, 177, 115, 55, 47, 88, 125, 97, 61, 41, 28, 21, 48, 49, 89, 136, 193, 173,
						138, 106, 60, 46,
						78, 141, 174, 204, 231, 233, 209, 202, 188, 180, 206, 197, 220, 242
					],
					[255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,
						255, 255, 255, 255,
						255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 245, 249,
						224, 202, 200, 211,
						193, 200, 233, 251, 227, 215, 170, 131, 51, 22, 68, 138, 176, 227, 172, 148, 92, 40, 17, 20,
						33, 61, 122, 184, 124,
						96, 57, 70, 127, 141, 114, 75, 99, 138, 84, 57, 59, 125, 200, 218, 155, 89, 58, 88, 81, 58, 83,
						160, 217, 226, 167,
						86, 52, 77, 100, 63, 67, 133, 199, 199, 139, 67, 50, 83, 117, 89, 59, 56, 72, 64, 33, 27, 47,
						91, 149, 120, 76, 26,
						3, 0, 0, 7, 37, 91, 173, 175, 170, 145, 130, 103, 73, 47, 51, 94, 156, 158, 149, 185, 240, 191,
						103, 53, 66, 113,
						142, 118, 103, 100, 140, 121, 66, 57, 89, 143, 152, 119, 70, 35, 18, 14, 54, 94, 161, 191, 200,
						179, 138, 96, 58,
						68, 122, 178, 211, 229, 230, 233, 209, 197, 195, 188, 220, 213, 238, 248
					]
				],
				step: 0,
				timer: null,
				loginBtMsg: [
					'停止登陆',
					'开始登陆',
					'停止登陆',
					'重新登陆'
				],
				popType: '',
				popMsg: ''
			}
		},
		methods: {
			disImage(path) {
				const ctx = uni.createCanvasContext('captcha');
				ctx.drawImage(path, 0, 0, 40, 19);
				ctx.save();
				ctx.draw(true, () => {
					uni.canvasGetImageData({
						canvasId: 'captcha',
						x: 0,
						y: 0,
						width: 40,
						height: 19,
						success: (res) => {
							const data = res.data;
							const pixelLs_1 = [];
							const pixelLs_2 = [];
							const pixelLs_3 = [];
							const pixelLs_4 = [];
							for (let i = 0; i < 3040; i += 4) {
								const pixel = data[i] * 0.299 + data[i + 1] * 0.587 + data[i + 2] *
									0.114;
								pixel = pixel > 150 ? 255 : 0;
								const remainder = Math.floor(i / 4) % 40;
								if (2 <= remainder && remainder < 12) pixelLs_1.push(pixel);
								if (11 <= remainder && remainder < 21) pixelLs_2.push(pixel);
								if (20 <= remainder && remainder < 30) pixelLs_3.push(pixel);
								if (29 <= remainder && remainder < 39) pixelLs_4.push(pixel);
							}
							const lossLs_1 = [];
							const lossLs_2 = [];
							const lossLs_3 = [];
							const lossLs_4 = [];
							for (let i = 0; i < 9; i++) {
								const numberLi = this.numberLs[i];
								let loss_1 = 0;
								let loss_2 = 0;
								let loss_3 = 0;
								let loss_4 = 0;
								for (let k = 0; k < 190; k++) {
									loss_1 += Math.abs(pixelLs_1[k] - numberLi[k]);
									loss_2 += Math.abs(pixelLs_2[k] - numberLi[k]);
									loss_3 += Math.abs(pixelLs_3[k] - numberLi[k]);
									loss_4 += Math.abs(pixelLs_4[k] - numberLi[k]);
								}
								lossLs_1.push(loss_1);
								lossLs_2.push(loss_2);
								lossLs_3.push(loss_3);
								lossLs_4.push(loss_4);
							}
							const code = '' +
								(lossLs_1.indexOf(Math.min(...lossLs_1)) + 1) +
								(lossLs_2.indexOf(Math.min(...lossLs_2)) + 1) +
								(lossLs_3.indexOf(Math.min(...lossLs_3)) + 1) +
								(lossLs_4.indexOf(Math.min(...lossLs_4)) + 1);
							console.log(code);
							this.postForm(code);
						}
					});
				});
			},
			postForm(code) {
				uni.request({
					url: 'https://u.njtech.edu.cn/cas/login?service=https%3A%2F%2Fu.njtech.edu.cn%2Foauth2%2Fauthorize',
					method: 'POST',
					header: this.header,
					data: {
						'username': this.username,
						'password': this.password,
						'channel': this.channel,
						'lt': this.lt,
						'execution': 'e1s1',
						'_eventId': 'submit',
						'captcha': code
					},
					success: (res) => {
						console.log(res);
						if (res.data.indexOf('返回登录') > 0) {
							clearInterval(this.timer);
							this.timer = null;
							this.step = 2;
							this.popType = 'success';
							this.popMsg = '登陆成功,广快上网~';
							this.$refs.message.open();
						} else if (res.data.indexOf('密码错误') > 0) {
							clearInterval(this.timer);
							this.timer = null;
							this.step = 0;
							this.popType = 'error';
							this.popMsg = '账号或密码错误!';
							this.$refs.message.open();
						} else if (res.data.indexOf('将会冻结') > 0) {
							clearInterval(this.timer);
							this.timer = null;
							this.step = 0;
							this.popType = 'error';
							this.popMsg = '账号被冻结请稍等!';
							this.$refs.message.open();
						} else {
							this.step = -1;
							this.popType = 'warn';
							this.popMsg = '登陆出错,自动重登中';
							this.$refs.message.open();
						}
					},
					fail: (res) => {
						console.log(res);
						this.step = -1;
					}
				});
			},
			netConnect() {
				if (this.step === 1) return;
				this.step = 1;
				this.header.Cookie = '';
				uni.request({
					url: 'https://u.njtech.edu.cn/cas/login?service=https%3A%2F%2Fu.njtech.edu.cn%2Foauth2%2Fauthorize',
					header: this.header,
					success: (res) => {
						const lt = res.data.split('name="lt"')[1].split('"')[1];
						this.lt = lt;
						console.log(lt);
						const cookies = res.cookies;
						console.log(cookies);
						this.header.Cookie = cookies[0].split(';')[0] + ';' + cookies[1].split(';')[0];
						console.log(this.header);
						uni.downloadFile({
							url: 'https://u.njtech.edu.cn/cas/captcha.jpg',
							header: this.header,
							success: (res) => {
								console.log(res.tempFilePath);
								this.disImage(res.tempFilePath, lt, cookies);
							},
							fail: (res) => {
								console.log(res);
								this.step = -1;
							}
						});
					},
					fail: (res) => {
						console.log(res);
						this.step = -1;
					}
				});
			},
			clickLogin() {
				if (this.step !== 1) {
					this.timer = setInterval(() => {
						this.netConnect();
					}, 200);
				} else {
					clearInterval(this.timer);
					this.timer = null;
					this.step = 0;
				}
			},
			saveConfig(key, data) {
				uni.setStorage({
					key,
					data,
					success: (res) => {
						console.log(data);
					}
				});
			},
			getConfig() {
				const username = uni.getStorageSync('username');
				if (username) this.username = username;
				const password = uni.getStorageSync('password');
				if (password) this.password = password;
				const channel = uni.getStorageSync('channel');
				if (channel) this.channel = channel;
			}
		},
		mounted() {
			this.getConfig();
			if (this.username && this.password && this.channel) this.clickLogin();
		}
	}
</script>

<style>
	#captcha {
		visibility: hidden;
		height: 100px;
	}

	.logo {
		height: 200rpx;
		margin-left: calc(50% - 83rpx);
	}

	.form {
		margin: 100rpx auto 0 auto;
		width: 80%;
	}

	.easyinput {
		margin-bottom: 20rpx;
	}

	.login-bt {
		margin: 100rpx auto 80rpx auto;
		width: 80%;
	}

	.btm-url {
		display: block;
		width: 56px;
		margin: auto;
	}
</style>
