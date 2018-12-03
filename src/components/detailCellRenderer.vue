<template>
    <div class="full-width-panel">
        <div class="full-width-details">
            <div class="full-width-detail"><b>Name: </b>{{params.data.name}}</div>
            <div class="full-width-detail"><b>Account: </b>{{params.data.account}}</div>
        </div>
        <ag-grid-vue style="width: 100%; height: 100%;"
                     class="full-width-grid"
                     :gridOptions="gridOptions"
                     :columnDefs="colDefs"
                     :rowData="rowData"
                     :gridReady="onGridReady">
        </ag-grid-vue>
    </div>
</template>

<script>
    import Vue from 'vue'
    import {AgGridVue} from "ag-grid-vue";
    export default Vue.extend({
        components: {
            'ag-grid-vue': AgGridVue
        },
        data: function () {
            return {
                gridOptions: null,
                colDefs: null,
                rowData: null
            };
        },
        beforeMount() {
            this.gridOptions = {debug: true};
            this.colDefs = [
                {field: 'callId'},
                {field: 'direction'},
                {field: 'number'},
                {field: 'duration', valueFormatter: "x.toLocaleString() + 's'"},
                {field: 'switchCode'}
            ];
        },
        mounted() {
            this.rowData = this.params.data.callRecords;
            this.rowIndex = this.params.rowIndex;
            this.masterGridApi = this.params.api;
        },
        beforeDestroy() {
            let detailGridId = "detail_" + this.rowIndex;

            // ag-Grid is automatically destroyed

            console.log("removing detail grid info with id: ", detailGridId);
            this.masterGridApi.removeDetailGridInfo(detailGridId);
        },
        methods: {
            onGridReady(params) {
                let detailGridId = "detail_" + this.rowIndex;

                let gridInfo = {
                    id: detailGridId,
                    api: params.api,
                    columnApi: params.columnApi
                };

                console.log("adding detail grid info with id: ", detailGridId);
                this.masterGridApi.addDetailGridInfo(detailGridId, gridInfo);
            }
        }
    })
</script>
<style>
    .full-width-panel {
        position: relative;
        background: #EDF6FF;
        height: 100%;
        width: 100%;
        padding: 5px;
        box-sizing: border-box;
    }

    .call-record-cell {
        text-align: right;
    }

    .full-width-detail {
        padding-top: 4px;
    }

    .full-width-details {
        float: left;
        padding: 5px;
        margin: 5px;
        width: 150px;
    }

    .full-width-grid {
        margin-left: 150px;
        padding-top: 25px;
        box-sizing: border-box;
        display: block;
        height: 100%;
    }

    .full-width-grid-toolbar {
        top: 4px;
        left: 30px;
        margin-left: 150px;
        display: block;
        position: absolute;
    }

    .full-width-phone-icon {
        padding-right: 10px;
    }

    .full-width-search {
        border: 1px solid #eee;
        margin-left: 10px;
    }
</style>
