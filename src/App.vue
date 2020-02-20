<template>
<div id="app">
    <div class="app-header">
        <div class="title">
            Manage Absence Codes
        </div>
        <div class="import-box">
            <div class="border-line">
            </div>
            <div class="divider-line" style="margin-right: 20px;">
                <v-btn @click="resetDivider" class="orange">
                    Reset divider
                </v-btn>
            </div>
            <div class="export-file" style="margin-right:20px;">
                <v-btn @click="saveExcel" class="orange">
                    Download File
                </v-btn>
            </div>
            <div class="import-file">
                <v-btn @click="onButton" class="orange">
                    Import List <br>from File
                    <!-- <p>(.csv, xls, xlxs)</p> -->
                </v-btn>
                <VueFullScreenFileDrop @drop='onDrop' style="border: 1px solid black"/>
                <input type="file" class="inputfile" id="myinput" @change="onFileChange">
            </div>
        </div>
    </div>
    <div class="main-panel">
        <Menu @change-menu="onChangeMenu" />
        <dashboard v-bind:element="element" />
    </div>
    <div class="app-footer">
    </div>
</div>
</template>

<script>
import Menu from './components/Menu.vue'
import Dashboard from './components/Dashboard.vue'
import readXlsxFile from 'read-excel-file'
import VueFullScreenFileDrop from 'vue-full-screen-file-drop'
import test from './components/test'

export default {
  name: 'App',
  components: {
    Menu,
    Dashboard,
    VueFullScreenFileDrop,
    test
  },
  data () {
    return {
      element: ''
    }
  },
  methods: {
    // Example event handler
    onDrop (formData, files) {
      readXlsxFile(files[0]).then(rows => {
        this.$store.state.exceldata = rows
      })
    },
    onChangeMenu (value) {
      this.element = value
    },
    onButton () {
      document.getElementById('myinput').click()
    },
    onFileChange (e) {
      var files = e.target.files || e.dataTransfer.files
      if (!files.length) {
        return
      }
      readXlsxFile(files[0]).then(rows => {
        this.$store.state.exceldata = rows
      })
    },
    resetDivider () {
      console.log('reset divider')
    },
    saveExcel () {
      var exceldata = []
      for (let i = 1; i < this.$store.state.exceldata.length; i++) {
        console.log(i)
        exceldata.push({
          'Code': this.$store.state.exceldata[i][0],
          'Description': this.$store.state.exceldata[i][1],
          'Location': this.$store.state.exceldata[i][2],
          'Order': this.$store.state.exceldata[i][3]
        })
      }
      var json_exceldata = XLSX.utils.json_to_sheet(exceldata)
      var wb = XLSX.utils.book_new()
      XLSX.utils.book_append_sheet(wb, json_exceldata, 'Sheet1') // sheetAName is name of Worksheet
      XLSX.writeFile(wb, 'save.xlsx')
    }
  }
}
</script>

<style>
.inputfile {
    display: none;
}

@font-face {
    font-family: "SegoeUI";
    src: url(./assets/font/segoe-ui.ttf);
}

* {
    box-sizing: border-box;
}

#app {
    max-width: 1243px;
    margin-left: auto;
    margin-right: auto;
    margin-top: 5rem;
}

body {
    background-color: #ffffff;
    padding-top: 0.625rem;
    padding-bottom: 2.6875rem;
    font-size: 16px;
}

label {
  left: 0px !important;
  right: auto !important;
}

.app-header {
    width: 100%;
}
.orange {
    background-color: orange !important;
    color: white !important;
    height: 50px !important;
}
.app-footer {
    width: 100%;
    border: 1px solid #707070;
    background-color: #707070;
    padding: 15px;
}

.title {
    color: white;
    background: #2665A6;
    text-align: center;
    border: solid 1px #707070;
    padding: 5px;
}

.import-box {
    display: flex;
    padding: 20px 0;
}

.border-line {
    width: 80%;
    border: 1px solid black;
    margin: auto;
}

.import-file {
    width: 12%;
}

.import-button {
    width: 100%;
    border-radius: 14px;
    border: solid 1px #707070;
    background-color: #fcccac;
    padding: 5px 5px;
    cursor: pointer;
    transition: all ease-in-out 0.3s;
}

.import-button:hover {
    color: #a01712;
    background: white;
    border-color: #fcccac;
}

p {
    margin: 0;
}

.main-panel {
    display: flex;
    padding: 0 40px;
}

@media only screen and (max-width: 425px) {
    .border-line {
        width: 72%;
    }

    .import-file {
        width: 20%;
    }
}

@media only screen and (max-width: 576px) {
    .main-panel {
        padding: 0 20px;
    }
}

@media only screen and (max-width: 768px) {
    #app {
        margin-top: 0;
    }

    .border-line {
        width: 60%;
    }

    .import-file {
        width: 29%;
    }
}

@media only screen and (max-width: 1024px) {
    .main-panel {
        flex-direction: column;
    }
}
</style>
