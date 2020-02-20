<template>
  <div class="list-section">
    <h4>Edit Existing Code</h4>
    <div class="list-section-body">
      <div class="search-part">
        <input type="text" placeholder="Search" class="search-box">
      </div>
      <div class="list-part">
        <div class="list-title">
          <div class="code">Code</div>
          <div class="description">Description</div>
        </div>
        <vue-custom-scrollbar class="scroll-area" :settings="settings" @ps-scroll-y="scrollHanle">
          <draggable v-model="abscodes" @start="drag = true" @end="drag = false" v-bind="dragOptions">
            <transition-group type="transition" :name="!drag ? 'flip-list' : null">
              <tr v-for="abscode in abscodes" :key="abscode.absence_code_pk">
                <div class="list-item normal">
                  <div class="code">{{abscode.absence_code}}</div>
                  <div class="description">{{ abscode.description}}</div>
                  <span class="delete-item" @click="deleteCode(abscode.absence_code_pk)">&times;</span>
                </div>
              </tr>
            </transition-group>
          </draggable>
        </vue-custom-scrollbar>
      </div>
    </div>
    <div class="list-section-footer">
    </div>
  </div>
</template>

<script>
import uuid from 'uuid';
import axios from 'axios';
import draggable from 'vuedraggable';
import vueCustomScrollbar from 'vue-custom-scrollbar';

export default {
  name: 'EditExistingCodes',
  display: 'Transitions',
  components: {
    draggable,
    vueCustomScrollbar,
  },
  computed: {
    dragOptions() {
      return {
        animation: 200,
        group: 'description',
        disabled: false,
        ghostClass: 'ghost',
      };
    },
  },
  data() {
    return {
      abscodes: [{}],
      addCodeForm: {
        absence_code: '',
        description: '',
        customer_id: '',
        active: '',
        order_index: '',
        user_id: '',
      },
      editForm: {
        absence_code_pk: '',
        absence_code: '',
        description: '',
        customer_id: '',
        active: '',
        order_index: '',
        user_id: '',
      },
      message: '',
      showMessage: false,
      testURL: 'http://127.0.0.1:5000',
      settings: {
        maxScrollbarLength: 60,
      },
      drag: false,
    };
  },
  methods: {
    scrollHanle(evt) {
    },
    async getCodes() {
      let key = 0;
      const path = `${this.testURL}/absencecodes?customerId=admin`;
      axios.get(path)
        .then((response) => {
          this.abscodes = response.data;
          for (key = 0; key < this.abscodes.length; key += 1) {
            console.log(this.abscodes[key]);
          }
        })
        .catch((error) => {
          // eslint-disable-next-line
                  console.error(error);
        });
    },
    addCode(payload) {
      this.showMessage = false;
      const path = `${this.testURL}/absencecodes?user_pk=`;
      axios.post(path, payload)
        .then(() => {
          this.getCodes();
          this.message = 'Absence code has been Added!';
          // this.showMessage = true;
        })
        .catch((error) => {
          // eslint-disable-next-line
                  console.log(error);
          this.getCodes();
        });
    },
    initForm() {
      this.addCodeForm.absence_code = '';
      this.addCodeForm.description = '';
      this.addCodeForm.order_index = '';
      this.editForm.absence_code_pk = '';
      this.editForm.absence_code = '';
      this.editForm.description = '';
      this.editForm.order_index = '';
    },
    onSubmit(evt) {
      evt.preventDefault();
      this.$refs.addCodeModal.hide();
      const payload = {
        absence_code: this.addCodeForm.absence_code,
        description: this.addCodeForm.description,
        order_index: this.addCodeForm.order_index,
      };
      this.addCode(payload);
      this.initForm();
    },
    onReset(evt) {
      evt.preventDefault();
      this.$refs.addCodeModal.hide();
      this.initForm();
    },
    onResetUpdate(evt) {
      evt.preventDefault();
      this.$refs.editCodeModal.hide();
      this.initForm();
      this.getCodes(); // why?
    },
    onSubmitUpdate(evt) {
      evt.preventDefault();
      this.$refs.editCodeModal.hide();
      const payload = {
        absence_code_pk: this.editForm.absence_code_pk,
        absence_code: this.editForm.absence_code,
        description: this.editForm.description,
        order_index: this.editForm.order_index,
      };
      this.updateCode(payload);
    },
    updateCode(payload) {
      this.showMessage = false;
      const path = `${this.testURL}/absencecodeupdate`;
      axios.put(path, payload)
        .then(() => {
          this.getCodes();
          this.message = 'Absence code has been Updated!';
          // this.showMessage = true;
        })
        .catch((error) => {
          // eslint-disable-next-line
                  console.error(error);
          this.getCodes();
        });
    },
    editCode(code) {
      this.editForm = code;
    },
    deleteCode(code) {
      this.showMessage = false;
      const payload = {
        absence_code_pk: code,
      };
      const path = `${this.testURL}/absencecodedelete`;
      axios.put(path, payload)
        .then(() => {
          this.getCodes();
          this.message = 'Absence Code deleted!';
          // this.showMessage = true;
        })
        .catch((error) => {
          // eslint-disable-next-line
                  console.error(error);
          this.getCodes();
        });
    },
    goBacktoSetup() {
      window.parent.location.href = 'http://127.0.0.1:8080/app/setupOptions.do?setupoption';
    },
  },
  created() {
    this.getCodes();
  },
};
</script>

<style scope>

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
    font-style: normal!important;
    line-height: 2;
    color: #000000;
    align-items: center;
    width: 400px;
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
  .ps__thumb-y, .ps__thumb-y:hover{
    top: 0px;
    height: 20px!important;
    width: 20px;
    border-radius: 50%;
    left: -8px;
  }
  .ps__thumb-y, .ps__thumb-y{
    top: 0px;
    height: 20px!important;
    width: 20px;
    border-radius: 50%;
    left: -8px;
  }
  .ps__rail-y:hover > .ps__thumb-y,
  .ps__rail-y:focus > .ps__thumb-y,
  .ps__rail-y.ps--clicking .ps__thumb-y {
    background-color: #999;
    width: 20px;
    height: 20px!important;
  }
  .ps__rail-y, .ps__rail-y:hover{
    right: 0px;
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
  .list-item .code {
    width: 27%;
    text-align: left;
  }
  .list-item .description {
    text-align: left;
    width: 60%;
  }

  h4 {
    font-family: SegoeUI;
    font-weight: 600;
    font-stretch: normal;
    font-style: italic;
    line-height: 1.48;
    letter-spacing: normal;
    color: #b23737;
    margin:0;
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
    padding: .5rem 2.75rem 0.75rem 2.81rem;
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
    padding: .5rem 2.75rem 0.75rem 2.81rem;
    outline: none;
    border: none;
    cursor: pointer;
    transition: all ease-in-out 0.3s;
  }
  .btn-cancel:hover {
    background: #999797;
    color: white;
  }
  @media only screen and (max-width: 1024px){
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

  @media only screen and (max-width: 768px){
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

  @media only screen and (max-width: 576px){
    .list-section-body {
      display: block;
      padding: 0 ;
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
  @media only screen and (max-width: 425px){

  }
</style>
