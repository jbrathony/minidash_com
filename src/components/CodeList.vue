<template>
    <div class="list-section">
        <h4>Recently added codes</h4>
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
                    <draggable v-model="loc_lists" @start="drag = true" @end="drag = false" v-bind="dragOptions">
                        <transition-group type="transition" :name="!drag ? 'flip-list' : null">
                            <div v-for="list in loc_lists" :key="list.id">
                                <div class="list-item normal">
                                    <div class="code">{{list.code}}</div>
                                    <div class="description">{{list.description}}</div>
                                    <span class="delete-item" @click="deleteItem(list.id)">&times;</span>
                                </div>
                            </div>
                        </transition-group>
                    </draggable>
                </vue-custom-scrollbar>
            </div>
        </div>
        <div class="list-section-footer">
            <button class="btn-submit">Submit</button>
            <button class="btn-cancel">Cancel</button>
        </div>
    </div>
</template>

<script>
import draggable from 'vuedraggable';
import vueCustomScrollbar from 'vue-custom-scrollbar';

export default {
  name: 'CodeList',
  props: ['lists'],
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
      drag: false,
      loc_lists: this.lists,
      settings: {
        maxScrollbarLength: 60,
      },
    };
  },
  watch: {
    loc_lists(value) {
      this.$emit('change', value);
    },
  },
  methods: {
    scrollHanle(evt) {
    },
    deleteItem(id) {
      this.$emit('delete-item', id);
    },
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
