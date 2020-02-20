<template>
  <div class="menu-container">
    <ul>
      <li @click="onMenuClick(1)">
        <img src="../assets/folder.png" alt="">
        <span>Add New Code</span>
      </li>
      <li @click="onMenuClick(2)">
        <img src="../assets/settings2.png" alt="">
        <span>Edit Exiting Codes</span>
      </li>
      <li @click="onMenuClick(3)">
        <img src="../assets/view_folder.png" alt="">
        <span>View All Codes</span>
      </li>
      <li @click="onMenuClick(4)">
        <img src="../assets/animal-paw-print.png" alt="">
        <span>Print</span>
      </li>
      <li @click="onMenuClick(5)">
        <img src="../assets/view_folder.png" alt="">
        <span>Code All</span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'Menu',
  methods: {
    onMenuClick (value) {
      this.$emit('change-menu', value)
    }
  }
}
</script>
<style>
.menu-container {
  width: 25%;
  padding: 20px;
}
ul {
  margin: 0;
  padding: 0;
  list-style: none;
}
li {
  display: flex;
  align-items: center;
  padding: 20px;
  cursor: pointer;
  transition: all ease-in-out 0.3s;
}
li:hover {
  opacity: 0.7;
}
li:active {
  opacity: 1;
}
li span {
    font-family: SegoeUI;
    font-size: 18px;
    font-weight: normal;
    font-stretch: normal;
    font-style: normal;
    line-height: 1.35;
    letter-spacing: normal;
    text-align: center;
    color: #202020;
}
img {
  width: 30px;
  height: 30px;
  margin-right: 15px;
}

@media only screen and (max-width: 1440px) {
  .menu-container {
    width: 25%;
  }
}
@media only screen and (max-width: 1024px) {
  .menu-container {
    width: 100%;
    padding: 0;
  }
  ul {
    display: flex;
    justify-content: space-between;
  }
  li {
    padding: 30px 0;
  }
}

@media only screen and (max-width: 576px) {
  span {
    font-size: 16px;
  }
}

</style>
