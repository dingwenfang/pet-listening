<template>
  <b-container>
      <AudioPlayList  v-bind:audioList="items">
      </AudioPlayList>
  </b-container>
</template>

<script>
import AudioPlayList from './AudioPlayList.vue'
export default {
  name: 'PetAudio',
  components: {
      AudioPlayList
  },
  props: {
  },
  data: function() {
      return {
          audios: [],
          /*items: [
              {
                  title: "02_U1_Ex2",
                  source: "./audio/02_U1_Ex2.mp3",
                  track: "<p>track for audio 1</p>",
              },
              {
                  title: "03_U1_Ex3",
                  source: "./audio/03_U1_Ex3.mp3",
                  track: "<p>track for audio 2</p>",
              },
              {
                  title: "04_U1_Ex5",
                  source: "./audio/04_U1_Ex5.mp3",
                  track: "<p>track for audio 2</p>",
              },
              {
                  title: "05_U2_Ex2",
                  source: "./audio/05_U2_Ex2.mp3",
                  track: "<p>track for audio 2</p>",
              }
          ],*/
          itemsInit: [
              {
                  title: "02_U1_Ex2",
                  source: "/audio/02_U1_Ex2.mp3",
                  trackUrl: "/track/t02.txt",
                  //track: "<p>track for audio 1</p>",
              },
          ],
          itemsFetched: [],
      }      
  },
  computed: {
    items: function() {
      /*if (this.itemsFetched.length === 0) {
        return this.itemsInit;
      } else {
        return this.itemsFetched;
      }*/
      return this.itemsFetched;

    }

  },
  created: function () {
    // `this` points to the vm instance
   this.axios.get('/audio').then((resp) => {
        console.log(resp.data);
        this.audios = resp.data;
        const it = this.audios.values();
        let cur = it.next();
        let title, source, trackUrl;
        while (!cur.done) {
          const fileName = cur.value;
          const parts = fileName.split('.')
          title = parts[0];
          source = "/audio/" + fileName;
          trackUrl = "/track/t" + parts[0].substr(6,2) + ".txt";
          /*this.axios.get(trackUrl).then((resp_t) => {
            console.log(resp_t.data);
            track = resp_t.data;
          }).catch(() => {
            //console.log(err.msg);
          })*/
          this.itemsFetched.push({ title, source, trackUrl});
          cur = it.next();
        }
      }).catch((err) => {
        console.log(err);
        //this.itemsFetched = [];
        /*this.$bvToast.toast(`${err.message}`, {
          title: 'Fail to get audios',
          variant: 'danger',
          solid: true,
        });*/
      });
    console.log("out");
    console.log(this.itemsFetched);
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
