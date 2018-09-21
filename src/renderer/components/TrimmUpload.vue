<template lang="html">
  <div class="">
    <div class="title-small">
      Upload Assets
    </div>
    <div class="upload-asset">
      <div class="input-and-upload">
        <input type="file" @change="processFileInput" id="file-input">
        <label class="text" for="file-input">{{ fileName }}</label>
        <a class="upload-button" :class="{ 'clickable' : fileIsSelected }" @click.prevent="uploadAsset">Upload</a>
      </div>
      <textarea class="description-input" rows="4" cols="53" placeholder="Add a description to your asset"></textarea>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      fileIsSelected: false,
      fileName: 'Choose a file to upload...'
    }
  },
  methods: {
    processFileInput(event) {
      this.fileIsSelected = true;
      console.log(event.target.files[0])
      this.fileName = event.target.files[0].name
    },
    uploadAsset() {
      
    }
  }
}
</script>

<style lang="css">
.upload-asset {
  width: 90%;
  margin: auto;
}

.input-and-upload {
  margin-bottom: 5px;
}

#file-input {
  display: none;
}

.upload-button {
  float: right;
  color: #DCDCDC;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
}

.clickable {
  color: white;
}

.description-input {
  border: none;
  width: 100%;
  border-radius: 4px;
  background-color: #392c9f;
  color: white;
  font-weight: 500;
  font-size: 13px;
}

.description-input::placeholder {

}
</style>
