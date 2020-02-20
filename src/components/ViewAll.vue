<template>
  <div class="list-section">
    <!-- <b-button v-b-modal.modal-1>Launch demo modal</b-button> -->

    <b-modal id="modal-1" v-model="show" centered title="Edit">
      <!-- <p class="my-4">Hello from modal!</p> -->
      <v-text-field color="primary" label="Code" v-model="d_code" />
      <v-text-field color="primary" label="Description" v-model="d_description" />
      <v-text-field color="primary" label="Location" v-model="d_location" />
      <v-text-field color="primary" label="Order" v-model="d_order" />
      <template v-slot:modal-footer>
        <div class="w-100">
          <b-button variant="primary" size="sm" class="float-right" @click="show=false">Cancel</b-button>
          <b-button
            variant="primary"
            size="sm"
            class="float-right"
            style="margin-right: 10px"
            @click="doUpdate"
          >Update</b-button>
        </div>
      </template>
    </b-modal>
    <h4>View All Absence Codes</h4>
    <!-- <div class="list-section-body" id="parent"> -->
    <SortedTable :values="abscodes">
      <thead>
        <tr>
          <th scope="col" class="code">
            <SortLink name="code">Code</SortLink>
          </th>
          <th scope="col" class="description">
            <SortLink name="description">Description</SortLink>
          </th>
          <th scope="col" class="location">
            <SortLink name="location">Location</SortLink>
          </th>
          <th scope="col" class="order">
            <SortLink name="order">Order</SortLink>
          </th>
          <th></th>
        </tr>
        <hr class="divider">
      </thead>
      <tbody slot="body" slot-scope="sort">
        <tr v-for="abscode in sort.values" :key="abscode.absence_code_pk">
          <td class="code">{{ abscode.code }}</td>
          <td class="description">{{ abscode.description }}</td>
          <td class="location">{{ abscode.location }}</td>
          <td class="order">{{ abscode.order }}</td>
          <td>
            <v-btn v-b-modal.modal-1 small icon @click="editdialog(abscode.absence_code_pk)">
              <v-icon>mdi-pencil</v-icon>
            </v-btn>
            <v-btn small icon @click="deleteRecord(abscode.absence_code_pk)">
              <v-icon>mdi-delete</v-icon>
            </v-btn>
          </td>
        </tr>
      </tbody>
    </SortedTable>
    <!-- </div> -->
    <div class="list-section-footer"></div>
  </div>
</template>

<script>
// eslint-disable-next-line no-unused-vars
import uuid from 'uuid'
import axios from 'axios'
import draggable from 'vuedraggable'
import vueCustomScrollbar from 'vue-custom-scrollbar'

export default {
  name: 'AbsenceCodes',
  display: 'Transitions',
  components: {
    draggable,
    vueCustomScrollbar
  },
  computed: {
    dragOptions () {
      return {
        animation: 200,
        group: 'description',
        disabled: false,
        ghostClass: 'ghost'
      }
    }
  },
  data () {
    return {
      abscodes: [],
      addCodeForm: {
        absence_code: '',
        description: '',
        customer_id: '',
        active: '',
        order_index: '',
        user_id: ''
      },
      editForm: {
        absence_code_pk: '',
        absence_code: '',
        description: '',
        customer_id: '',
        active: '',
        order_index: '',
        user_id: ''
      },
      message: '',
      showMessage: false,
      testURL: 'http://127.0.0.1:5000',
      settings: {
        maxScrollbarLength: 60
      },
      drag: false,
      show: false,
      d_code: '',
      d_description: '',
      d_location: '',
      d_order: '',
      updateKey: 0
    }
  },
  methods: {
    doUpdate () {
      this.show = false
      this.$store.state.exceldata[this.updateKey][0] = this.d_code
      this.$store.state.exceldata[this.updateKey][1] = this.d_description
      this.$store.state.exceldata[this.updateKey][2] = this.d_location
      this.$store.state.exceldata[this.updateKey][3] = this.d_order
      this.getCodes()
    },
    deleteRecord (key) {
      this.$store.state.exceldata.splice(key + 1, 1)
      this.getCodes()
    },
    editdialog (key) {
      this.updateKey = key + 1
      this.d_code = this.abscodes[key].code
      this.d_description = this.abscodes[key].description
      this.d_location = this.abscodes[key].location
      this.d_order = this.abscodes[key].order
    },
    async getCodes () {
      this.abscodes = []
      for (let i = 1; i < this.$store.state.exceldata.length; i++) {
        this.abscodes.push({
          absence_code_pk: i - 1,
          code: this.$store.state.exceldata[i][0],
          description: this.$store.state.exceldata[i][1],
          location: this.$store.state.exceldata[i][2],
          order: this.$store.state.exceldata[i][3]
        })
      }
    },
    addCode (payload) {
      this.showMessage = false
      const path = `${this.testURL}/absencecodes?user_pk=`
      axios
        .post(path, payload)
        .then(() => {
          this.getCodes()
          this.message = 'Absence code has been Added!'
          // this.showMessage = true;
        })
        .catch(error => {
          // eslint-disable-next-line
          console.log(error);
          this.getCodes()
        })
    },
    initForm () {
      this.addCodeForm.absence_code = ''
      this.addCodeForm.description = ''
      this.addCodeForm.order_index = ''
      this.editForm.absence_code_pk = ''
      this.editForm.absence_code = ''
      this.editForm.description = ''
      this.editForm.order_index = ''
    },
    onSubmit (evt) {
      evt.preventDefault()
      this.$refs.addCodeModal.hide()
      const payload = {
        absence_code: this.addCodeForm.absence_code,
        description: this.addCodeForm.description,
        order_index: this.addCodeForm.order_index
      }
      this.addCode(payload)
      this.initForm()
    },
    onReset (evt) {
      evt.preventDefault()
      this.$refs.addCodeModal.hide()
      this.initForm()
    },
    onResetUpdate (evt) {
      evt.preventDefault()
      this.$refs.editCodeModal.hide()
      this.initForm()
      this.getCodes() // why?
    },
    onSubmitUpdate (evt) {
      evt.preventDefault()
      this.$refs.editCodeModal.hide()
      const payload = {
        absence_code_pk: this.editForm.absence_code_pk,
        absence_code: this.editForm.absence_code,
        description: this.editForm.description,
        order_index: this.editForm.order_index
      }
      this.updateCode(payload)
    },
    updateCode (payload) {
      this.showMessage = false
      const path = `${this.testURL}/absencecodeupdate`
      axios
        .put(path, payload)
        .then(() => {
          this.getCodes()
          this.message = 'Absence code has been Updated!'
          // this.showMessage = true;
        })
        .catch(error => {
          // eslint-disable-next-line
          console.error(error);
          this.getCodes()
        })
    },
    editCode (code) {
      this.editForm = code
    },
    deleteCode (code) {
      this.showMessage = false
      const payload = {
        absence_code_pk: code
      }
      const path = `${this.testURL}/absencecodedelete`
      axios
        .put(path, payload)
        .then(() => {
          this.getCodes()
          this.message = 'Absence Code deleted!'
          // this.showMessage = true;
        })
        .catch(error => {
          // eslint-disable-next-line
          console.error(error);
          this.getCodes()
        })
    },
    goBacktoSetup () {
      window.parent.location.href =
        'http://127.0.0.1:8080/app/setupOptions.do?setupoption'
    }
  },
  created () {
    this.getCodes()
  }
}
</script>

<style scoped>
.divider {
  margin: 0px;
  border: 3px dashed #eee;
}

#parent {
  /* position: absolute; */
  /* left: 10vw;
  top: 15vh;
  width: 80vw;
  height: 75vh; */
  overflow: hidden;
}

table {
  display: inline-flex;
  flex-direction: column;
  /* flex: 1 1 auto; */
  /* overflow: hidden; */
  width: 700px;
  height: 350px !important;
}
.code {
  width: 100px;
}
.description {
  width: 150px;
}
.location {
  width: 130px;
}
.order {
  width: 100px;
}
thead {
  flex: 1 0 auto;
  display: block;
  /* x-scrolling will be managed via JS */
  overflow-x: hidden;
  overflow-y: scroll;
  scrollbar-base-color: #ccc;
  scrollbar-face-color: #ccc;
  scrollbar-highlight-color: #ccc;
  scrollbar-track-color: #ccc;
  scrollbar-arrow-color: #ccc;
  scrollbar-shadow-color: #ccc;
  /* scrollbar-dark-shadow-color: #ccc; */
}
thead::-webkit-scrollbar {
  display: block;
  background-color: #ccc;
}
thead::-webkit-scrollbar-track {
  background-color: #ccc;
}
tbody {
  display: block;
  overflow: scroll;
}
table thead tr th {
  font-size: 18px !important;
  text-align: center !important;
  /* padding-left: 10px;
  padding-right: 10px; */
}
table thead tr th a {
  text-decoration: none;
}
table tbody tr td {
  font-size: 20px;
  text-align: center;
  margin: 0px !important;
  /* padding-left: 10px;
  padding-right: 10px; */
}
tbody:nth-child(3) {
  display: none;
}
td:first-child,
th:first-child {
  position: sticky;
  position: -webkit-sticky;
  left: 0;
}

.list-section {
  text-align: center;
  padding-top: 1.5625rem;
  margin-right: 100px;
}

.list-section-body {
  display: grid;
  grid-template-columns: 25% 75%;
}

.list-section-footer {
  display: flex;
  justify-content: space-evenly;
  padding: 1.875rem 12.5rem;
}

.search-part {
  padding-top: 5rem;
  text-align: left;
}

.search-box {
  font-family: SegoeUI;
  font-weight: 300;
  font-stretch: normal;
  font-style: italic;
  line-height: 1.42;
  letter-spacing: normal;
  text-align: left;
  color: #202020;
  padding: 0.425rem;
  font-size: 1rem;
  width: 100%;
}

.list-title {
  font-weight: 600;
  padding: 2rem 0 1rem 0;
  display: flex;
}

.list-item {
  display: flex;
  cursor: pointer;
}

.list-item.normal {
  font-style: normal !important;
  line-height: 2;
  color: #000000;
  align-items: center;
  width: 700px;
}

.delete-item {
  border: 1px solid;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  width: 17px;
  height: 17px;
  padding-bottom: 1px;
}

.delete-item:active {
  font-weight: bold;
}

.scroll-area {
  position: relative;
  width: 400px;
  height: 320px;
}

.ps__thumb-y,
.ps__thumb-y:hover {
  top: 0px;
  height: 20px !important;
  width: 20px;
  border-radius: 50%;
  left: -8px;
}

.ps__thumb-y,
.ps__thumb-y {
  top: 0px;
  height: 20px !important;
  width: 20px;
  border-radius: 50%;
  left: -8px;
}

.ps__rail-y:hover > .ps__thumb-y,
.ps__rail-y:focus > .ps__thumb-y,
.ps__rail-y.ps--clicking .ps__thumb-y {
  background-color: #999;
  width: 20px;
  height: 20px !important;
}

.ps__rail-y,
.ps__rail-y:hover {
  height: 400px;
  width: 4px;
  background: grey;
  margin-right: 10px;
  /* top:68px!important; */
}

.list-part {
  height: 400px;
  font-family: SegoeUI;
  font-weight: 300;
  font-stretch: normal;
  font-style: italic;
  line-height: 1.42;
  letter-spacing: normal;
  color: #202020;
  padding-left: 3.125rem;
  /* overflow-y: scroll; */
}

.list-title .code {
  width: 4.125rem;
  height: 2rem;
  text-align: left;
}

.list-title .description {
  padding-left: 2.8rem;
  text-align: left;
}

.list-title .location {
  padding-left: 30px;
  text-align: left;
}

.list-title .order {
  padding-left: 30px;
  text-align: left;
}

.list-item .code {
  width: 20%;
  text-align: left;
}

.list-item .description {
  text-align: left;
  width: 28%;
}

.list-item .location {
  text-align: left;
  width: 23%;
}
.list-item .order {
  text-align: left;
  width: 17%;
}

h4 {
  font-family: SegoeUI;
  font-weight: 600;
  font-stretch: normal;
  font-style: normal;
  line-height: 1.48;
  letter-spacing: normal;
  color: #b23737;
  margin: 0;
  font-size: 1.125rem;
}

.btn-submit {
  font-family: SegoeUI;
  font-weight: normal;
  font-stretch: normal;
  font-style: normal;
  line-height: 1.42;
  letter-spacing: normal;
  text-align: left;
  color: #000000;
  border-radius: 1rem;
  background-color: #b9b9b9;
  padding: 0.5rem 2.75rem 0.75rem 2.81rem;
  outline: none;
  border: none;
  cursor: pointer;
  transition: all ease-in-out 0.3s;
}

.btn-submit:hover {
  background: #999797;
  color: white;
}

.btn-cancel {
  font-family: SegoeUI;
  font-weight: normal;
  font-stretch: normal;
  font-style: normal;
  line-height: 1.42;
  letter-spacing: normal;
  text-align: left;
  color: #000000;
  border-radius: 1rem;
  background-color: #b9b9b9;
  padding: 0.5rem 2.75rem 0.75rem 2.81rem;
  outline: none;
  border: none;
  cursor: pointer;
  transition: all ease-in-out 0.3s;
}

.btn-cancel:hover {
  background: #999797;
  color: white;
}

@media only screen and (max-width: 1024px) {
  .list-section {
    margin-right: 0;
  }

  .list-title .description {
    padding-left: 0;
  }

  .list-title .code {
    width: 27%;
  }

  .scroll-area {
    width: 100%;
  }
}

@media only screen and (max-width: 768px) {
  .list-section-body {
    padding-right: 0;
  }

  .search-part {
    padding-left: 0;
  }

  .list-section-footer {
    padding: 1.475rem 2.5rem;
  }
}

@media only screen and (max-width: 576px) {
  .list-section-body {
    display: block;
    padding: 0;
  }

  .search-part {
    padding-top: 20px;
    padding-left: 0;
    text-align: center;
  }

  .search-box {
    width: 100%;
  }

  .list-part {
    padding: 0;
    margin-top: 10px;
  }

  .list-title {
    padding: 1rem 0 1rem 0;
  }

  .list-title .code {
    width: 33%;
  }

  .list-section-footer {
    padding: 1.475rem 2.5rem;
  }

  .list-item .code {
    width: 33%;
  }

  .list-item .description {
    width: 57%;
  }

  .scroll-area {
    width: 100%;
  }
}

@media only screen and (max-width: 425px) {
}
</style>
