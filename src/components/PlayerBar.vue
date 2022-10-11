<template>
  <div class="player-bar" v-if="track != null">
    <div class="player-bar__container">
      <div class="player-bar__track-info">
        <img class="player-bar__cover" :src="track.cover">
        <div class="player-bar__track-info__name-author">
          <span class="player-bar__track-name">{{track.name}}</span>
          <br>
          <span class="player-bar__track-author">{{track.author}}</span>
        </div>
        <div class="player-bar__container__controls">
          <button class="play" @click="play">
            <img src="../assets/icons/play.svg">
          </button>
          <br>
          <div class="player-bar__bars">
            <span @click="scroll" class="player-bar__track__duration" id="player-bar__track__duration"></span>
            <span @click="scroll" class="player-bar__track__listened" id="player-bar__track__listened"></span>
          </div>
        </div>
      </div>
      <audio class="player-bar__track" id="player-bar__track">
        <source :src="track.source" :type="track.type">
      </audio>

      <div class="player-bar__volume">
        <img class="player-bar__volume__icon" src="../assets/icons/volume.svg">
        <div class="controls">
          <span @click="changeVolume" class="player-bar__volume__full" id="player-bar__volume__full"></span>
          <span @click="changeVolume" class="player-bar__volume__current" id="player-bar__volume__current"></span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "PlayerBar",

  props: {
    track: {
      type: Object,
      default: null,
    }
  },

  methods: {
    play() {
      const currentTrack = document.getElementById("player-bar__track");

      if (currentTrack.paused) {
        currentTrack.play();
        this.changeListenedBar();
        currentTrack.onended = this.trackEnded;
      }
      else {
        currentTrack.pause();
        this.stopChanging()
      }
    },

    data() {
      return {
        timer: null,
        muted: false,
        lastVolume: 0,
      }
    },

    scroll(event) {
      const currentTrack = document.getElementById("player-bar__track");
      const target = document.getElementById("player-bar__track__duration");
      const passedBar = document.getElementById("player-bar__track__listened");
      const passed = event.layerX / target.offsetWidth;

      currentTrack.currentTime = currentTrack.duration * passed;
      passedBar.style.width = `${passed * 100}%`
    },

    changeVolume(event) {
      const currentTrack = document.getElementById("player-bar__track");
      const target = document.getElementById("player-bar__volume__full");
      const currentVolume = document.getElementById("player-bar__volume__current");

      const setValue = event.layerX / target.offsetWidth;

      currentTrack.volume = this.chomp(0, 1, setValue);
      currentVolume.style.width = `${this.chomp(0, 1, setValue) * 100}%`;
    },

    changeListenedBar() {
      const currentTrack = document.getElementById("player-bar__track");
      const passedBar = document.getElementById("player-bar__track__listened");
      const multiply = 10;
      const oneTickTime = currentTrack.duration / (100 * multiply);
      let percents = 0;

      if (passedBar.style.width == "") {
        passedBar.style.width = "0%"
      }

      this.timer = setInterval(() => {
        percents = Number.parseFloat(passedBar.style.width.replace("%", ""));
        percents += 1 / multiply;
        passedBar.style.width = `${percents}%`;
      }, oneTickTime * 1000)
    },

    stopChanging() {
      clearInterval(this.timer)
    },

    trackEnded() {
      document.getElementById("player-bar__track__listened").style.width = "0%";
      this.stopChanging()
    },

    chomp(min, max, value) {
      if (value < min) {
        return min;
      }

      if (value > max) {
        return max;
      }

      return value;
    }
  }
}
</script>

<style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Quicksand:wght@700');


  span {
    font-family: Quicksand;
  }

  .player-bar {
    height: 20%;
    width: 100%;
    background-color: #1e1e1e;
    filter: drop-shadow(0px -25px 100px rgba(16, 16, 16, 0.51));
    position: absolute;
    bottom: 0;
  }

  .player-bar__cover {
    width: 8%;
    float: left;
    border-radius: 14px;
    outline: black;
    margin-right: 1.5%;
  }

  .player-bar__container{
    margin: auto;
    width: 70%;
    height: fit-content;
    transform: translateY(25%);
  }

  .player-bar__track-name {
    color: white;
    font-family: Quicksand;
    font-size: 20px;
  }

  .player-bar__track-author {
    color: rgba(255, 255, 255, 0.44);
    font-size: 14px;
  }

  .player-bar__track-info__name-author {
    margin-top: 1.3%;
  }

  .player-bar__track-info {
    display: inline-block;
  }

  .player-bar__container__controls {
    position: relative;
    margin: 0 20%;
    transform: translateY(-60%);
  }

  .player-bar__track__listened, .player-bar__track__duration {
    display: block;
    border-radius: 50px;
    height: 5px;
  }

  .player-bar__track__duration {
    margin-top: 10px;
    width: 100%;
    background: rgba(255, 255, 255, 0.04);
  }

  .player-bar__track__listened {
    margin-top: -5px;
    width: 0%;
    background: #FACD66
  }

  .play {
    clear: both;
    background: transparent;
    outline: transparent;
    border: transparent;
    cursor: pointer;
    display: block;
    margin-left: auto;
    margin-right: auto;
  }

  .play img {
    border-radius: 50%;
    width: 40%;
    height: 40%;
  }
  
  .player-bar__bars:hover {
    cursor: pointer;
  }

  .player-bar__volume {
    position: absolute;
    top: 35%;
    left: 83%;
    width: 15%;
  }
  
  .player-bar__volume:hover {
    cursor: pointer;
  }

  .player-bar__volume__full, .player-bar__volume__current {
    display: block;
    border-radius: 50px;
    height: 5px;
    width: 100%;
  }

  .controls {
    transform: translate(20%, 55%);
  }

  .player-bar__volume__full {
    background: rgba(255, 255, 255, 0.04);
    width: 100%;
    margin: 0;
  }

  .player-bar__volume__current {
    transform: translateY(-100%);
    background: #FACD66;
  }

  .player-bar__volume__icon {
    margin-left: auto;
    margin-right: auto;
    float: left;
  }

</style>