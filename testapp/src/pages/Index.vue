<template>
  <q-page class="flex flex-center">
   <list-editor
     @create="create($event)"
     @update="update($event)"
     @delete="deleteIt($event)"
     :data="data"
     label="Name and Age"
     :definition= "[{
     name: 'name'
 }, {
     name: 'age'
 }]"
 ident="someUniqueThingorempty"
     >
  </list-editor>
  </q-page>
</template>

<script>
import { defineComponent, onMounted, ref } from 'vue';

import utils from 'jshelperutils'
import { uid } from 'quasar';
export default defineComponent({

setup(){
const data = ref([{name:"Erhard", age: "99", __uuid: "1"}])

const create=(payload) =>{
  const name = payload.buffer[0].value
  const age = payload.buffer[1].value
  data.value.push({name: name, age: age, __uuid: uid()})
  console.log(data.value)
}

const update=(payload) =>{
  const name = payload.buffer[0].value
  const age = payload.buffer[1].value
  const id = payload.id 
  const index = utils.getIndexOfObjectInArray(data.value, "__uuid",id)
  if (index > -1){
    data.value[index]["name"] = name
    data.value[index]["age"] = age
  }
}

const deleteIt = (payload) => {
  console.log("deleteIt")
  console.log(payload)
  const id = payload.id
  const index = utils.getIndexOfObjectInArray(data.value, "__uuid",id)
  if (index > -1){
    data.value.splice(index,1)
  }
}

  return {
    data,
    create,
    update,
    deleteIt
  
  }
}
})
</script>
