<template>
  <div class="dashboard">
    <template v-if="element==1">
      <add-new-code></add-new-code>
    </template>
    <template v-else-if="element==2">
      <edit-existing-code></edit-existing-code>
    </template>
    <template v-else-if="element==3">
      <view-all></view-all>
    </template>
    <template v-else-if="element==4">
      <view-all></view-all>
    </template>
    <template v-else-if="element==5">
      <code-all></code-all>
    </template>
  </div>
</template>
<script>
import AddNewCode from './AddNewCode.vue'
import EditExistingCode from './EditExistingCode.vue'
import ViewAll from './ViewAll.vue'
import CodeAll from './CodeAll.vue'

export default {
  name: 'Dashboard',
  components: {
    AddNewCode, EditExistingCode, ViewAll, CodeAll
  },
  props: ['element'],
  data () {
    return {
    }
  }
}
</script>
<style>

.dashboard {
  width: 85%;
}

@media only screen and (max-width: 1440px) {
  .dashboard {
    width: 75%;
  }
}
@media only screen and (max-width: 1024px) {
  .dashboard {
    width: 100%;
  }

}
</style>
