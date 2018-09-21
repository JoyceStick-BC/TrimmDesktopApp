<template lang="html">
    <div class="main">
        <button type="button" class="signout" @click.prevent="logout">Sign out</button>
        <DragBar></DragBar>
        <div class="project-selector-container">
            <input type="checkbox" class="project-selector-state" id="project-selector-state-id">
            <label for="project-selector-state-id" class="project-selector-state-label">
                {{ (currentDirectory != null ? currentDirectory.name : 'Select Project Directory')}}
            </label>
            <div class="project-selector" id="project-selector">
                <label for="project-selector-state-id" class="project-selector-state-label-selected" ></label>
                <input type="file" v-on:change="processNewFolder" webkitdirectory directory>
                <div class="directory" v-for="(directory, index) in savedDirectories" :key="index">
                    {{ index +  ':' + directory }}
                </div>
            </div>
        </div>
        <TrimmDownload></TrimmDownload>
    </div>
</template>

<script>
import DragBar from '@/components/DragBar'
import os from 'os'
import storage from 'electron-json-storage'
import TrimmDownload from '@/components/TrimmDownload'

export default {
  components: {
    DragBar,
    TrimmDownload
  },
  data() {
    return {
      savedDirectories: {},
      currentDirectory: null,
    }
  },
  created() {
    var self = this

    storage.getMany(['saved-directories', 'last-directory'], function(error, data) {
      if (data['saved-directories']['directories']) {
        self.savedDirectories = data['saved-directories']['directories']
        console.log(self.savedDirectories)
      }
      if (data['last-directory']) {
        self.currentDirectory = data['last-directory']['directory']
      }
    })
  },
  methods: {
    logout() {
      this.$router.push({
        path: 'landing-page'
      })
    },
    processNewFolder() {
      this.currentDirectory = event.target.files[0]
      this.savedDirectories[this.currentDirectory.path] = this.currentDirectory.name
      var self = this
      // Save new directory from the object attached to this
      storage.set('saved-directories', {
        directories: self.savedDirectories
      }, function(error) {
        if (error) {
          console.log(error)
        }
      })
      // Save new folder as most recent
      var tempDirectory = {}
      tempDirectory.path = self.currentDirectory.path
      tempDirectory.name = self.currentDirectory.name
      storage.set('last-directory', {
        directory: tempDirectory
      }, function(error) {
        if (error) {
          console.log(error)
        }
      })
      // Uncheck to hide project selector
      document.getElementById('project-selector-state-id').checked = false
    }
  }
}
</script>

<style lang="css">

    @import url('https://fonts.googleapis.com/css?family=Nunito:300,400,600,700');

    body {
        background-color: #20176f;
        padding: none;
        margin: 0;
        font-family: 'Nunito', sans-serif;
    }

    .signout {
        display: block;
        position: absolute;
        z-index: 4;
        right: 0;
        margin-top: 0px;
        border: none;
        background: none;
        margin-right: 5px;
        color: white;
        line-height: 18px;
        cursor: pointer;
    }

    .project-selector-container {
    }

    .project-selector-state {
        display: none;
    }

    .project-selector-state-label {
        width: 100%;
        background-color: #614cdb;
        color: white;
        display: block;
        text-align: center;
        padding: 5px 0px
    }

    .project-selector-state-label-selected {
        background-color: pink;
        height: 50px;
        width: 50px;
        display: block;
        margin-top: 22px;
    }

    #project-selector-state-id:checked ~ #project-selector {
        display: block;
        top: 0;
    }

    .project-selector {
        height: 100vh;
        width: 100%;
        background-color: white;
        display: none;
        position: absolute;
        z-index: 3;
        top: 100vh;
        transition: 0.2s ease all;
    }
</style>
