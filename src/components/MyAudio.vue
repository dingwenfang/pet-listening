<template>
  <b-card id="MyAudio" border-variant="info" class="mt-2 mb-2" >
      <b-row align-v="center">
        <b-col cols="2" >
            <span style="font-weight:bold; color:DarkCyan" class = "mb-2 mt-2">
                {{audioInfo.title}}
            </span>
        </b-col>
        <b-col cols="8" >
            <audio ref="audio" volume='0'  v-on:loadedmetadata="loadedmetadata" v-on:ended="$emit('audioend', audioInfo)" v-on:canplaythrough="canplaythrough" v-on:timeupdate="timeupdate">
                <source v-bind:src='audioInfo.source'>
            </audio>
            <b-button @click=playPause() variant="outline-info">
                <b-icon v-bind:icon="playPauseIcon"></b-icon> 
            </b-button>

            <span class="ml-2">{{currentTimeShort}}</span>
            <b-form-input id="progress" class="myrange ml-2  pt-3" style="width: 10rem;" type="range" min="0.0" v-bind:max="duration" step="1" number v-model="currentTime" v-on:change="changeCurrentTime"/>
            <span class="ml-2">{{durationShort}}</span>

            <b-dropdown id="speed" dropup text="倍速" variant="outline-info" class="m-2">
                <b-dropdown-item-button v-bind:variant="playbackRate===0.5? 'info' : 'default'" @click="setPlaybackRate(0.5)">0.5X</b-dropdown-item-button>
                <b-dropdown-item-button v-bind:variant="playbackRate===0.75? 'info' : 'default'" @click="setPlaybackRate(0.75)">0.75X</b-dropdown-item-button>
                <b-dropdown-item-button v-bind:variant="playbackRate===1.0? 'info' : 'default'" @click="setPlaybackRate(1.0)">1.0X</b-dropdown-item-button>
                <b-dropdown-item-button v-bind:variant="playbackRate===1.25? 'info' : 'default'" @click="setPlaybackRate(1.25)">1.25X</b-dropdown-item-button>
                <b-dropdown-item-button v-bind:variant="playbackRate===1.5? 'info' : 'default'" @click="setPlaybackRate(1.5)">1.5X</b-dropdown-item-button>
                <b-dropdown-item-button v-bind:variant="playbackRate===1.75? 'info' : 'default'" @click="setPlaybackRate(1.75)">1.75X</b-dropdown-item-button>
                <b-dropdown-item-button v-bind:variant="playbackRate===2.0? 'info' : 'default'" @click="setPlaybackRate(2.0)">2.0X</b-dropdown-item-button>
            </b-dropdown>

            <b-button id="popover-volume" variant="outline-info">
              <b-icon icon="volume-up-fill"></b-icon> 
            </b-button>
            <b-popover id="popover-volume" target="popover-volume" placement="top" triggers="hover focus" >
              <b-form-group style="width: 5rem;">
                <b-form-input id="volume" type="range" min="0.0" max="1.0" step="0.1" number v-model="volume" class="myrange vranger" />
              </b-form-group>
            </b-popover>
        </b-col>
         
        <b-col cols="2" >
            <b-button v-b-toggle.transcripts size="sm" class="mb-2 mt-2" variant="outline-info"
            @click="showHideTranscript" >
            {{buttonTitle}}
            </b-button>
        </b-col>
      </b-row>
     
      <b-collapse id="transcripts" v-model="visible" class="mt-2">
      <!--<b-row>       -->
        <b-card>
            <b-card-body ref="track" v-html="track">
            </b-card-body>
        </b-card>
      <!--</b-row> -->
      </b-collapse>
      <!--
      -->
  </b-card>
</template>

<script>
export default {
  name: 'PetAudio',
  props: {
      audioInfo: {
          type: Object,
          required: true,
      },
  },
  data: function() {
    return {
        playPauseIcon: 'play-fill',
        volume: 0,
        buttonTitle:'Show Transcripts',
        visible: false,
        currentTime: 0,
        duration: 0,
        playbackRate :1,
        track:'',
    };
  },
  computed: {
    durationShort: function() {
      return this.transferTime(this.duration);
    },
    currentTimeShort: function() {
      return this.transferTime(this.currentTime);
    }
      
  },
  watch: {
    volume: function (val) {
      const audio = this.$refs.audio;
      audio.volume = val;
    },    
  },
  methods: {
      showHideTranscript() {        
        if (this.buttonTitle === 'Show Transcripts') {
          if  (this.audioInfo.track === undefined) {
            this.axios.get(this.audioInfo.trackUrl).then((resp_t) => {
              console.log(resp_t.data);
              this.audioInfo.track = resp_t.data.replace(/\n|\r\n/g,"<br/>");
              this.track = this.audioInfo.track;
            }).catch((err) => {
              console.log(err.msg);
            })
          } else {
            this.track = this.audioInfo.track;
          }
        }
        this.buttonTitle = (this.buttonTitle === 'Show Transcripts')? 'Hide Transcripts' : 'Show Transcripts'; 
        this.visible = !this.visible;
      },
      playPause() {
          const audio = this.$refs.audio;
          if (audio.paused) {
              this.playPauseIcon="pause-fill";
              audio.play();              
          } else {
              this.playPauseIcon="play-fill";
              audio.pause();
          }
      },
      setPlaybackRate(rate) {
          this.playbackRate = rate;
          const audio = this.$refs.audio;
          audio.playbackRate = rate;
      },
      changeCurrentTime() {
        const audio = this.$refs.audio;
        audio.currentTime = this.currentTime;
      },
      loadedmetadata: function (event) {
         if (event) {
          this.duration= parseInt(event.target.duration, 10);
        }
      },
      timeupdate: function (event) {
         if (event) {
          this.currentTime= parseInt(event.target.currentTime, 10);
        }
      },
      canplaythrough: function() {

      },
      /*audioEnd: function() {
        const audio = this.$refs.audio;
        audio.play();
      },*/
      playAudio:function() {
        const audio = this.$refs.audio;
        audio.play();
      },
      loadAndPlayAudio:function() {
        const audio = this.$refs.audio;
        audio.load();
        audio.playbackRate = this.playbackRate;   
        audio.play();
      },
      pauseAndLoadAudio:function() {
        const audio = this.$refs.audio;
        audio.pause();
        audio.load(); 
        audio.playbackRate = this.playbackRate;       
        this.playPauseIcon="play-fill";
        this.track ='';
        this.buttonTitle = 'Show Transcripts';
        this.visible = false;
      },
      transferTime(second) {
        if (Number(second) && second > 0) {
            second = parseInt(second); // 舍去秒数以后的小数位
        } else {
            //return '00:00:00'
            return '00:00';
        }

        //var h,m,s;
        var m,s;
        s = second % 60;
        m = ((second - s) % 3600) / 60;
        //h = parseInt(second / 3600)

        function fn(num) {
            return num >= 10 ? num : '0' + num
        }
        //return fn(h) + ':' + fn(m) + ':' + fn(s)
        return  fn(m) + ':' + fn(s);
    }
      

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.vranger {
  -webkit-transform:rotate(270deg);
  -moz-transform:rotate(270deg);
  -o-transform:rotate(270deg);
  -ms-transform:rotate(270deg);
  transform:rotate(270deg);
}

input[type=range].myrange {
  width: 100%;
  margin: 14.2px 0;
  background-color: transparent;
  -webkit-appearance: none;
}
input[type=range].myrange:focus {
  outline: none;
}
input[type=range].myrange::-webkit-slider-runnable-track {
  background: #696562;
  border: 3.4px solid #4a4822;
  border-radius: 12.1px;
  width: 100%;
  height: 7.6px;
  cursor: pointer;
}
input[type=range].myrange::-webkit-slider-thumb {
  margin-top: -13.6px;
  width: 16px;
  height: 26px;
  background: #009698;
  border: 1px solid #000000;
  border-radius: 3px;
  cursor: pointer;
  -webkit-appearance: none;
}
input[type=range].myrange:focus::-webkit-slider-runnable-track {
  background: #76726e;
}
input[type=range].myrange::-moz-range-track {
  background: #696562;
  border: 3.4px solid #4a4822;
  border-radius: 12.1px;
  width: 100%;
  height: 7.6px;
  cursor: pointer;
}
input[type=range].myrange::-moz-range-thumb {
  width: 16px;
  height: 26px;
  background: #009698;
  border: 1px solid #000000;
  border-radius: 3px;
  cursor: pointer;
}
input[type=range].myrange::-ms-track {
  background: transparent;
  border-color: transparent;
  border-width: 15.2px 0;
  color: transparent;
  width: 100%;
  height: 7.6px;
  cursor: pointer;
}
input[type=range].myrange::-ms-fill-lower {
  background: #5c5856;
  border: 3.4px solid #4a4822;
  border-radius: 24.2px;
}
input[type=range].myrange::-ms-fill-upper {
  background: #696562;
  border: 3.4px solid #4a4822;
  border-radius: 24.2px;
}
input[type=range].myrange::-ms-thumb {
  width: 16px;
  height: 36px;
  background: #009698;
  border: 1px solid #000000;
  border-radius: 3px;
  cursor: pointer;
  margin-top: 0px;
  /*Needed to keep the Edge thumb centred*/
}
input[type=range].myrange:focus::-ms-fill-lower {
  background: #696562;
}
input[type=range].myrange:focus::-ms-fill-upper {
  background: #76726e;
}
/*TODO: Use one of the selectors from https://stackoverflow.com/a/20541859/7077589 and figure out
how to remove the virtical space around the range input in IE*/
@supports (-ms-ime-align:auto) {
  /* Pre-Chromium Edge only styles, selector taken from hhttps://stackoverflow.com/a/32202953/7077589 */
  input[type=range].myrange {
    margin: 0;
    /*Edge starts the margin from the thumb, not the track as other browsers do*/
  }
}


#popover-volume .arrow {
  visibility: hidden;
}

.bs-popover-top {
  background-color: transparent!important;
  border: 0px transparent!important;
}

input#progress {
  padding-top: 3;
}
</style>
