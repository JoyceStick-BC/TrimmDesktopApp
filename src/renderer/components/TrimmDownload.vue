<template lang="html">
  <div class="">
    <div class="title-container">
      <div class="title-small">
        Download Assets
        <a class="trimm-pull-button" @click.prevent="trimmPull">Reinstall All</a>
      </div>

    </div>
    <div class="trimm-download">
      <div class="trimm-search">
        <input type="text" class="trimm-search-input" @input="searchInputChanged" v-model="searchInputValue" placeholder="Search Asset Store">
        <div class="search-results">
            <div class="search-result" v-for="(result, index) in searchResults" @click="trimmDownload(result)">
                <p class="search-result-text">{{ result._source.bundleName }}</p>
            </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import storage from 'electron-json-storage'
import axios from 'axios'

export default {
  data() {
    return {
      util: require("util"),
      spawn: require("child_process").spawn,
      searchInputValue: '',
      searchResults: []
    }
  },
  props: ['token', 'path'],
  created() {
  },
  methods: {
    trimmPull() {
      var process = this.spawn('python', [__dirname + '/trimm_desktop/trimm.py', 'pull']);

      process.stdout.on('data', function(data){
        var response = new TextDecoder("utf-8").decode(data);
        console.log(response);
      })

      process.stderr.on('data', function(error) {
        var response = new TextDecoder("utf-8").decode(error);
        console.log(response);
      })
    },
    trimmDownload(result) {
        console.log(this.path.path)
        console.log(result)
        var process = this.spawn('python', [__dirname + '/trimm_desktop/trimm.py', 'install', this.path.path, result._source.user + '/' + result._source.bundleName]);

        process.stdout.on('data', function(data){
          var response = new TextDecoder("utf-8").decode(data);
          console.log(response);
        })

        process.stderr.on('data', function(error) {
          var response = new TextDecoder("utf-8").decode(error);
          console.log(response);
        })
    },
    searchInputChanged() {
      var self = this
      axios.get(process.env.HOST_URL + 'api/browse/' + this.searchInputValue, {
          headers: {
              Authorization: this.token
          }
      }).then(function(response) {
          self.searchResults = response.data
          console.log(self.searchResults)
      })
    },
  }
}
</script>

<style lang="css">

.title-container {
  display: block;
}

.trimm-pull-button {
  display: inline-block;
  float: right;
  margin-right: 5%;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
}

.trimm-download {
  width: 90%;
  margin: auto;
  display: block;
}

.trimm-search-input {
  display: block;
  border: none;
  border-radius: 4px;
  background-color: #392c9f;
  height: 22px;
  width: 100%;
  color: white;
  font-weight: 500;
  padding-left: 5px;
  font-size: 13px;
}

.trimm-search-input::placeholder {
  font-size: 13px;
}

.search-result {
    padding: 0;
    margin: 0;
    cursor: pointer;
    border-radius: 4px;
}

.search-result:hover {
    background-color: #392c9f;
}

.search-result-text {
    color: white;
    padding: 3px;
    font-size: 14px;
}
</style>
