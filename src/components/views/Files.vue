<template>
  <section class="content">
    <div class="row center-block">
      <form role="form">
        <div class="box-body">
          <div class="form-group">
            <label for="exampleInputFile">File input</label>
            <input type="file" id="exampleInputFile" accept="*.jpg, *.png" @change="uploadFile($event)" >
            <img width="100" v-bind:src="imagePreview" :show="showImage">
          </div>
        </div>
        <!-- /.box-body -->
      </form>
    </div>
  </section>
</template>

<script>
import $ from 'jquery'

export default {
  name: 'files',
  data(){
    return {
      showImage: true,
      imagePreview: "https://v1.vuejs.org/images/logo.png"  // we will store base64 format of image in this string
    }
  },
  methods: {
    uploadFile (event) {
      var file = event.target.files[0]
      if (file.size > 3072000) {
        alert("File is too big!");
        return;
      }
      // console.log(file.name)
      var reader = new FileReader()
      var _this = this
      reader.addEventListener('loadend', function(){

        let result = reader.result

        _this.imagePreview = result
        var request = new XMLHttpRequest()
        request.open('POST', 'https://pq1natvbk8.execute-api.ap-southeast-1.amazonaws.com/dev/v1/s3drive/storage')
        let data = {
          'fileName': file.name,
          'fileData': result
        }

        request.send(JSON.stringify(data))
        request.onreadystatechange = function() { // Call a function when the state changes.
            if (this.readyState === 4 && this.status != 200) {
              alert('Error: ' + this.status);
            }
            if (this.readyState === 4 && this.status === 200) {
                _this.imagePreview = 'http://' + JSON.parse(this.response)['cloudfront']
                alert('Upload sucessfully!: ');
            }
        }
      }.bind(this), false)

      if (file) {
        reader.readAsDataURL(file)
      }
    }

  },
  mounted () {
    this.$nextTick(() => {
    })
  }
}
</script>
