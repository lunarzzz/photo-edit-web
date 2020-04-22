<template>
  <div class="hello">
    <!-- <img src="../assets/DSC00245的副本.jpg" id="test"> -->
    <!-- <input type="button" v-on:click="get()" value="点我异步获取数据(Get)"> -->
    <!-- <Blog></Blog> -->
<input type="file" @change="onFileChanged">
<img :src="imgUrl">


<button @click="onUpload">Upload!</button>
<button @click="onRecord">Record!</button>
<button @click="onPause">Pause!</button>
<button @click="downloadAudio">DownloadAudio</button>
<br/>
<button @click="startMusic">StartMusic {{isEncrypted}}</button>
<input v-model="password" placeholder="please input password!"/>{{password}}

<input type="checkbox" id="IsEncrypted" v-model="isEncrypted">
<label for="checkbox">{{ isEncrypted }}</label>
<br/>
<input v-model="audioId" placeholder="please input audio id!"/>{{audioId}}
<button @click="onGetAudioById">Get Audio!</button>

<!-- 全局音效 -->
<!-- <audio src="" id="eventAudio">My Audio</audio> -->
  </div>
</template>

<script type = "text/javascript">
import axios from "axios"
// import Blog from "@/components/Blog"
import Recorderx, { ENCODE_TYPE } from "recorderx";
export default {
  name: 'HelloWorld',
  components: {
    // Blog,
  },
  data: function() {
    return {
      rmsg: 15,
      msg: 'Welcome to My World',
      selectedFile: null,
      imgUrl: null,
      rc : new Recorderx(),
      wav : null,
      password : null,
      isEncrypted: false,
      audioId : null
    }
  },
  methods: {
    onEncrypted() {
      this.isEncrypted = true
    },
    onNoEncrypted() {
      this.isEncrypted = false
    },
    onGetAudioById() {
      console.log('audioId' + this.audioId)
      axios.get("http://localhost:8081/audio", {
        params: {
          audioId : this.audioId,
          isEncrypted : this.isEncrypted,
          password : this.password
        }
      }).then(res => {
        let audio = new Audio()
        // console.log("audio" + res.data.audio)
        // console.log("audio" + res.data)

        var url = 'data:audio/wav;base64,' + new Buffer(res.data.audio).toString('base64')
        // var url = URL.createObjectURL(res.data.audio);
        console.log(url)
        audio.src = url
        audio.play();
      })
    },
    startMusic() {
      let audio = new Audio()
      var url = URL.createObjectURL(this.wav);
      audio.src = url
      audio.play();
    },
    get: function() {
      axios
        .get('http://localhost:8081/age')
        .then(response=>(this.rmsg=response.data))
    },
    onFileChanged (event) {
        // const file = event.target.files[0]
        this.selectedFile = event.target.files[0]
    },
    onRecord() {
      this.rc.start()
      .then(() => {
      console.log("start recording");
  })
  .catch(error => {
    console.log("Recording failed.", error);
  });
    },
    onPause() {
      // pause recorderx
        this.rc.pause();

      // get wav, a Blob
      this.wav = this.rc.getRecord({
        encodeTo: ENCODE_TYPE.WAV,
        compressible: true
      });
      /*eslint no-console:*/
      console.log(this.wav)
    },
    downloadAudio() {
      var downloadElement = document.createElement('a')  
      /*eslint no-console:*/
      console.log(this.wav)      
      var href = URL.createObjectURL(this.wav) // 创建下载的链接        
      downloadElement.href = href        
      document.body.appendChild(downloadElement)        
      downloadElement.click() // 点击下载        
      document.body.removeChild(downloadElement) // 下载完成移除元素  
      // window.URL.revokeObjectURL(href) // 释放掉blob对象  
    },
    async onUpload() {
        // upload file
        const formData = new FormData();
        formData.append('myFile', this.selectedFile, this.selectedFile.name);
        formData.append('myAudio', this.wav, this.wav.name);
        formData.append('password', this.password)
        formData.append('isEncrypted', this.isEncrypted)
        // start recorderx
// // get wav, but disable compression
// var wav = rc.getRecord({
//   encodeTo: ENCODE_TYPE.WAV
// });
        axios.post('http://localhost:8081/fileUpload', formData).then(response=> {
            /*eslint no-console:*/
            //将从后台获取的图片流进行转换
            // var img = new Image();
            // 'data:image/jpg;base64,' + 
            console.log(response.data)
            return 'data:image/jpeg;base64,' + btoa(
            new Uint8Array(response.data).reduce((data, byte) => data + String.fromCharCode(byte), '')
          );
            
        }).then(data => {
            console.log(this.imgUrl + data)   // document.body.appendChild(img);
            console.log('end') 
        })
    }
  }
  }

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#test {
  width: 100%;
  height: 30%;
}
</style>
