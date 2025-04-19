<!-- vuejs has only 3 part
 the SCRIPT, the TEMPLATE, the STYLE -->
<script setup>
// import HelloWorld from './components/HelloWorld.vue'
import {ref, onMounted} from 'vue'
import PocketBase from 'pocketbase';
const pb = new PocketBase('http://127.0.0.1:8090');
const todoInput= ref("")

const hi = "my name is"
const todos = ref([])
const name = ref("name")
const number = ref(0);
const newTodo= ref("")
let userInput = ref("")
let capitalCities = ref(["England,","Kuala lampur","Paris","Abuja"])
const records = ref([])
let solatTimeLink = "https://www.e-solat.gov.my/index.php?r=esolatApi/takwimsolat&period=today&zone=JHR01"
let prayerTime = ref({})
function getPrayertime(){
  fetch (solatTimeLink).then(result => result.json()).then(data=>{
    prayerTime.value = data.prayerTime[0]
    console.log(data.prayerTime[0])
  }
  )
}

async function getTodo (){
  records.value= await pb.collection('todos').getFullList()
  console.log(todos);
}

// function createTodo(){
//   const newTodo = {
//     item: todoInput.value
//     isDone: false
//   }
//   pb.collection('todos').create(newTodo).then(res =>{
//     records.value.push(res)
//     todoInput.value =""
//   })
// }

onMounted(async ()=>{
  await getTodo();
})

function increment() {
  number.value++;
  console.log(number)
}
function startInterval(){
  setInterval(increment, 1000)
}

function AddToList(){
 if(userInput.value.trim() == ""){
    return;
 }
    capitalCities.value.push(userInput.value);
  userInput.value = ""
  
}
</script>

<template>
  <div class="container mx-auto p-6 max-w-3xl bg-gradient-to-br from-blue-50 to-purple-50 rounded-lg shadow-lg">
    <!-- Counter Section -->
    <div class="mb-8 flex items-center justify-center flex-col">
      <h1 class="text-6xl font-bold text-indigo-600 mb-4">{{ number }}</h1>
      <button 
        @click="startInterval" 
        class="px-6 py-2 bg-indigo-600 text-white font-medium rounded-full shadow-md hover:bg-indigo-700 transition-colors duration-300"
      >
        Increment
      </button>
    </div>
    
    <!-- Divider -->
    <div class="border-b border-gray-200 mb-8"></div>
    
    <!-- City Input Section -->
    <div class="mb-8 flex flex-col sm:flex-row items-center gap-4">
      <input 
        type="text" 
        v-model="userInput"
        placeholder="Enter a city name..."
        class="flex-grow px-4 py-2 border-2 border-indigo-200 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-400 focus:border-transparent"
      >
      <button 
        @click="AddToList"
        class="px-6 py-2 bg-emerald-500 text-white font-medium rounded-lg shadow-md hover:bg-emerald-600 transition-colors duration-300 w-full sm:w-auto"
      >
        Add City
      </button>
    </div>
    
    <!-- Name Input Section -->
    <div class="mb-8">
      <input 
        type="text" 
        v-model="name"
        v-on:keyup="AddToList"
        placeholder="Enter your name..."
        class="w-full px-4 py-2 border-2 border-indigo-200 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-400 focus:border-transparent mb-4"
      >
      <div class="bg-white p-4 rounded-lg shadow-md">
        <h1 class="text-lg font-medium text-gray-600">My name is:</h1>
        <h2 class="text-2xl font-bold text-indigo-600">{{ name || 'Anonymous' }}</h2>
      </div>
    </div>
    
    <!-- City List Section -->
    <div class="mb-8">
      <div class="bg-white p-6 rounded-lg shadow-md">
        <h2 class="text-xl font-bold text-indigo-600 mb-4">Capital Cities</h2>
        <ol class="list-decimal pl-8 space-y-2">
          <li 
            v-for="(city, index) in capitalCities" 
            :key="index"
            class="text-gray-700 font-medium"
          >
            {{ city }}
          </li>
          <li v-if="!capitalCities || capitalCities.length === 0" class="text-gray-400 italic">No cities added yet</li>
        </ol>
      </div>
    </div>
    
    <!-- Toggle Section -->
    <div class="mb-8 flex flex-col items-center">
      <h1 
        v-if="isTrue" 
        class="text-2xl font-bold text-amber-500 mb-4 animate__animated animate__fadeIn"
      >
        England is my city
      </h1>
      <button 
        @click="isTrue = !isTrue"
        class="px-6 py-2 bg-amber-500 text-white font-medium rounded-lg shadow-md hover:bg-amber-600 transition-colors duration-300"
      >
        Toggle Text
      </button>
    </div>
    
    <!-- Prayer Times Section -->
    <div class="mb-4">
      <button 
        @click="getPrayertime"
        class="w-full px-6 py-3 bg-purple-600 text-white font-medium rounded-lg shadow-md hover:bg-purple-700 transition-colors duration-300 mb-6"
      >
        Get Prayer Times
      </button>
      
      <div class="bg-white rounded-lg overflow-hidden shadow-lg">
        <div class="bg-purple-100 py-3 px-4">
          <h2 class="text-xl font-bold text-purple-800 text-center">Prayer Times</h2>
        </div>
        <div class="p-4">
          <table class="w-full border-collapse">
            <thead>
              <tr class="bg-purple-50">
                <th class="py-3 px-2 text-purple-800 font-medium">Fajr</th>
                <th class="py-3 px-2 text-purple-800 font-medium">Duha</th>
                <th class="py-3 px-2 text-purple-800 font-medium">Dhuhr</th>
                <th class="py-3 px-2 text-purple-800 font-medium">Asr</th>
                <th class="py-3 px-2 text-purple-800 font-medium">Maghrib</th>
                <th class="py-3 px-2 text-purple-800 font-medium">Isha</th>
              </tr>
            </thead>
            <tbody>
              <tr class="text-center border-t border-purple-100">
                <td class="py-4 px-2 text-gray-700">{{ prayerTime.fajr || '--:--' }}</td>
                <td class="py-4 px-2 text-gray-700">{{ prayerTime.dhuha || '--:--' }}</td>
                <td class="py-4 px-2 text-gray-700">{{ prayerTime.dhuhr || '--:--' }}</td>
                <td class="py-4 px-2 text-gray-700">{{ prayerTime.asr || '--:--' }}</td>
                <td class="py-4 px-2 text-gray-700">{{ prayerTime.maghrib || '--:--' }}</td>
                <td class="py-4 px-2 text-gray-700">{{ prayerTime.isha || '--:--' }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>


    
    <footer class="text-center text-gray-500 text-sm mt-8">
      Made with ❤️ using Vue and Tailwind CSS
    </footer>
  </div>
</template>

<script>
export default {
  data() {
    return {
      number: 0,
      userInput: '',
      name: '',
      capitalCities: [],
      isTrue: false,
      prayerTime: {
        fajr: '',
        dhuha: '',
        dhuhr: '',
        asr: '',
        maghrib: '',
        isha: ''
      }
    }
  },
  methods: {
    startInterval() {
      this.number += 1;
    },
    AddToList() {
      if (this.userInput && this.userInput.trim() !== '') {
        this.capitalCities.push(this.userInput);
        this.userInput = '';
      }
    },
    getPrayertime() {
      // Simulating API response
      this.prayerTime = {
        fajr: '05:30',
        dhuha: '07:15',
        dhuhr: '12:30',
        asr: '15:45',
        maghrib: '18:15',
        isha: '20:00'
      };
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap');

body {
  font-family: 'Poppins', sans-serif;
  background-color: #f3f4f6;
}

/* Optional: Add animations */
.animate__animated {
  animation-duration: 1s;
}

.animate__fadeIn {
  animation-name: fadeIn;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>