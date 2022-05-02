<template>
  <div>
    <q-card class="card" v-if="show=='table'">
    <div class="q-pa-md row items-center q-gutter-md row justify-center" >
        <q-table 
          class="table"
          :title="label"
          :rows="data"
          :columns="columns"
          row-key="name"
        >
      <template v-slot:header="props">
        <q-tr :props="props">
          <q-th
            v-for="col in props.cols"
            :key="col.name"
            :props="props"
          >
            {{ col.label }}
          </q-th>
          <q-th auto-width />
          <q-th auto-width />
        </q-tr>
      </template>

 <template v-slot:body="props">
        <q-tr :props="props">
          <q-td 
            v-for="col in props.cols"
            :key="col.name"
            :props="props"
          >
      <div>
      {{col.value.substring(0,len)}}
     </div>    
     <q-tooltip v-if = "col.value.length >= len">
        {{col.value}} 
      </q-tooltip>     
          </q-td>
          <q-td auto-width>
            <q-btn size="sm"  @click="edit(props.row)" icon-right="edit" flat dense  />
          </q-td>
          <q-td auto-width>
            <q-btn size="sm"  @click="deleteRow(props.row)"  icon-right="delete" flat dense />
          </q-td>
        </q-tr>
 </template>
        </q-table>
      </div>
      <q-card-actions>
        <q-btn class="row centered" @click="add()">add</q-btn>
      </q-card-actions>
    </q-card>
    <div class="q-pa-md row items-center q-gutter-md row justify-center" v-if="show=='edit' || show=='add'">
    <q-card class = "q-pa-md my-card">
    <h6>{{label}}</h6>
      <q-card-section v-for="(el, index) in edit_buffer" :key="index">
       <q-input :label="el.name" v-model="el.value"/>
      </q-card-section>
      <q-card-actions>
       <q-btn @click="save()"> OK</q-btn>
       <q-btn @click="show='table'"> Cancel</q-btn>
      </q-card-actions>
    </q-card>
    </div>
  </div>
  </template>
<script setup>
import {ref, watch} from "vue";
import utils from 'jshelperutils'
let titles=[]
let columns=[]
let show=ref('table')
let edit_buffer = ref([])
let current_index = 0
let uuid = "-100"
const emits = defineEmits(["create",  "update", "delete"])
const props= defineProps({
  data: Array,
  definition: Array,
  name: String,
  ident: String,
  label: String,
  maxlength: String
  })
    let counter = 0;
    const fieldname = "item";
    const len =  props.maxlength? parseInt(props.maxlength) : 10000 
    props.definition.forEach(el => {
     titles.push(el.name)
    })
    titles.forEach(el => {
      let field = fieldname + counter.toString();
      const element = {
        name: el,
        label: el,
        align: "left",
        field: el
      };
      columns.push(element);
      counter = counter + 1;
    });
    //added uuid to the data for identification of the index
    
    watch(()=> props.data,(a,b)=>{
     console.log("Data changed")
     console.log(a)
     console.log(b) 
    })

    function edit(row) {
       edit_buffer.value=[]
        let keys = Object.keys(row)
        uuid=row.__uuid
        keys.forEach(el => {
        if(utils.isObjectInArray(columns, el, 'field')) {
          edit_buffer.value.push({name: el , value: row[el] })
        }
        })
       show.value='edit'
    }

    function save() {
     if(show.value=='edit'){ 
     const content = edit_buffer.value
     const payload = {ident: props.ident, id: uuid, index: current_index, buffer: content}
     emits("update", payload)
     }
     if(show.value=='add'){ 
       const content = edit_buffer.value
       const payload = { ident: props.ident, buffer: content}
       emits("create",payload)
     }
      show.value="table"
    }
    function cancel(){
        show.value='table'
        console.log("cancel pressed")
    }
    function deleteRow(row) {
      emits("delete",{id:row.__uuid, ident: props.ident})
    }

    function add() {
    edit_buffer.value=[];
    props.definition.forEach(el => {
        let element = {}
        element["name"] = el.name;
        element["value"] = "";
        element["__uuid"]="newElement - Please create UUID";
        edit_buffer.value.push(element);
      });
      show.value="add" 
    }
</script>
<style lang="scss" scoped>
.my-card{
  width: 100%;
  max-width: 550px;
}

.table{
  table-layout: fixed;
  min-width: 300px;
}


.test {
  max-width: 100px;
  display: inline-block;
  color: green;
  overflow-wrap: break-word; 
 
 }

.card {
  display: inline-block;
  max-width: 400px;
}



</style>