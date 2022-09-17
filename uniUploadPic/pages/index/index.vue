<template>
	<view class="content">
		<u-upload :fileList="fileList1" @afterRead="afterRead" @delete="deletePic"
			name="1" multiple :maxCount="1" width="200" height="200"></u-upload>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',
				fileList1: [],
			}
		},
		onLoad() {

		},
		methods: {
			deletePic(event) {
				this[`fileList${event.name}`].splice(event.index, 1)
			},
			// 新增图片
			async afterRead(event) {
				// 当设置 mutiple 为 true 时, file 为数组格式，否则为对象格式
				let lists = [].concat(event.file)
				console.log(event)
				console.log(this[`fileList${event.name}`])
				let fileListLen = this[`fileList${event.name}`].length
				lists.map((item) => {
					this[`fileList${event.name}`].push({
						...item,
						status: 'uploading',
						message: '上传中'
					})
				})
				for (let i = 0; i < lists.length; i++) {
					const result = await this.uploadFilePromise(lists[i].url)
					let item = this[`fileList${event.name}`][fileListLen]
					this[`fileList${event.name}`].splice(fileListLen, 1, Object.assign(item, {
						status: 'success',
						message: '',
						url: result
					}))
					fileListLen++
				}
			},
			uploadFilePromise(url) {
				return new Promise((resolve, reject) => {
					console.log(this.fileList1[0].url);
					let a = uni.uploadFile({
						url: 'http://localhost:8080/upload/picture', // 仅为示例，非真实的接口地址
						filePath: this.fileList1[0].url,
						name: 'file',
						formData: {
							user: 'test'
						},
						success: (res) => {
							getApp().globalData.imageUrl=res.data;
							//this.model1.imageUrl = res.data;
							setTimeout(() => {
								resolve(res.data.data)
							}, 1000)
						}
					});
				})
			},
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
