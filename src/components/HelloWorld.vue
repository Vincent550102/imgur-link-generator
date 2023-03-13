<template>
	<v-card title="圖片網址產生器" class="mx-auto mt-4" subtitle="將您的圖片變為 imgur 的網址" text="您可以選擇用下方區塊上傳或將圖片拖放或直接貼上到此區塊！"
		max-width="500" variant="outlined" :loading="loading" @paste="pasteImage">
		<v-card-text id="dropmom">
			<!-- <v-file-input :disabled="disabled" v-model="fileInput" accept="image/png, image/jpeg, image/bmp" -->
			<!-- 	placeholder="Upload your images!" prepend-icon="mdi-image" label="Image" density="compact" -->
			<!-- 	@change="showFile"></v-file-input> -->
			<!-- <DropZone :acceptedFiles="['image']" v-model="fileInput" :uploadOnDrop="true" :maxFiles="1" @addedFile="showFile" /> -->
			<!-- <form action="/file-upload" id="form1" class="dropzone" @addedFile="dropImage"></form> -->
			<!-- <vue-dropzone ref="myVueDropzone" id="dropzone" :options="dropzoneOptions"></vue-dropzone> -->
			<form action="/" method="get" class="dropzone" id="myGreatDropzone"></form>
		</v-card-text>
		<v-card-actions class="float-right">
			<v-btn @click="submit"><v-icon icon="mdi-check"></v-icon>
			</v-btn>
		</v-card-actions>
	</v-card>
	<v-card max-width="400" class="mx-auto" v-if="uploadFinishs.length !== 0">
		<meowCard :url="uploadFinish.url" v-for="uploadFinish in uploadFinishs" v-bind:key="uploadFinish.title">
		</meowCard>
	</v-card>
	<!-- <meowCard /> -->
</template> 
<script>
import meowCard from './myCard.vue'
var default_file = null, default_fs_name = '', default_title = ''
// TODO
// 1. 新增 dropzone 上傳完成後自動送出的功能
// 2. 修好送出後將 dropzone 清空的功能
// eslint-disable-next-line
Dropzone.options.myGreatDropzone = { // camelized version of the `id`
	paramName: "file", // The name that will be used to transfer the file
	maxFilesize: 2, // MB
	acceptedFiles: "image/*",
	method: "get",
	maxFiles: 1,
	accept: function (file) {
		default_file = file
		default_fs_name = default_file.name
		default_title = default_file.name;
		this.submit()
	}
};


export default {
	components: {
		meowCard,
	},
	data() {
		return {
			des: '',
			loading: false,
			disabled: false,
			uploadFinishs: [],
			fileInput: null,
		}
	},
	methods: {
		pasteImage(e) {
			const paste_file = e.clipboardData.items[0].getAsFile()
			if (paste_file != null && paste_file.type.startsWith("image")) {
				default_file = paste_file
				default_fs_name = default_file.name
				default_title = default_fs_name;
				this.submit()
			}
		},
		showFile(e) {
			default_file = e.file;
			default_fs_name = default_file.name;
			default_title = default_fs_name;
		},
		submit() {
			if (default_file == null) return;
			//this.$refs.fileupload.value = null;
			const token = "35ac59e22bb084643ec26d0d179365f7ad9e2c5d";

			let form = new FormData();
			form.append('image', default_file);
			//form.append('title', default_title);
			//form.append('description', this.des);

			this.loading = true;
			this.disabled = true;
			fetch("https://api.imgur.com/3/image", {
				body: form,
				headers: {
					'Authorization': 'Bearer ' + token
				},
				method: 'POST'
			})
				.then(resp => resp.json())
				.then(res => {
					console.log(res.data.link)
					this.uploadFinishs.unshift({
						title: default_title,
						url: res.data.link
					})

					this.loading = false
					this.disabled = false
				});
		},
	},

}
</script>
