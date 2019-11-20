<template>
	<view class="content">
		<view @click="file">上传图片</view>
		<view v-for="(item,index) in imga" :key='index'>
			<img style='width: 150px;' :src="httpA + item" alt="">
		</view>
		<br>
		<hr>
		<br>
		<!--上传视频模块-->
		<view>
			<view  v-if="!showvideo" @tap="test">上传视频</view>
			<view class="videoclass" v-if="showvideo">
				<!-- 视频窗口 -->
				<video id="myVideo" :src="Mp"  enable-danmu danmu-btn controls></video>
				<view class="uni-list">
						<view class="uni-list-cell">
								<view>
										<view class="uni-label">弹幕内容</view>
								</view>
								<view class="uni-list-cell-db">
										<input @blur="bindInputBlur" class="uni-input" type="text" placeholder="在此处输入弹幕内容" />
								</view>
						</view>
				</view>
				<view class="btn-area">
					<button @tap="bindSendDanmu" class="page-body-button" formType="submit">发送弹幕</button>
				</view>
			</view>
		</view>
		<!--飘过-->
		<div class="homelist" ref="aa">
			~~~
		</div>
		<br>
		<hr>
		<br>
		<div @click="showview">
			点击
		</div>
		<transition name="fade">
			<div v-if="viewbox" class="viewbox">慢慢展示~~~~~~~~</div>

		</transition>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				httpA:'http://112.74.55.49:8888/',
				imga:[],
				Mp:'',
				inputValue: '',
				showvideo:false,
				viewbox:false,
			}
		},
		 onReady: function (res) {
		     this.videoContext = uni.createVideoContext('myVideo')
		 },
		onLoad() {

		},
		mounted() {
			// 随机数
		 // let item = setInterval(()=>{
			// 	let mathnum = Math.floor(Math.random()*300+1)
			// 	this.$refs.aa.style.top = mathnum + 'px'
			// 	console.log(mathnum)
			// },8000)
		},
		methods: {
			//  上传视频
			test: function () {
				var self = this;
				uni.chooseVideo({
						count: 1,
						sourceType: ['camera', 'album'],
						success: function (res) {
								self.Mp = res.tempFilePath;
								self.showvideo = true
						}
				});
			},
			// 弹幕输入框
			bindInputBlur: function (e) {
					this.inputValue = e.target.value
			},
			// 发送弹幕
			bindSendDanmu: function () {
					this.videoContext.sendDanmu({
							text: this.inputValue,
							color: this.getRandomColor()
					})
			},
			videoErrorCallback: function (e) {
					console.log('视频错误信息:')
					console.log(e.target.errMsg)
			},
			// 弹幕主题颜色
			getRandomColor: function () {
					const rgb = []
					for (let i = 0; i < 3; ++i) {
							let color = Math.floor(Math.random() * 256).toString(16)
							color = color.length == 1 ? '0' + color : color
							rgb.push(color)
					}
					return '#' + rgb.join('')
			},
			// 上传图片
			file () {
				uni.chooseImage({
			    success: (chooseImageRes) => {
			        const tempFilePaths = chooseImageRes.tempFilePaths;
			        const uploadTask = uni.uploadFile({
			            url: 'http://112.74.55.49/fileInfo/upload',
									fileType:'image/video/audio',
			            filePath: tempFilePaths[0],
			            name: 'file',
			            formData: {
			                'user': 'test'
			            },
			            success: (uploadFileRes) => {
										let json_data = JSON.parse(uploadFileRes.data)
											this.imga = this.imga.concat(json_data.data.fullPath)
			            }
			        });

							uploadTask.onProgressUpdate((res) => {
									console.log('上传进度' + res.progress);
									console.log('已经上传的数据长度' + res.totalBytesSent);
									console.log('预期需要上传的数据总长度' + res.totalBytesExpectedToSend);
							});
			    }
			});
			},
			showview () {
				this.viewbox = !this.viewbox
			}
		}
	}
</script>

<style scoped lang='less'>
	.videoclass{
		text-align: center;
	}
	 .homelist{
			z-index: 99999;
	    min-width: 80px;
	    padding:2px 5px ;
	    color: #fff;
	    background: rgba(0,0,0,0.5);
	    animation:myfirst 8s infinite;
	    -moz-animation:myfirst 8s infinite; /* Firefox */
	    -webkit-animation:myfirst 8s infinite; /* Safari and Chrome */
	    -o-animation:myfirst 8s infinite; /* Opera */
	    transition:transform ease-in-out;
	    -moz-transition: -moz-transform ease-in-out; /* Firefox 4 */
	    -webkit-transition: -webkit-transform ease-in-out; /* Safari and Chrome */
	    -o-transition:-o-transform ease-in-out; /* Opera */
	    position: fixed;
	    border-radius: 5px;
	    text-align: center;
	    top: 100px;
	    img{
	      vertical-align: middle;
	      width: 20px;
	      height: 20px;
	      border-radius: 50%;
	    }
	  }
	  @keyframes myfirst
	  {
	    from {left:0%;}
	    to {left:100%;}
	  }
	.viewbox{
		height: 50px;
		background: red;
		color: #ffffff;
		line-height: 50px;
	}
	.fade-enter-active, .fade-leave-active {
		transition: opacity .5s;
	}
	.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
		opacity: 0;
	}
</style>
