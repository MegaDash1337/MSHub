<template>
  <PlayerBar :track="tr" />
</template>

<script>
import PlayerBar from "@/components/PlayerBar";
import Track from "@/scripts/Track";
export default {
  components: {PlayerBar},

  data() {
    return {
      tr: new Track("bankai", "zxcursed", "https://images.genius.com/5efb0f72e55bcab3b366229f4f1b9547.1000x1000x1.png", "https://now.morsmusic.org/load/1700367275/zxcursed_-_bankai_(musmore.com).mp3", "audio/mp3")
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>
