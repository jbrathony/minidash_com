<template>
  <div>
    <AddCode v-on:add-list="addList" v-on:clean-list="cleanList"/>
    <CodeList v-bind:lists="lists" @change="onChange" @delete-item="deleteItem"/>
  </div>
</template>
<script>
import AddCode from './AddCode.vue'
import CodeList from './CodeList.vue'

export default {
  components: {
    AddCode, CodeList
  },
  data () {
    return {
      lists: []
    }
  },
  methods: {
    addList (obj) {
      if (obj.code && obj.description && obj.location && obj.order) {
        this.lists.push(obj)
      }
    },
    onChange (newArray) {
      this.lists = newArray
    },
    cleanList () {
      // eslint-disable-next-line prefer-destructuring
      const length = this.lists.length
      for (let i = 0; i < length; i += 1) {
        this.lists.pop()
      }
    },
    deleteItem (id) {
      const self = this
      let index = -1
      // eslint-disable-next-line array-callback-return
      this.lists.findIndex((list, ind) => {
        if (list.id === id) {
          index = ind
        }
      });
      if (index !== -1) { self.lists.splice(index, 1) }
    },
  },
};
</script>
