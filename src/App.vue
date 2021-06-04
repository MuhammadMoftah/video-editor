<template>
  <div id="app" style="padding-bottom: 50px">
    <!-- Error section -->
    <transition name="fade" appear>
      <div class="error" v-if="error == 'size'">
        Sorry, Video should be less than 500 MB
      </div>
    </transition>
    <transition name="fade" appear>
      <div class="error" v-if="error == 'name'">
        Sorry, Video must be only MP4 extensions
      </div>
    </transition>

    <!-- Upload section -->
    <section class="upload-section" v-if="!videoSrc">
      <label>
        <img src="./assets/upload-icon.svg" width="200px" alt="" />
        <h2>Upload Vidoe to Edit</h2>
        <input
          type="file"
          @change="changeVideo($event)"
          hidden
          accept="video/mp4"
        />
      </label>
    </section>

    <!-- Title section -->
    <section style="text-align: center" v-if="videoSrc">
      <h4 style="margin-bottom: 8px; color: black">
        Add Video Title
        <input type="color" class="title-color" v-model="titleColor" />
      </h4>
      <input type="text" v-model="videoTitle" maxlength="40" />
    </section>

    <!-- Video Section -->
    <section class="container" v-if="videoSrc">
      <video
        id="video"
        ref="video"
        controls
        :style="`filter: brightness(${brightness}%) contrast(${contrast}%) saturate(${saturate}%)`"
      >
        <source :src="videoSrc" type="video/mp4" />
        <source :src="videoSrc" type="video/ogg" />
        Your browser does not support HTML video.
      </video>

      <div class="overlay">
        <p :style="{ fontSize: '20px', color: titleColor }">{{ videoTitle }}</p>
      </div>

      <div class="video-setting" style="margin-top: 10px">
        <div style="width: 30%">
          <div>
            <img
              width="20"
              src="./assets/brightness.png"
              style="vertical-align: sub"
            />
            Brightness
          </div>
          <input
            max="180"
            min="20"
            type="range"
            v-model="brightness"
            style="width: 100%"
          />
        </div>

        <div style="width: 30%">
          <div>
            <img
              width="17"
              src="./assets/contrast.png"
              style="vertical-align: sub"
            />
            Contrast
          </div>

          <input
            max="180"
            min="17"
            type="range"
            v-model="contrast"
            style="width: 100%"
          />
        </div>

        <div style="width: 30%">
          <div>
            <img
              width="17"
              src="./assets/saturation.png"
              style="vertical-align: sub"
            />
            Saturation
          </div>

          <input
            max="180"
            min="0"
            type="range"
            v-model="saturate"
            style="width: 100%"
          />
        </div>
      </div>

      <div class="video-ratio" style="margin-top: 30px">
        <h4 style="margin-bottom: 8px; color: black">Video Ratio</h4>
        <button style="margin-right: 5px" @click="original()">Original</button>
        <button style="margin: 0 5px" @click="square()">1:1</button>
        <button style="margin: 0 5px" @click="rectangle()">16:9</button>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "App",
  components: {},
  data() {
    return {
      brightness: 100,
      contrast: 100,
      saturate: 100,
      videoSrc: "",
      file: "",
      OriginalHeight: "",
      OriginalWidth: "",
      videoTitle: "",
      titleColor: "#ffffff",
      error: false,
    };
  },
  methods: {
    changeVideo(event) {
      this.file = event.target.files[0];
      console.log("size", event.target.files[0].size);
      if (this.file.size > 500000000) {
        this.error = "size";

        setTimeout(() => {
          this.error = false;
        }, 3000);
        return;
      }

      if (this.file.name.split(".").pop() !== "mp4") {
        this.error = "name";
        setTimeout(() => {
          this.error = false;
        }, 3000);
        return;
      }

      this.error = false;
      const file = URL.createObjectURL(event.target.files[0]);
      this.videoSrc = file;
      setTimeout(() => {
        this.OriginalHeight = document.getElementById("video").clientHeight;
        this.OriginalWidth = document.getElementById("video").clientWidth;
      }, 500);
    },
    square() {
      // console.log(this.$refs.video.clientWidth);
      // console.log(this.$refs.video.clientHeight);
      console.log(document.getElementById("video").clientWidth);
      console.log(document.getElementById("video").clientHeight);
      this.$refs.video.style.width = this.OriginalHeight + "px";
      this.$refs.video.style.height = this.OriginalHeight + "px";
    },
    original() {
      this.$refs.video.style.width = this.OriginalWidth + "px";
      this.$refs.video.style.height = this.OriginalHeight + "px";
    },
    rectangle() {
      // 16:9
      const width = (this.OriginalHeight * 16) / 9;
      this.$refs.video.style.width = Math.ceil(width) + "px";
    },
  },
};
</script>

<style>
/* tajwal font  */
@import url("https://fonts.googleapis.com/css2?family=Tajawal:wght@200;300;400;500;700;800&display=swap");

/* Dancing Script font */
@import url("https://fonts.googleapis.com/css2?family=Dancing+Script:wght@400;500;600;700&family=Tajawal:wght@200;300;400;500;700;800&display=swap");
input[type="text"] {
  outline: none;
  border: 1px solid rgb(216, 216, 216);
  border-radius: 10px;
  padding: 10px;
  width: 100%;
  max-width: 400px;
  margin-bottom: 20px;
}
input:hover {
  background: rgb(247, 247, 247);
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
* {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  font-family: "Tajawal", sans-serif;
  /* font-family: 'Dancing Script', cursive; */
}

.container {
  width: 100%;
  max-width: 800px;
  display: block;
  margin: auto;
  position: relative;
  text-align: center;
}

.error {
  padding: 10px;
  border-radius: 10px;
  text-align: center;
  margin: auto;
  color: rgb(255, 76, 76);
  border: 2px dotted rgb(255, 76, 76);
  background: rgb(255, 195, 195);
  font-weight: 700;
  width: 95%;
  max-width: 500px;
  position: fixed;
  top: 100px;
  left: 0;
  right: 0;
  z-index: 999;
}
.upload-section {
  text-align: center;
}
.upload-section label {
  cursor: pointer;
  border: 2px dotted rgb(218, 218, 218);
  padding: 20px 50px;
  border-radius: 10px;
  display: block;
  margin: auto;
  margin-top: 27vh;
  width: fit-content;
}
.upload-section label:hover {
  background: rgb(243, 243, 243);
}

.container video {
  position: relative;
  z-index: 0;
  width: 100%;
  border-radius: 8px;
  background: black;
}
.overlay {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 1;
  right: 0;

  text-transform: capitalize;
  font-weight: bold;
  color: white;
  font-size: 20px;
  text-shadow: 1px 1px 1px black;
}
.title-color {
  border: 1px solid #ababab;
  background: #ececec;
  border-radius: 5px;
  margin: 0 10px;
  cursor: pointer;
  vertical-align: sub;
  width: 25px;
}
.video-setting {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
}
.video-setting > div div {
  margin-bottom: 3px;
}
.video-setting > div {
  text-align: left;
  font-size: 13px;
  font-weight: bold;
  color: rgb(61, 61, 61);
}
@media (max-width: 540px) {
  .video-setting > div {
    width: 100% !important;
    padding: 0 20px;
  }
}
button {
  padding: 5px 15px;
  border-radius: 5px;
  border: none;
  color: white;
  cursor: pointer;
  background: rgb(35, 123, 255);
  font-size: 20px;
}
button:hover {
  background: rgb(82, 151, 255);
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
