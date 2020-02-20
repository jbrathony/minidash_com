<template>
    <div class="add-section">
        <h2>Enter New Code Details</h2>
        <form action="">
            <input v-model="code" type="text" name="code" id="code" placeholder="Code" ref="code">
            <input v-model="description" type="text" name="description" id="description" placeholder="Description" @keyup.enter="addList">
            <input v-model="location" type="text" name="location" id="location" placeholder="Location" ref="location">
            <input v-model="order" type="text" name="order" id="order" placeholder="order" ref="order">
            <button class="plus-black-symbol" @click="addList"></button>
            <button class="rubber" @click="cleanList"></button>
        </form>
    </div>
</template>

<script>
import uuid from 'uuid'
import axios from 'axios'

export default {
  name: 'AddCode',
  data () {
    return {
      id: '',
      code: '',
      description: '',
      location: '',
      order: '',
      codeExists: true,
      testURL: 'http://127.0.0.1:5000',
    };
  },
  methods: {
    existsCode (payload) {
      this.showMessage = false
      const path = `${this.testURL}/absencecodeexists`
      return axios.put(path, payload)
        .then((response) => {
          this.message = 'Absence code has been Added!'
          // this.showMessage = true;
          const exists = response.data
          if (exists > 0) {
            this.codeExists = true
            console.log('Code exists')
          } else {
            this.codeExists = false;
            console.log('Code does not exist')
          }
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.log(error)
          this.codeExists = true
          console.log('Code exists - error')
        })
    },
    async addList (e) {
      e.preventDefault()
      const newList = {
        code: this.code,
        description: this.description,
        location: this.location,
        order: this.order
      }
      const newCode = {
        absence_code: this.code,
        customer_id: 'admin'
      }
      // await this.existsCode(newCode)
      // if (!this.codeExists) {
      this.$emit('add-list', newList)
      this.description = ''
      this.code = ''
      this.location = ''
      this.order = ''
      this.$refs.code.focus()
      // } else {
      // this.$refs.code.focus();
      // }
    },
    cleanList (e) {
      e.preventDefault()
      this.$emit('clean-list')
    }
  }
}
</script>

<style scope>
    .add-section {
        padding-bottom: 20px;
        border-bottom: 1px solid black;
        margin-right: 100px;
    }
    h2 {
        opacity: 0.79;
        font-family: SegoeUI;
        font-size: 1.3625rem;
        font-weight: 600;
        font-stretch: normal;
        font-style: normal;
        line-height: 1.03;
        letter-spacing: normal;
        color: #202020;
        margin: 0;
        padding-bottom: 30px;
   }
   form {
        display: flex;
        align-items: center;
        justify-content: space-between;
        flex-wrap: wrap;
   }
   input[id='code'] {
        display: block;
        border: solid 1px #707070;
        background-color: #ffffff;
        padding: 0.425rem;
        font-family: SegoeUI;
        font-weight: 300;
        font-stretch: normal;
        font-style: italic;
        line-height: 1.42;
        letter-spacing: normal;
        text-align: left;
        color: #202020;
        width: 5.625rem;
        font-size: 1rem;
   }
   input[id='description'] {
        display: block;
        border: solid 1px #707070;
        background-color: #ffffff;
        padding: 0.425rem;
        font-family: SegoeUI;
        font-weight: 300;
        font-stretch: normal;
        font-style: italic;
        line-height: 1.42;
        letter-spacing: normal;
        text-align: left;
        color: #202020;
        width: 10.5625rem;
        font-size: 1rem;
   }
   input[id='location'] {
        display: block;
        border: solid 1px #707070;
        background-color: #ffffff;
        padding: 0.425rem;
        font-family: SegoeUI;
        font-weight: 300;
        font-stretch: normal;
        font-style: italic;
        line-height: 1.42;
        letter-spacing: normal;
        text-align: left;
        color: #202020;
        width: 10.5625rem;
        font-size: 1rem;
   }
   input[id='order'] {
        display: block;
        border: solid 1px #707070;
        background-color: #ffffff;
        padding: 0.425rem;
        font-family: SegoeUI;
        font-weight: 300;
        font-stretch: normal;
        font-style: italic;
        line-height: 1.42;
        letter-spacing: normal;
        text-align: left;
        color: #202020;
        width: 10.5625rem;
        font-size: 1rem;
   }
   .plus-black-symbol  {
        background-image: url(../assets/plus-black-symbol.png);
        background-repeat: no-repeat;
        border: none;
        background-color: transparent;
        background-size: contain;
        width: 1.3625rem;
        height: 1.3625rem;
        object-fit: contain;
        opacity: 0.67;
        cursor: pointer;
        opacity: 0.7;
        transition: all ease-in-out 0.2s;

   }
   .plus-black-symbol:active {
       opacity: 1;
   }
   .rubber  {
        background-image: url(../assets/rubber.png);
        background-repeat: no-repeat;
        border: none;
        background-color: transparent;
        background-size: contain;
        width: 1.3625rem;
        height: 1.3625rem;
        object-fit: contain;
        opacity: 0.67;
        cursor: pointer;
        opacity: 0.7;
        transition: all ease-in-out 0.2s;

   }
   .rubber:active {
       opacity: 1;
   }
  @media only screen and (max-width: 1024px){
       .add-section {
           margin-right: 0;
       }
       input[id='code'] {
           width: 14.625rem;
       }
       input[id='description'] {
            width: 32.5625rem;
       }
   }

    @media only screen and (max-width: 992px){

        form {
            padding: 0;
        }
        input[id='code'] {
            width: 25%;
        }
        input[id='description'] {
            width: 60%;
        }
   }

    @media only screen and (max-width: 768px){
       .add-section {
            margin: 0 ;
        }
        input[id='code'] {
            width: 25%;
        }
        input[id='description'] {
            width: 55%;
        }
        form {
            padding: 0;
        }
   }
   @media only screen and (max-width: 576px){
       .add-section {
            padding: 1.875rem 0 1.787rem 0;
       }
       h2 {
           font-size: 1.3625rem;
       }
       input[id='code'] {
           padding: 0.425rem;
           width: 17.625rem;
           margin-bottom: 15px;
       }
       input[id='description'] {
           padding: 0.425rem;
           width: 80%;
       }
       .plus-black-symbol {
           width: 8%;
       }
       .rubber {
           width: 8%;
       }
   }
   @media only screen and (max-width: 425px){
       input[id='code'] {
            width: 14.625rem;
        }
        input[id='description'] {
            width: 72%;
        }
    }
</style>
