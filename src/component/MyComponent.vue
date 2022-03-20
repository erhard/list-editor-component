<template>
  <div>
    <q-card v-if="show=='table'">
    <div class="q-pa-md row items-center q-gutter-md row justify-center" >
        <q-table
          :title="label"
          :rows="rows"
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
            {{ col.value }}
          </q-td>
          <q-td auto-width>
            <q-btn size="sm"  @click="edit(props.row)" icon-right="edit" flat dense  />
          </q-td>
          <q-td auto-width>
            <q-btn size="sm"  @click="deleteIt(props.row)"  icon-right="delete" flat dense />
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
<script>
import {getIndexOfObjectInArray} from "src/services/artbarrack/utils"
import EventBus from 'vjseventbus';
import { uid } from 'quasar';
export default {
  data() {
    return {
      show: 'table',
      columns: [],
      rows: [],
      titles: [],
      edit_buffer: [],
      current_index: 0
    };
  },

  props: {
    data: {
      type: Array,
      default: () => {
        return [];
      }
    },
    label: {
      type: String,
      default: ""
    },
    definition: {
      type: Array,
      default: () => {
        return [];
      }
  },
  ident: {
      type: String,
      default: ""
  },
  name: {
    type: String,
    default: "LISTEDITOR"
  }

  },


   watch:{
       data (val) {
           console.log("data changed");
           console.log(val)
           this.rows=val
       }
   },



  mounted() {
    let counter = 0;
    const fieldname = "item";
    this.definition.forEach(el => {
      this.titles.push(el.name)
    })
    this.titles.forEach(el => {
      let field = fieldname + counter.toString();
      const element = {
        name: el,
        label: el,
        align: "left",
        field: el
      };
      this.columns.push(element);
      counter = counter + 1;
    });
    //added uuid to the data for identification of the index
     
    this.rows = this.data.map(element=>{
        element["__uuid"] = uid()
        return element
    })
    console.log("rows with UUID");
    console.log(this.rows);
  },

  methods: {
    save() {
    console.log("save edit");
    console.log(this.rows);
    console.log(this.edit_buffer);
    //this.rows[this.current_index] = this.edit_buffer
      console.log("data");
      console.log(this.rows);
     if(this.show=='edit'){ 
      EventBus.$emit(this.name + ":update", {ident: this.ident, index: this.current_index, buffer: this.edit_buffer})
     }
     if(this.show=='add'){ 
      EventBus.$emit(this.name + ":create", { ident: this.ident, id: uid(), buffer: this.edit_buffer})
     }

      this.show="table"
    },
    cancel(){
        show='table'
    },
    add() {
      this.edit_buffer=[];
      this.definition.forEach(el => {
        let element = {}
        element["name"] = el.name;
        element["value"] = "";
        element["__uuid"]=uid();
        this.edit_buffer.push(element);
      });
      this.show="add"
    },
    deleteIt(row){
        const id = row.__uuid;
        this.current_index = getIndexOfObjectInArray(this.rows,"__uuid",id)
        EventBus.$emit(this.name +":delete",  this.current_index, this.ident)
    },

    edit(row){
     this.edit_buffer=[]
       this.show='edit'
        const id=row["__uuid"];
        this.current_index = getIndexOfObjectInArray(this.rows,"__uuid",id)
        //Find Index
        let keys = Object.keys(row)
        keys.forEach(el => {
        console.log("keys");
        console.log(keys);
        if(el !="__uuid"){
        this.edit_buffer.push({name: el , value: row[el] })
        }
        })
    }
  }
};
</script>
<style lang="scss" scoped>
.my-card{
  width: 100%;
  max-width: 550px;
}
</style>