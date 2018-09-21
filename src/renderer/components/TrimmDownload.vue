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
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      util: require("util"),
      spawn: require("child_process").spawn,
      searchInputValue: '',
    }
  },
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
    searchInputChanged() {
      console.log(this.searchInputValue)
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
  color: #DCDCDC;
  font-size: 13px;
}
</style>
