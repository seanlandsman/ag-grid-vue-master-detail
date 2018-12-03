<template>
    <div style="height: 600px">
        <div style="height: 100%; padding-top: 35px; box-sizing: border-box;">
            <ag-grid-vue style="width: 100%; height: 100%;" class="ag-theme-balham"
                         :gridOptions="gridOptions"
                         :gridReady="onGridReady"
                         :columnDefs="columnDefs"
                         :masterDetail="true"
                         :detailRowHeight="detailRowHeight"
                         :detailCellRenderer="detailCellRenderer"
                         :frameworkComponents="frameworkComponents"
                         :rowData="rowData"></ag-grid-vue>
        </div>
        <div style="position: absolute; top: 0px; left: 0px;">
            <button v-on:click="printDetailGridInfo()">Print Detail Grid Info</button>
        </div>
    </div>
</template>

<script>
    import {AgGridVue} from "ag-grid-vue";
    import "ag-grid-enterprise";
    import DetailCellRenderer from "./components/detailCellRenderer";

    export default {
        components: {
            "ag-grid-vue": AgGridVue
        },
        data: function () {
            return {
                gridOptions: null,
                gridApi: null,
                columnApi: null,
                columnDefs: null,
                detailRowHeight: null,
                detailCellRenderer: null,
                frameworkComponents: null,
                rowData: []
            };
        },
        beforeMount() {
            this.gridOptions = {};
            this.columnDefs = [
                {
                    field: "name",
                    cellRenderer: "agGroupCellRenderer"
                },
                {field: "account"},
                {field: "calls"},
                {
                    field: "minutes",
                    valueFormatter: "x.toLocaleString() + 'm'"
                }
            ];
            this.detailRowHeight = 260;
            this.detailCellRenderer = "myDetailCellRenderer";
            this.frameworkComponents = {myDetailCellRenderer: DetailCellRenderer};
        },
        mounted() {
            this.gridApi = this.gridOptions.api;
            this.gridColumnApi = this.gridOptions.api.columnApi;
        },
        methods: {
            printDetailGridInfo() {
                console.log("Currently registered detail grid's: ");
                this.gridApi.forEachDetailGridInfo(function (detailGridInfo) {
                    console.log(detailGridInfo);
                });
            },
            onGridReady(params) {
                const httpRequest = new XMLHttpRequest();
                const updateData = data => {
                    this.rowData = data;
                };

                httpRequest.open(
                    "GET",
                    "https://raw.githubusercontent.com/ag-grid/ag-grid-docs/latest/src/javascript-grid-master-detail/custom-detail-with-grid/data/data.json"
                );
                httpRequest.send();
                httpRequest.onreadystatechange = () => {
                    if (httpRequest.readyState === 4 && httpRequest.status === 200) {
                        updateData(JSON.parse(httpRequest.responseText));
                    }
                };

                setTimeout(function () {
                    var rowCount = 0;
                    params.api.forEachNode(function (node) {
                        node.setExpanded(rowCount++ === 1);
                    });
                }, 500);
            }
        }
    };
</script>

<style>
</style>

