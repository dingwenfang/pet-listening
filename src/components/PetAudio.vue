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
          itemsFetched: [],
      }      
  },
  computed: {
    items: function() {
      let items = []
      let title = "Track 01_Copyright"
      let source = "/audio/" + title + ".mp3"
      let trackUrl = ''
      items.push({title, source, trackUrl})
      var i
      for (i = 2; i <= 78; i++) {
        if (i<10) {
          title = "Track 0" + i
          source = "/audio/" + title + ".mp3"
          trackUrl = "/track/t0" + i + ".txt"
        } else {
          title = "Track " + i
          source = "/audio/" + title + ".mp3"
          trackUrl = "/track/t" + i + ".txt"
        }
        items.push({title, source, trackUrl})
      }
      return items

    }

  },
  created: function () {
    // `this` points to the vm instance
   /*this.axios.get('/audio').then((resp) => {
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
          this.itemsFetched.push({ title, source, trackUrl});
          cur = it.next();
        }
      }).catch((err) => {
        console.log(err);
      });
    console.log("out");
    console.log(this.itemsFetched);*/
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
