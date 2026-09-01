<script setup>
import { ref, computed } from 'vue'

const newItem = ref('')
const items= ref([])
const wordCount = computed(()=> newItem.value.length)

const addItem= ()=>{
  items.value.push(
    {
      id:items.value.length +1,
      label: newItem.value,
      purchased:false,
      highPriority:itemHighPriority.value
    })
    newItem.value = ""
    itemHighPriority.value = ""
}
const togglePurchased =(item)=>{
  item.purchased = !item.purchased
}

const selectedItem = ref(null)

const selectItem = (item)=>{

  if (selectedItem.value === item.id) {

    selectedItem.value = null    
  } else {
    selectedItem.value = item.id
  }

  //selectedItem.value = item.id
}

const togglePurchasedAndDeselect =(item)=>{
  togglePurchased(item)
  selectedItem.value = null
}

const itemHighPriority = ref(false)

const deleteItem =(index)=>{
  items.value.splice(index,1)
  selectedItem.value = null
}

const keyboardCommands = (event, item, index) =>{

  if (event.key === ""){
    event.preventDefault()
    togglePurchased(item)
  }

  if ( event.key === 'Backspace' || event.key === 'Delete') {
    deleteItem(index)
  }
}
</script>

<template>
  <form 
      @submit.prevent="addItem"
      class="add-item-form"
  >
    <h1>My Shopping List App</h1>

    <input 
      class="item-input"
      type="text" 
      v-model.trim="newItem" 
      placeholder="Add Item here"
      maxlength="100"      
    >

    <label for="high-priority" class="high-priority-box">
    <input type="checkbox" v-model="itemHighPriority" class="high-priority-checkbox">
    High Priority
    </label>

    <p>{{ wordCount }} /100</p>

    <button 
    class="btn"
    :disabled="newItem.length < 3"
    >      
      Add Item to List      
    </button>

  </form>  

  <div class="list">
    <ul>
      <li
          v-for="(item,index) in items"          
          :key = "item.id"
          tabindex="0"
          @click="selectItem(item)"
          @keydown.space ="togglePurchasedAndDeselect(item)"
          @keydown.delete="deleteItem(index)"
          @keydown.backspace ="deleteItem(index)"
          :class="{
            strikeout:item.purchased,
            priority:item.highPriority,
            selected: selectedItem === item.id
          }"          
              
      >
          {{ item.label }}

      </li>
  
    </ul>

    <p v-if="!items.length"> No Items in List</p>

  </div>

  
</template>

