 <template>
  <div id="app">
    <form>
      <editor :api-key="getTinyConfig()" v-model="content" ref="img2" v-on:onChange="this.update" :init="{
        height: 500,
        menubar: true,
        file_picker_types: 'image',
        automatic_uploads: true,
        file_picker_callback: file_picker_callback,
        plugins: [
          'advlist autolink lists link image charmap print preview anchor',
          'searchreplace visualblocks code fullscreen',
          'insertdatetime media table paste code help wordcount '
        ],
        toolbar:
          'undo redo | formatselect | bold italic backcolor| link image| \
                                                           alignleft aligncenter alignright alignjustify | \
                                                           bullist numlist outdent indent | removeformat | help',
      
        content_style: 'body { font-family:Helvetica,Arial,sans-serif; font-size:14px }'
      
      }" />
    </form>
  </div>
</template>


 <script>
 import Editor from '@tinymce/tinymce-vue'
 
 
 export default {
   name: 'app',
   components: {
     'editor': Editor
   },
 
   data: function () {
     return {
       content: this.value // default to the passed value
     }
 
   },
   methods: {
     file_picker_callback: function (cb, value, meta) {
 
       var input = document.createElement('input');
       input.setAttribute('type', 'file');
       input.onchange = function () {
         var file = this.files[0];
         var reader = new FileReader();
 
         // FormData
         var fd = new FormData();
         var files = file;
         fd.append('filetype', meta.filetype);
         fd.append("file", files);
         var filename = "";
         var xhr;
         xhr = new XMLHttpRequest();
         xhr.withCredentials = false;
         xhr.open('POST', 'http://localhost:8000/api/teste');
 
         xhr.setRequestHeader("X-CSRF-Token", '545454');
 
         xhr.onload = function () {
 
           if (xhr.status != 200) {
             alert('HTTP Error: ' + xhr.status);
             return;
           }
 
           filename = xhr.responseText;
           reader.onload = function () {
             cb(filename);
           };
 
           cb(filename, { title: file.name });
           reader.readAsDataURL(file);
         };
         xhr.send(fd);
         return
       };
 
       input.click();
     },
     getTinyConfig: function () {
       return "72epzj4jvgzja4b2u9aclyk2twbeorrb25d3l5ky962nyfov"
     },
 
     update: function () {
       // pass updated content back to the parent
       console.log(this.content)
       this.$emit('input', this.content);
     }
   }
 }
 </script>
