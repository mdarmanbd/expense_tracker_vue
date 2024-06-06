<script setup>
import { ref, reactive, watch, computed } from 'vue';
import { toast } from 'vue3-toastify';
import 'vue3-toastify/dist/index.css';


const addItemParse = JSON.parse(localStorage.getItem("addItem")) || []
const addItem = reactive(addItemParse);
const itemName = ref('')
const itemAmount = ref()
const balance = ref(100)

// toast("Track Your Many!", {
//     "theme": "dark",
//   "type": "warning",
//   "autoClose": 1000,
//   "dangerouslyHTMLString": true
// })


const showToster = (message, type='info') => {
    toast(message,{
        type: type,
        autoClose: 1000
    })
}


const submitBtn = () => {
   if(itemName.value === '' || itemAmount.value === ''){
    console.error(`empty field`)
   }else{
        addItem.push({
            id: addItem.length + 1 ,
            name : itemName.value,
            amount : itemAmount.value
        }) 
        localStorage.setItem("addItem", JSON.stringify(addItem))
        itemName.value = ''
        itemAmount.value = ''
        
    }
    
    

    // toast.success('Success to add item', {
    //     autoClose: 1000,
    // })
    
}


const positiveNumber = computed (()=>{
    
    let total = 0
    for(let item of addItem){
        if(item.amount > 0){
            total = total + item.amount
        }
    }
    return total
})

const negativeNumber = computed(()=>{
    let total = 0 
    for(let item of addItem){
        if(item.amount<0){
            total+=item.amount
        } 
    }
    return total
})

watch (negativeNumber, ()=>{
    
        showToster('Negative value', 'error')
    
})

const totalBalnce = computed(() => {
   return balance.value + positiveNumber.value + negativeNumber.value
})

const removeItem = (item) => {
    let i = addItem.indexOf(item)
    const remove = addItem.splice(i, 1)
    localStorage.setItem("addItem", JSON.stringify(addItem))
    showToster('Remove item', 'error')
    // toast.error('Remove item', {
    //     autoClose: 1000,
    // })

    return remove
}

// watch (addItem, (value) => {
//     localStorage.setItem('additem', JSON.stringify(value))
// })


</script>

<template>
    <!-- <p>{{ addItem }}</p> -->

    <div class="w-full h-screen bg-gray-100 pt-2">
        <div class="w-[500px] m-auto bg-white py-3 px-5">
            <h4 class="text-base font-medium text-gray-800 ">Expense Tracker</h4>
            <h6 class="text-sm font-normal text-gray-800 mt-2">YOUR BALANCE</h6>
            <h3 class="text-base font-medium text-gray-800 ">$ {{ totalBalnce }}</h3>
            <div class="w-full grid grid-cols-2 gap-3 mt-3 ">
                <div class="text-center shadow py-3 border rounded">
                    <p class="capitalize text-base font-normal text-gray-800">income</p>
                    <span class="text-green-500 text-base font-medium block pt-1">$ {{ positiveNumber }}</span>
                </div>
                <div class="text-center shadow py-3 border rounded">
                    <p class="capitalize text-base font-normal text-gray-800">expense</p>
                    <span class="text-red-500 text-base font-medium block pt-1">$ {{ negativeNumber }} </span>
                </div>
            </div>
            <p class="mt-5 text-gray-800 text-sm font-medium border-b pb-1">History</p>
            <div class="h-[110px] overflow-y-auto">
                <div v-for="(item, index) in addItem" :key="index">
                    <div class="mt-2 ml-5 relative group mb-1 flex items-center py-1 px-3 bg-gray-100 border-r-4 " :class="item.amount > 0 ? 'bg-green-200 border-r-green-500' : 'bg-red-200 border-r-red-500'">
                        <img @click="removeItem(item)" class="absolute z-0 invisible group-hover:visible group-hover:z-10 hover:bg-red-100 bg-red-50 -left-5 top-0 py-1 cursor-pointer" src="../svg/cross.svg">
                        <p class="text-sm font-medium text-gray-800 capitalize">{{ item.name }}</p> 
                        <h6 class="text-sm font-medium text-gray-800 ml-auto"> $ {{ item.amount }} </h6>
                        
                    </div>
                </div>
            </div>
            <p class="text-gray-800 font-normal text-sm pb-1 border-b mt-3 pl-1">
                Add new transation
            </p>
            <form @submit.prevent ="submitBtn">
                <label class="block text-sm text-gray-700 font-medium mt-4 pb-1">
                    Text
                </label>
                <input v-model="itemName" required type="text" class="bg-gray-100 text-sm text-gray-800 w-full rounded focus:outline-none py-1 px-3">
                <label class="block text-sm text-gray-700 font-medium mt-4 pb-1">
                    Amout 
                    <br>
                    (negative - expense, positive - income)
                </label>
                <input v-model="itemAmount" required type="number" class="bg-gray-100 text-sm text-gray-800 w-full rounded focus:outline-none py-1 px-3">
                <button class=" w-full text-white text-sm font-normal text-center py-2 rounded bg-blue-600 mt-5" type="submit">
                    Add transation
                </button>
            </form>
        </div>
    </div>
</template>


<style scoped>

</style>
