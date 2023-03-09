<template>
  <v-card title="圖片網址產生器" class="mx-auto mt-4" subtitle="將您的圖片變為 imgur 的網址" text="您可以選擇用下方區塊上傳或將圖片拖放或直接貼上到此區塊！"
    max-width="500" variant="outlined" :loading="loading" @paste="pasteImage">
    <v-card-text>
      <v-file-input :disabled="disabled" accept="image/png, image/jpeg, image/bmp" placeholder="Upload your images!"
        prepend-icon="mdi-image" label="Image" density="compact" @change="showFile"></v-file-input>
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
import meowCard from './Card.vue'
export default {
  components: {
    meowCard
  },
  data() {
    return {
      file: null,
      fs: {
        name: '',
        thumbnail: null,
        size: null
      },
      title: '',
      des: '',
      loading: false,
      disabled: false,
      uploadFinishs: []
    }
  },
  methods: {
    pasteImage(e) {
      const paste_file = e.clipboardData.items[0].getAsFile()
      if (paste_file != null && paste_file.type.startsWith("image")) {
        this.file = paste_file
        this.fs.name = this.file.name
        this.fs.size = Math.floor(this.file.size * 0.001) + 'KB';
        this.fs.thumbnail = window.URL.createObjectURL(this.file);
        this.title = this.fs.name;
        this.submit()
      }
    },
    showFile(e) {
      this.file = e.target.files[0];
      this.fs.name = this.file.name;
      console.log(this.fs.name)
      this.fs.size = Math.floor(this.file.size * 0.001) + 'KB';
      this.fs.thumbnail = window.URL.createObjectURL(this.file);
      this.title = this.fs.name;
    },
    submit() {
      console.log(this.title);
      const token = "35ac59e22bb084643ec26d0d179365f7ad9e2c5d";

      let form = new FormData();
      form.append('image', this.file);
      //form.append('title', this.title);
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
            title: this.title,
            url: res.data.link
          })
          this.loading = false
          this.disabled = false
        });
    }
  },

}
</script>
