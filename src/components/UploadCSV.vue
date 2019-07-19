<template >
    <v-container style="width: 80%">
        <v-layout v-if="!submitting"
                  row align-center
        >
            <v-flex xs12>
                <div class="my-2">
                    <input type="file" id="uploaddata" style="display: none" v-on:change="handleFileChange"/>
                    <v-btn color="primary" dark large v-on:click="uploadcsv">Upload Data(csv/excel)</v-btn>
                </div>
            </v-flex>
        </v-layout>
        <v-flex xs12 v-if ="submitting">
            <div>
                <span class="header1" >Column Headings & Data Info</span><br/>
                <span class="header2" >Row Count {{data.length}} Column Count: 8</span>
            </div>
        </v-flex>
        <v-layout
            v-if ="submitting"
        >

            <template>
                <v-data-table
                        :headers="headers"
                        :items="data"
                        :pagination.sync="pagination"
                        select-all
                        item-key="name"
                        class="elevation-1"
                >
                    <template v-slot:headers="props">
                        <tr>
                            <th>

                            </th>
                            <th
                                    v-for="header in props.headers"
                                    :key="header.text"
                                    :class="['column sortable', pagination.descending ? 'desc' : 'asc', header.value === pagination.sortBy ? 'active' : '']"
                                    @click="changeSort(header.value)"
                            >
                                <v-icon small>arrow_upward</v-icon>
                                {{ header.text }}
                            </th>
                        </tr>
                    </template>
                    <template v-slot:items="props">
                        <tr  @click="selected = props.item.id">
                            <td>
                                <v-radio-group
                                        v-model="selected"
                                        name="rowSelector">
                                    <v-radio :value="props.item.id"/>
                                </v-radio-group>
                            </td>

                            <td class="text-xs-right">{{ props.item.name }}</td>
                            <td class="text-xs-right">{{ props.item.min }}</td>
                            <td class="text-xs-right">{{ props.item.max }}</td>
                            <td class="text-xs-right">{{ props.item.mode }}</td>
                            <td class="text-xs-right">{{ props.item.mean }}</td>
                            <td class="text-xs-right">{{ props.item.median }}</td>
                            <td class="text-xs-right">{{ props.item.range }}</td>
                        </tr>
                    </template>
                </v-data-table>
            </template>
        </v-layout>
    </v-container>
</template>

<script>
    export default {
        name: "UploadCSV",
        data() {
            return {
                submitting: false,
                error: false,
                success: false,
                fileinput : null,
                rows : [],
                cols : [],
                data : [],
                selected: 0,
                pagination: {
                    sortBy: 'name'
                },
                headers: [
                    {
                        text: 'Name',
                        align: 'left',
                        sortable: false,
                        value: 'name'
                    },
                    { text: 'Min', value: 'min' },
                    { text: 'Max', value: 'max' },
                    { text: 'Mode', value: 'mode' },
                    { text: 'Mean', value: 'mean' },
                    { text: 'Median', value: 'median' },
                    { text: 'Range', value: 'range' }
                ],

            }
        },
        methods: {
            uploadcsv() {
                let elem = document.getElementById('uploaddata');
                elem.click();

            },
            handleFileChange(){
                const input = document.getElementById('uploaddata');
                const file = input.files[0];
                const reader = new FileReader();
                reader.onload = (e) => {
                    this.fileinput = e.target.result;
                    this.makerowcols(this.fileinput);
                }
                reader.readAsText(file);
            },
            makerowcols(data){
                const input = data;
                let rows =[];
                let resrow =[];
                rows = input.split("\n");
                if(rows && rows.length ){
                    rows.forEach((row)=>{
                        let m = row.split(",");
                        resrow.push(m);
                    });
                }
                this.rows = resrow;
                this.makedata(resrow);
            },
            getmode(input){
                let count = [];
                for (let i=0; i < input.length; i++) {
                    count[input[i]]++;
                }
                let index = count.length-1;
                for (let i=count.length-2; i >=0; i--) {
                    if (count[i] >= count[index])
                        index = i;
                }
                return index;
            },
            makedata(getrows){
                const rows = getrows;
                const cols = getrows.length ? getrows[0] :[];
                let rescol =[];
                // let data =[];
                if(cols && cols.length){
                    cols.forEach((col,index)=>{
                        let midcols=[];
                        rows.forEach((row)=>{
                            midcols.push(row[index]);
                        });
                        rescol.push(midcols);
                    });
                }
                if(rescol && rescol.length ){
                    let resdata=[];
                    rescol.forEach((col,index)=>{
                        let name = "";
                        let min = null;
                        let max = null;
                        let mode = 0;
                        let mean = 0;
                        let median = 0;
                        let range = 0;
                        let sum = 0;
                        let len = 0;
                        let mmedian=[];
                        if(isNaN(col[0])){
                            name = col[0];
                        }
                        col.forEach((item)=>{

                            if(!isNaN(item)){

                                if(min === null || parseFloat(min) > parseFloat(item) ){
                                    min = parseFloat(item)
                                }
                                if(max === null || parseFloat(max) < parseFloat(item)){
                                    max = parseFloat(item);
                                }
                                mmedian.push(parseFloat(item));
                                sum += parseFloat(item);
                                len ++;
                            }
                        });
                        if(len){
                            mean = parseFloat(sum /len).toFixed(2);
                        }
                        if(max !== null && min !== null){
                            range = max - min;
                        }
                        mmedian.sort((a,b)=>{return a - b});
                        if(mmedian.length %2 ===0){
                            median = parseFloat(parseFloat(mmedian[mmedian.length/2]) + parseFloat(mmedian[mmedian.length/2 - 1])/2).toFixed(2);
                        }
                        else{
                            median = parseFloat(mmedian[(mmedian.length - 1)/2]).toFixed(2);
                        }
                        mode = this.getmode(mmedian);
                        let mdata ={
                            name : name,
                            min  : min,
                            max  : max,
                            mode  : mode,
                            mean  : mean,
                            median  : median,
                            range  : range,
                            data:mmedian,
                            id : index
                        };
                        if(min !== null && max !== null && len ){
                            resdata.push(mdata);
                        }
                    });
                    this.data = resdata;
                    console.log(resdata,'resdata');
                    // data = resdata;
                }
                this.cols = rescol;
                this.changesubmit();
            },
            changesubmit(){
                this.submitting = !this.submitting;
            },
            changeSort (column) {
                if (this.pagination.sortBy === column) {
                    this.pagination.descending = !this.pagination.descending
                } else {
                    this.pagination.sortBy = column
                    this.pagination.descending = false
                }
            }

        }

    }
</script>

<style scoped>
    table.v-table thead td:not(:nth-child(1)), table.v-table tbody td:not(:nth-child(1)), table.v-table thead th:not(:nth-child(1)), table.v-table tbody th:not(:nth-child(1)), table.v-table thead td:first-child, table.v-table tbody td:first-child, table.v-table thead th:first-child, table.v-table tbody th:first-child{
        padding-left: 10px !important;
        padding-right: 10px !important;
        padding-top: 5px !important;
    }
    .header1{
        font-size : 20px;
        font-weight: 800;
    }
    .header2{
        font-size: 18px;
        font-weight: 700;
    }
</style>