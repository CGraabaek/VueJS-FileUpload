# VueJS File Upload



File `src/main.js` add filter to show how big the file is.
```javascript
Vue.filter('kb', val => {
  return Math.floor(val/1024);  
});
```
In the file  `src/components/FileUpload.vue` we add the file functionality. 
First is the HTML Template:
```HTML
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

});
```

Next step is the script functions.

First there is the data properties

```javascript
  data() {
    return {
      files: [],
      uploaded: false,
      status: ""
    };
  }
```
The computed values
```javascript
 computed: {
    uploadDisabled() {
      return this.files.length === 0;
    }
  }
```

And then the methods for handling the file upload.

```javascript
 methods: {
    addFile(e) {
      let droppedFiles = Array.from(e.dataTransfer.files);
      if (!droppedFiles) return;
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
```

There  is included a dummy fileuoload to httpbin, just to show how to upload the files.


## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```