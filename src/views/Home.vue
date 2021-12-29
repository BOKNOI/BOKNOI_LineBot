<template>
  <div class="home container">

    <div class="camera">
      <video class="video" muted autoplay playsinline></video>
    </div>

    <canvas width="1080" height="1920" class="picture" v-if="this.picture"></canvas>

    <div class="search">
      <input class="searchInput form-control" name="กรอกชื่อร้านที่ต้องการค้นหา" placeholder=" บอกร้านที่อยากหามาได้เลย">
      <button type="button" class="confirmInput" name="ค้นหา">
        find
      </button>
    </div>

    <div class="preview" v-if="this.url">
      <button class="delete" v-on:click="deletePic()">X</button>
      <img v-if="this.url" :src="this.url" />
    </div>

    <button class="snap" name="ถ่ายรูป" v-on:click="takePicture()">
        <img class="capture" src="../assets/capture.svg">
    </button>

    <!-- <div class="openGallery">
      <div class="informSize" style="text-align: left; margin-left: 4%">
        width : {{ this.img.width }} height : {{ this.img.height }} <br>
        latitude : {{ this.location.latitude }} longtitude: {{ this.location.longitude }} <br>
        accuracy : {{ this.location.accuracy }} <br>
        id: {{ this.lineId }} <br>
      </div>
    </div> -->

      <label class="gallery" for="upload"></label>
      <input class="upload" name="เลือกรูปภาพจากคลัง" id="upload" type="file" accept="image/*" @change="fileUpload( $event )">
  </div>
</template>

<script>
import liff from '@line/liff';

export default {
  name: 'Home',
  data () {
    return {
      picture: "",
      upLoad: "",
      url: "",
      img: {
        width: "",
        height: "",
        image: ""
      },
      imageBase64: null,
      lineId: "",
      lineName: "",
      location: {
        latitude: "",
        longitude: "",
        accuracy: ""
      },
    }
  },
  methods: {
    async init () {
      if(navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
        let constraints = {
          video: true,
          audio: false
          // video: {
          //   width: {
          //     min: 1280,
          //     // min: 640,
          //     // ideal: 1280,
          //     // max: 1920,
          //   },
          //   height: {
          //     min: 720,
          //     // min: 360,
          //     // ideal: 720,
          //     // max: 1080
          //   }
          // }
        };
        await navigator.mediaDevices.getUserMedia(constraints).then(stream => {
          const videoPlayer = document.querySelector("video");
          videoPlayer.srcObject = stream;
          // videoPlayer.play();
        });
      } else {
        alert("cannot get media devices : ", navigator.mediaDevices);
        // navigator.getDisplayMedia(displayMediaStreamConstraints).then(success).catch(error);
      }
    },
    runApp () {
      liff.getProfile().then(lineProfile => {
        this.lineId = lineProfile.userId;
        this.lineName = lineProfile.displayName;
      })
    },
    async takePicture () {
      // const context = canvas.getContext("2d");
      // this.img.height = window.innerWidth;
      // this.img.width = window.innerHeight;
      // console.log("w : ", window.innerWidth, " h : ", window.innerHeight);
      // context.imageSmoothingEnabled = true;
      // context.imageSmoothingQuality = "high";
      // context.drawImage(document.querySelector(".feed"), 0, 0, this.img.width, this.img.height);
      // let image = document.createElement('img');
      // image.src = canvas.toDataUTL('image/png');
      // console.log(image)
      this.picture = "doing";

      const video = document.querySelector("video");
      const canvas = document.querySelector("canvas");
      console.log(canvas, video)
      // canvas.width = 360;
      // canvas.height = 480;
      canvas.getContext('2d').drawImage(video, 0, 0, canvas.width, canvas.height);
      
      this.picture = canvas;
      console.log(this.picture, canvas.width, canvas.height);
      // liff.closeWindow();
    },
    fileUpload( event ) {
      this.upLoad = event.target.files[0];
      this.url = URL.createObjectURL(this.upLoad);
      console.log(this.url, "\n", event.target.files[0]);
    },
    deletePic() {
      this.url = "";
    }
  },
  beforeMount () {
    this.init();

    navigator.geolocation.getCurrentPosition(pos => {
      let gps = pos;
      this.location.latitude = gps.coords.latitude;
      this.location.longitude = gps.coords.longitude;
      this.location.accuracy = gps.coords.accuracy;
      console.log("GPS: ", this.location.latitude, this.location.longitude);
    });
  },
  // async created () {
  //   await liff.init({ 
  //     liffId: "1656567977-bEZ7Xpwd" 
  //   })
  //   .then(() => {
  //     if(liff.isLoggedIn()) {
  //       this.runApp();
  //     } else {
  //       liff.login();
  //       this.runApp();
  //     }
  //   })
  //   .catch((err) => {
  //     console.log(err.code, err.message);
  //     liff.closeWindow();
  //   });
    
  // }
}
</script>

<style>
.camera {
  display: flex;
  position: absolute;
  width: 100%;
  height: 100%;
  /* background: #111113; */
  z-index: -1;
}

.video {
  display: flex;
  position: absolute;
  width:100%;
  height: 100%;
  background: #111113;
  z-index: -1;
}

.picture {
  width: 50vw;
  height: 50vh;
  /* margin-top: 20%; */
  object-fit: contain;
}

.search {
  position: fixed;
  display: flex;
  justify-content: center;
  align-items: flex-end;
  top: 0%;
  left: 0%;
  width: 100%;
  height: 8vh;
  /* background-color: transparent; */
  /* background-color: aquamarine; */
}

.searchInput {
  height: 36px;
  width: 55%;
  box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.25);
  border-radius: 6px;
  border-color: transparent;
  /* border-color: greenyellow; */
  margin-right: 10px;
  padding: 0% 12px 0% 12px;
}

.confirmInput {
  height: 40px;
  width: 20%;
  cursor: pointer;

  font-weight: bold;
  font-size: 18px;
  line-height: 21px;
  color: #FFFFFF;

  background: #161a8b;
  box-shadow: 0px 4px 4px rgba(112, 112, 112, 0.1);
  border-radius: 6px;
}

.preview {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.delete {
  background: #FFFFFF;
  color: red;
  font-size: 24px;
}

.preview img {
  width: 60%;
}

.snap {
  background-color: transparent;
  /* background-color: cadetblue; */
  background-repeat: no-repeat;
  overflow: hidden;
  /* outline: none; */
  /* opacity: 0.8; */
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  position: fixed;
  padding: 0%;
  margin: 0%;
  bottom: 0%;
  left: 0%;
  width: 100%;
  height: 45vh;
  z-index: 1;
}

.capture {
  margin-bottom: 5%;
}

/* .openGallery {
  background-color: transparent;
  border: none;
  overflow: hidden;
  display: flex;
  align-items: center;
  position: fixed;
  width: 100%;
  height: 10vh;
  bottom: 0%;
  z-index: 1;
  color: white
}  */

.gallery {
  position: fixed;
  width: 55px;
  height: 55px;
  background-image: url(../assets/gallery.svg);
  background-repeat: no-repeat;
  right: 4%;
  bottom: 1%;
  z-index: 2;
}

.gallery:active {
  opacity: 0.2;
}

.upload {
  display: none;
}
</style>