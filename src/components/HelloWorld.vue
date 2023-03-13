<template>
  <v-card
    title="圖片網址產生器"
    class="mx-auto mt-4"
    subtitle="將您的圖片變為 imgur 的網址"
    text="您可以選擇用下方區塊上傳或將圖片拖放或直接貼上到此區塊！"
    width="calc(100% - 32px)"
    max-width="500"
    variant="outlined"
    :loading="loading"
    @paste="pasteImage"
  >
    <v-card-text id="dropmom">
      <div
        class="drop-container"
        :class="{ droping }"
        @dragover="droping = true"
        @dragleave="droping = false"
        @dragend="droping = false"
        @drop="onFileChange"
      >
        <div class="drop-message">
          <div class="drop-message-icon">
            <v-icon icon="mdi-cloud-upload"></v-icon>
          </div>
          <div class="drop-message-text">拖曳圖片到此處或點擊此處上傳</div>
        </div>
        <input type="file" id="fileupload" ref="fileupload" @change="onFileChange" accept="image/*" />
      </div>
    </v-card-text>
  </v-card>
  <meowCard :url="uploadFinish.url" v-for="uploadFinish in uploadFinishs" :key="uploadFinish.title"></meowCard>
</template>
<style scoped>
  .drop-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    position: relative;
    border: 2px dashed #ccc;
    padding: 16px;
    border-radius: 4px;
    min-height: 200px;
  }
  .drop-container.droping {
    border-color: #2196f3;
  }
  .drop-message {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  .drop-message-icon {
    font-size: 3rem;
    color: #ccc;
  }
  .drop-message-text {
    font-size: 1.5rem;
    color: #ccc;
  }
  .drop-container input[type='file'] {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    opacity: 0;
    cursor: pointer;
  }
</style>
<script>
import meowCard from './myCard.vue'

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
      droping: false,
    }
  },
  methods: {
    pasteImage(e) {
      const paste_file = e.clipboardData.items[0].getAsFile()
      if (paste_file != null && paste_file.type.startsWith("image")) {
        this.submit({
          file: paste_file,
          filename: paste_file.name
        })
      }
    },
    onFileChange(e) {
      this.droping = false;
      const files = e.target.files || e.dataTransfer.files;
      if (!files.length) return;
      this.submit({
        file: files[0],
        filename: files[0].name
      })
    },
    submit({ file, filename }) {
      if (file == null) return;
      console.log(file)
      //this.$refs.fileupload.value = null;
      const token = "35ac59e22bb084643ec26d0d179365f7ad9e2c5d";

      let form = new FormData();
      form.append('image', file);
      // form.append('title', filename);
      //form.append('description', this.des);

      this.loading = true;
      this.disabled = true;
      fetch("https://api.imgur.com/3/image", {
        body: form,
        headers: {
          'Authorization': `Bearer ${token}`
        },
        method: 'POST'
      })
        .then(resp => resp.json())
        .then(res => {
          console.log(res.data.link)
          this.uploadFinishs.unshift({
            title: filename,
            url: res.data.link
          })

          this.loading = false
          this.disabled = false
        })
        .catch(err => {
          console.log(err)
          this.loading = false
          this.disabled = false
        })
    },
  },

}
</script>
