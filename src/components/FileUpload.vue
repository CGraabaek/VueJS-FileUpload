<template>
  <div
    v-cloak
    @drop.prevent="addFile"
    @dragover.prevent
    class="file-upload"
    id="file-upload"
    role="dialog">
    <h1>FileUpload</h1>
    <h4>Drag and drop one or more files here</h4>
    <ul>
      <li v-for="file in this.files">
        {{ file.name }} ({{ file.size | kb }} kb)
        <button @click="removeFile(file)" title="Remove">X</button>
      </li>
    </ul>

    <button v-if="!this.uploaded" :disabled="uploadDisabled" @click="upload">Upload</button>
    <p>{{status}}</p>
  </div>
</template>

<script>
export default {
  name: "FileUpload",
  data() {
    return {
      files: [],
      uploaded: false,
      status: ""
    };
  },
  computed: {
    uploadDisabled() {
      return this.files.length === 0;
    }
  },
  methods: {
    addFile(e) {
      let droppedFiles = Array.from(e.dataTransfer.files);
      if (!droppedFiles) return;
      // this tip, convert FileList to array, credit: https://www.smashingmagazine.com/2018/01/drag-drop-file-uploader-vanilla-js/
      [...droppedFiles].forEach(f => {
        this.files.push(f);
      });
    },
    removeFile(file) {
      this.files = this.files.filter(f => {
        return f != file;
      });
    },
    upload() {
      let formData = new FormData();
      this.files.forEach((f, x) => {
        formData.append("file" + (x + 1), f);
      });

      fetch("https://httpbin.org/post", {
        method: "POST",
        body: formData
      })
        .then(res => res.json())
        .then(res => {
          this.uploaded = true;
          this.status = "File uploaded successfully";
          console.log("done uploading", res);
        })
        .catch(e => {
          console.error(JSON.stringify(e.message));
        });
    }
  }
};
</script>

<style scoped>
h1,
h2 {
  font-weight: normal;
}
a {
  color: #42b983;
}

.file-upload {
  background-color: #42b983 !important;
  display: block;
  position: fixed;
  z-index: 10001;
  left: 50%;
  top: 38.1966%;
  max-height: 90%;
  box-sizing: border-box;
  width: auto;
  transform: translate(-50%, -38.1966%);
  overflow: auto;
  background-color: white;
  padding: 20px;
  border-radius: 5px;
}
</style>
