<template lang="html">
  <div class="">
    <div class="trimm-download">
      <div class="trimm-pull">
        <a class="trimm-pull-button" @click.prevent="trimmPull">Trimm Pull All Assets</a>
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
    }
  }
}
</script>

<style lang="css">
.trimm-download {
  padding: 10px;
}

.trimm-pull {
  background-color: #392c9f;
  color: white;
  border-radius: 4px;

}

.trimm-pull-button {
  padding: 5px;
}
</style>
