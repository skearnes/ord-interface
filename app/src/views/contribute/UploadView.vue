<script>

export default {
  data() {
    return {
      uploadFile: {
        name: null,
        loading: false,
        value: null,
      }
    }
  },
  methods: {
    async setFile(e) {
      // converts uploaded file into useable array buffer
      const files = e.target.files || e.dataTransfer.files
      if (!files.length) return console.error('No file')
      this.uploadFile.loading = true
      this.uploadFile.name = files[0].name
      const fileReader = new FileReader()
      fileReader.onload = readerEvent => {
        this.uploadFile.value = readerEvent.target.result
        this.uploadFile.loading = false
      }
      fileReader.readAsArrayBuffer(files[0])
    },
    submitUpload() {
      if (this.uploadFile.loading)
        return alert("Files are still processing. Please try again in a moment.")
      else if (!this.uploadFile.value)
        return alert("You must upload a file for the dataset before submitting.")
      // send dataset file to api for upload
      const xhr = new XMLHttpRequest();
      xhr.open('POST', `root + 'dataset/${this.uploadFile.name}/upload`);
      const payload = this.uploadFile.value
      xhr.onload = () => {
        if (xhr.status === 200) {
          location.reload();
        } else {
          alert('Error: ' + xhr.response);
        }
      }
      // Attempt to catch timeouts.
      xhr.onerror = () => {
        alert('Error: request failed (possibly due to timeout)');
      }
      xhr.send(payload);
    }
  },
}
</script>

<template lang="pug">
.upload
  .subtitle Upload dataset file:
  .file-picker
    .input
      label(for='upload') Dataset filename:
      input#template(
        type='file'
        accept='.pbtxt,.pb'
        v-on:change='(e) => setFile(e)'
      )
  .submit
    button#upload-submit(@click='submitUpload') Submit Upload
  .copy Lorem ipsum dolor sit amet, consectetur adipiscing elit. Donec tincidunt eros eget magna venenatis, nec volutpat justo ornare. Praesent mauris enim, dignissim nec posuere ut, mollis at lacus. Phasellus ut ultricies arcu, ultrices efficitur enim. Donec pharetra turpis nulla, id elementum felis fermentum sit amet. In id imperdiet sapien. Quisque vulputate odio eget ex aliquet aliquam. Vestibulum a metus magna.
</template>

<style lang="sass" scoped>
.subtitle
  font-size: 1.5rem
.upload
  padding-top: 1rem
  .file-picker
    padding: 1rem 0
    display: flex
    flex-wrap: wrap
    row-gap: 1rem
    .input
      label
        margin-right: 0.5rem
  .submit
    margin-bottom: 2rem
  .copy
    margin-top: 1rem
</style>