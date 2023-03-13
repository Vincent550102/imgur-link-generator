<template>
  <v-container>
    <v-card class="mx-auto" max-width="500">
      <v-img class="align-end text-white" height="200" :src="url" cover>
        <!-- <v-card-title>Top 10 Australian beaches</v-card-title> -->
      </v-img>
      <v-card-text>
        <div>{{ url }}</div>
      </v-card-text>

      <v-card-actions class="float-right">
        <v-btn color="orange" @click="copyText(url, $event)">
          <v-icon icon="mdi-content-copy"></v-icon>
        </v-btn>

        <v-btn color="orange">
          <v-icon icon="mdi-download"></v-icon>
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-container>
</template>
<script>
export default {
  props: {
    url: String
  },
  methods: {
    copyText(u) {
      console.log(u)
      if (navigator.clipboard && window.isSecureContext) {
        // navigator clipboard api method'
        return navigator.clipboard.writeText(u);
      } else {
        // text area method
        let textArea = document.createElement("textarea");
        textArea.value = u;
        // make the textarea out of viewport
        textArea.style.position = "fixed";
        textArea.style.left = "-999999px";
        textArea.style.top = "-999999px";
        document.body.appendChild(textArea);
        textArea.focus();
        textArea.select();
        return new Promise((res, rej) => {
          // here the magic happens
          document.execCommand('copy') ? res() : rej();
          textArea.remove();
        });
      }
    }
  }
}
</script>
