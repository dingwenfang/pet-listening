<template>
  <b-container>
      <MyAudio ref="myRunningAudio"  v-bind:audioInfo="currentAudio" v-on:audioend="playNext">
      </MyAudio>

      <b-card border-variant="info" class="mt-2 mb-2" >
        <b-row>
            <b-col cols=6>
                <b-form-input  type="text" style="width: 5rem;" class="p-0 m-0" size="sm"
                v-model="audioSearch" placeholder="*"/>
            </b-col>
            <b-col cols=6>
                <b-button @click="repeatMode = repeatMode === 1? 0 : 1" variant="outline-info">
                    {{repeatMode === 0? '列表循环' : '单曲循环'}}
                </b-button>
            </b-col>
        </b-row>
        <!--<b-collapse id="audiolist">-->
            <b-list-group class="mt-4" flush>
                <b-list-group-item button
                v-for="a in filteredAudioList" :key="a.title" @click="setCurrentAudio(a)">
                    <b-row>
                        <b-col cols="2"/>
                        <b-col cols="7">{{a.title}}</b-col>
                        <b-col cols="3">
                        <b-button class="h-75 mb-2" pill variant="outline-danger" size="sm"
                                @click="removeFromPlayList(a)">
                            <b-icon icon="trash" aria-hidden="true"></b-icon>
                        </b-button>
                        </b-col>
                    </b-row>
                </b-list-group-item>
            </b-list-group>
        <!--</b-collapse>-->
      </b-card>
  </b-container>
</template>

<script>
import MyAudio from './MyAudio.vue'
export default {
  name: 'AudioPlayList',
  components: {
      MyAudio
  },
  props: {
      audioList: {
          type: Array,
          required: true,
      },
  },
  data: function() {
      return {
          currentAudio: null,
          //myAudioList: null,
          audioSearch: '',
          repeatMode: 0,//0 stands for list repeat, 1 stands for repeat 1.
      }      
  },
  computed:  {
    filteredAudioList() {
        return this.audioList.filter(
            (a) => a.title.toLowerCase().includes(this.audioSearch.toLowerCase()),
        );
    },
      
  },
  methods: {
      setCurrentAudio(a) {
          this.currentAudio = a;
          this.$refs.myRunningAudio.pauseAndLoadAudio();
      },
      removeFromPlayList(a) {
          this.audioList = this.audioList.filter(item => item.title !== a.title);
      },
      playNext(a) {
          if (this.repeatMode === 1) { //单曲循环
              this.$refs.myRunningAudio.playAudio();
          } else {
              //find the next
              const it = this.filteredAudioList.values();
              let  nextAudio = it.next();
              let cur = nextAudio;
              while ( !cur.done) {
                  if (cur.value.title === a.title) {
                      cur = it.next();
                      if ( !cur.done) {
                          this.currentAudio = cur.value;
                          this.$refs.myRunningAudio.loadAndPlayAudio();
                          return
                      } else {
                          break;
                      }
                  }
                  cur = it.next();
              }
              this.currentAudio = nextAudio.value;
              this.$refs.myRunningAudio.loadAndPlayAudio();
          }
      },



  },
  created: function() {
    this.currentAudio = this.audioList[0];
    //this.myAudioList = this.audioList.slice();
    //this.myAudioList = this.audioList;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
</style>
