<template>
    
    <div id="vueapp" class="vue-app">
        <Grid ref="grid"
            :style="{height: '440px'}"
            :data-items="gridData"
            :edit-field="'inEdit'"
            @edit="edit"
            @remove="remove"
            @save="save"
            @cancel="cancel"
            @itemchange="itemChange"
            :columns="columns">
            <grid-toolbar>
                <button title="Add new"
                        class="k-button k-primary"
                        @click='insert' >
                    Add new
                </button>
                <button v-if="hasItemsInEdit"
                        title="Cancel current changes"
                        class="k-button"
                        @click="cancelChanges">
                        Cancel current changes
                </button>
            </grid-toolbar>
            <grid-norecords>
                There is no data available custom
            </grid-norecords>
        </Grid>
    </div>
</template>


<script>
import Vue from 'vue';
import uuid from 'uuid';
import axios from 'axios';
import { Grid, GridToolbar, GridNoRecords } from '@progress/kendo-vue-grid';
import { process } from '@progress/kendo-data-query';

const CommandCell = Vue.component("template-component", {
    props: {
        field: String,
        dataItem: Object,
        format: String,
        className: String,
        columnIndex: Number,
        columnsCount: Number,
        rowType: String,
        level: Number,
        expanded: Boolean,
        editor: String
    },
    template: ` <td v-if="!dataItem['inEdit']">
                    <button
                        class="k-primary k-button k-grid-edit-command"
                        @click="editHandler">
                        Edit
                    </button>
                    <button
                        class="k-button k-grid-remove-command"
                        @click="removeHandler">
                        Remove
                    </button>
                </td>
                <td v-else>
                       <button
                           class="k-button k-grid-save-command"
                           @click="addUpdateHandler">
                           {{this.dataItem.ProductID? 'Update' : 'Add'}}
                       </button>
                       <button
                           class="k-button k-grid-cancel-command"
                           @click="cancelDiscardHandler">
                           {{this.dataItem.ProductID? 'Cancel' : 'Discard'}}
                       </button>
                   </td>`,
    methods: {
        editHandler: function() {
            this.$emit('edit', {dataItem:this.dataItem});
        },
        removeHandler: function() {
            this.$emit('remove', {dataItem:this.dataItem});
        },
        addUpdateHandler: function() {
            this.$emit('save', {dataItem:this.dataItem});
        },
        cancelDiscardHandler: function() {
            this.$emit('cancel', {dataItem:this.dataItem});
        }
    }
});

export default {
    name: 'AbsenceCodes',
    display: 'Transitions',
    components: {
        Grid,
        GridToolbar,
        GridNoRecords,
    },
    data () {
        return {
            updatedData: [],
            editID: null,
            group: [ { field: 'ProductID' } ],
            expandedItems: [],
            columns: [
                { field: 'ProductID', editable: false, title: 'ID', width: '50px' },
                { field: 'Code', title: 'Code' },
                { field: 'Description', title: 'Description' },
                { cell: CommandCell, width: '180px' }
            ],
            gridData: [{
                "ProductID": 1,
                "Code": "FST",
                "Description": 'Festival Duty'
            },{
                "ProductID": 2,
                "Code": "KID",
                "Description": 'Sick Kid'
            },{
                "ProductID": 3,
                "Code": "BANGHEAD",
                "Description": 'Banged Head Against Keyboard Too Much'
            },{
                "ProductID": 4,
                "Code": "OUCH",
                "Description": 'OUCH'
            }]
        };
    },
    computed: {
        hasItemsInEdit() {
            return this.gridData.filter(p => p.inEdit).length > 0;
        }
    },
    mounted () {
      this.updatedData = JSON.parse(JSON.stringify(this.gridData));
    },
    methods: {
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
                console.error(error);
            });
        },
        itemChange: function (e) {
            if (e.dataItem.ProductID) {
              let item = this.gridData.find(p => p.ProductID === e.dataItem.ProductID);
              let index = this.gridData.findIndex(p => p.ProductID === item.ProductID);
              Vue.set(item, e.field, e.value);
            } else {
              Vue.set(e.dataItem, e.field, e.value);
            }
        },
        insert() {
            const dataItem = { inEdit: true, Discontinued: false };
            const newproducts = this.gridData.slice();
            newproducts.unshift(dataItem);
            Vue.set(this, "gridData", newproducts);
        },
        edit: function (e) {
            Vue.set(e.dataItem, 'inEdit', true);
        },
        save: function(e) {
            Vue.set(e.dataItem, 'inEdit', undefined);
            let item = this.gridData.find(p => p.ProductID === e.dataItem.ProductID);
            let index = this.gridData.findIndex(p => p.ProductID === item.ProductID);

            Vue.set(this.gridData, index, this.update(this.gridData.slice(), item));
            Vue.set(this.gridData[index], 'inEdit', undefined);
            this.updatedData = JSON.parse(JSON.stringify(this.gridData));
        },
        update(data, item, remove) {
            let updated;
            let index = data.findIndex(p => item.ProductID && p.ProductID === item.ProductID);
            if (index >= 0) {
                updated = Object.assign({}, item);
                data[index] = updated;
            } else {
                let id = 1;
                data.forEach(p => { if (p.ProductID) { id = Math.max(p.ProductID + 1, id); } });
                updated = Object.assign({}, item, { ProductID: id });
                data.unshift(updated);
                index = 0;
            }

            if (remove) {
                data = data.splice(index, 1);
            }
            return data[index];
        },
        cancel(e) {
            if (e.dataItem.ProductID) {
                let originalItem = this.updatedData.find(p => p.ProductID === e.dataItem.ProductID);
                let index = this.updatedData.findIndex(p => p.ProductID === originalItem.ProductID);

                Vue.set(originalItem, 'inEdit', undefined);
                Vue.set(this.gridData, index, originalItem);
            } else {
                this.update(this.gridData, e.dataItem, true);
            }
        },
        remove(e) {
            e.dataItem.inEdit = undefined;
            this.update(this.gridData, e.dataItem, true);
            this.update(this.updatedData, e.dataItem, true);
            this.gridData = this.gridData.slice();
        },
        cancelChanges () {
             let editedItems = this.updatedData.filter(p => p.inEdit === true);
             if(editedItems.length){
                editedItems.forEach(
                    item => {
                       Vue.set(item, 'inEdit', undefined);
                     });
             }
            Vue.set(this, 'gridData', this.updatedData.slice());
        }
    }
}

</script>
<style scope>
    html, body { overflow: hidden; }
    body { font-family: "RobotoRegular",Helvetica,Arial,sans-serif; font-size: 14px; margin: 0; }
    #vueapp { display: block; width: 100%; overflow: hidden; min-height: 80px; box-sizing: border-box; padding: 30px; }
    .example-wrapper { min-height: 280px; align-content: flex-start; }
    .example-wrapper p, .example-col p { margin: 0 0 10px; }
    .example-wrapper p:first-child, .example-col p:first-child { margin-top: 0; }
    .example-col { display: inline-block; vertical-align: top; padding-right: 20px; padding-bottom: 20px; }
    .example-config { margin: 0 0 20px; padding: 20px; background-color: rgba(0,0,0,.03); border: 1px solid rgba(0,0,0,.08); }
    .event-log { margin: 0; padding: 0; max-height: 100px; overflow-y: auto; list-style-type: none; border: 1px solid rgba(0,0,0,.08); background-color: #fff; }
    .event-log li {margin: 0; padding: .3em; line-height: 1.2em; border-bottom: 1px solid rgba(0,0,0,.08); }
    .event-log li:last-child { margin-bottom: -1px;}
</style>