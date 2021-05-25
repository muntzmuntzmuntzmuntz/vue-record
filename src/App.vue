<template>

  <div id="app">
    <head>
  <link href="https://vjs.zencdn.net/7.11.4/video-js.css" rel="stylesheet" />

</head>
    <main class="container has-text-centered">
      <section id="example-audio">
        <div class="columns">
          <div class="column">
            <div class="has-text-right">
              <h3 class="title is-3">Recording audio files</h3>
              <p class="subtitle">Simply <strong>{{recordMode.audio}}</strong> the button to record an audio clip</p>
            </div>

            <div class="record-settings">
              <vue-record-audio :mode="recordMode.audio" @stream="onStream" @result="onResult" />
            </div>
          </div>
          <div class="column">
            <div class="recorded-audio">
              <div v-for="(record, index) in recordings" :key="index" class="recorded-item">
                <div class="audio-container"><audio :src="record.src" controls /></div>
                <div><button @click="removeRecord(index)" class="button is-dark">delete</button></div>
              </div>
            </div>
          </div>
        </div>
      </section>

      <section id="example-video">
        <div class="columns">
          <div class="column">
            <div class="has-text-right">
              <h3 class="title is-3">Recording video files</h3>
              <p class="subtitle">Simply <strong>{{recordMode.video}}</strong> the button to record a video clip</p>
            </div>

            <div class="record-settings">
              <vue-record-video mode="press" @stream="onVideoStream" @result="onVideoResult" />
            </div>
          </div>
          <div class="column">
            <div class="recorded-video" v-if="videoRef">
              <videoCut :video_options="video_options" :video_offset_ms="video_offset_ms"  />
            </div>
          </div>
        </div>
      </section>
    </main>

  </div>
</template>

<script>
import Vue from 'vue'
import videoCut from 'vue-video-cut'

Vue.use(videoCut)
export default {
  name: 'app',
  data () {
    return {
      videoRef: null,
      recordMode: {
        audio: 'press',
        video: 'press'
      },
      recordings: [],
      video_options: {
        width: 600,
        height: 250,
      },
      video_offset_ms: {
        start_ms: 0,
        end_ms: 6000,
      },
    }
  },
  methods: {
    removeRecord (index) {
      this.recordings.splice(index, 1)
    },
    onStream (stream) {
      console.log('Got a stream object:', stream);
    },
    onVideoStream (stream) {
      console.log('Got a video stream object:', stream);
    },
    onVideoResult (data) {
      this.video_options.src = null;
      this.videoRef =  window.URL.createObjectURL(data);
      this.video_options.src = this.videoRef;
      console.log(this.videoRef)      
    },
    onResult (data) {
      this.recordings.push({
        src: window.URL.createObjectURL(data)
      })
    },
  }
}
</script>

<style lang="scss">
html, body, #app {
  width: 100%;
  height: 100%;
}
#app {
  display: flex;
  flex-direction: column;
  main{
    height: 100%;
  }

  strong, a {
    color: #41b883 !important;
  }

  section {
    margin: 40px 0;
  }
}

.header {
  padding: 20px 10px;
  margin-bottom: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  .navbar-brand img {
    height: 64px;
    margin-right: 20px;
    width: 64px;
  }
}

.vue-audio-recorder, .vue-video-recorder {
  margin-right: 16px;
}

.record-settings {
  margin-top: 16px;
  display: flex;
  justify-content: flex-end;
}

.recorded-audio {
  margin: 0 auto;
  height: 200px;
  overflow: auto;
  border: thin solid #eee;
  background-color: #f7f7f7;
  padding: 10px 5px;
  .recorded-item {
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 16px;
  }
  .audio-container {
    display: flex;
    height: 54px;
    margin-right: 16px;
  }
}

.recorded-video {
  video {
    width: 100%;
    max-height: 400px;
  }
}

.footer {
  background-color: #eee;
}
</style>
