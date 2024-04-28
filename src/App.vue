<script setup>
import HelloWorld from "./components/HelloWorld.vue";
import { ref, reactive, computed, onMounted, watch } from 'vue';
import axios from "axios";
import Alert from '../src/Alert.vue';
import { CogIcon } from "@vue-hero-icons/outline"

const showAlert = ref(false);

let id = ref('');
let name = ref('');
let email = ref('');
let username = ref('');
let password = ref('');
let confirmpassword = ref('');

let getStatus = ref('');

const addUser = async () => {
  // let result = await axios.post("http://localhost:8000/api/usersbooking", {
  try {
    let response = await axios.post("http://localhost:3000/user", {
      // id: id.value,
      name: name.value,
      email: email.value,
      // username: username.value,
      password: password.value,
    });
    notificationTimeout()
    console.warn(response.status)
    if (response.status === 201) {
      getStatus.value = 'success';
    }
  } catch (error) {
    console.log('server error', error)
    getStatus.value = 'danger';
    notificationTimeout()
  }
}

const testPassword = computed(() => {
  return password.value.length >= 6 && confirmpassword.value === password.value;
})

const notificationTimeout = () => {
  showAlert.value = true;
  setTimeout(() => {
    showAlert.value = false
  }, 3000)
}

const counter = reactive({ count: 0 })
// const searchTexe = ref('')

const increase = () => {
  counter.count = counter.count + 1
}

const isCheck = ref(false)

const toggleCheck = () => {
  isCheck.value = !isCheck.value
}

const items = ref(['item1', 'item2', 'item3'])

const addNewItem = () => {
  const itemLength = (items.value.length + 1)
  items.value.push('item' + itemLength)
}

const message = ref('')

const isLoading = ref(false)
const isValid = ref(true)

const firstname = ref('')
const lastname = ref('')
const email_ = ref('')
const errors = ref({})

const fullname = computed(() => {
  return `${firstname.value} ${lastname.value}`
})

const updateProfile = async () => {
  isLoading.value = true
  await (new Promise(resolve => setTimeout(resolve, 2000)))
  isLoading.value = false
  getStatus.value = 'success'
  notificationTimeout()
}

const validateName = (name) => {
  const re = /\d/
  return !re.test(name)
}

const validateEmail = (email) => {
  return email.includes('@')
}

watch([firstname, lastname, email_], () => {
  isValid.value = true
  getStatus.value = 'danger'
  errors.value = {}

  if (!validateName(firstname.value)) {
    isValid.value = false
    errors.value.firstname = 'ชื่อจริงไม่ถูกต้อง'
  }
  if (!validateName(lastname.value)) {
    isValid.value = false
    errors.value.lastname = 'นามสกุลไม่ถูกต้อง'
  }
  if (!validateEmail(email_.value)) {
    isValid.value = false
    errors.value.email_ = 'อีเมลล์ไม่ถูกต้อง'
  }
})

onMounted(() => {
  firstname.value = 'ชื่อ'
  lastname.value = 'นามสกุล'
  email_.value = 'test@mail.com'
})

</script>

<template>
  <div>

    <div>
      <ul>
        <li v-for="item in items">
          {{ item }}
        </li>
      </ul>
      <button @click="addNewItem()"
        class="inline-flex items-center px-3 py-2 text-sm font-medium text-center bg-blue-800 shadow-lg shadow-gray-500 rounded-lg hover:bg-blue-900 focus:outline-none">Add
        new item</button>
    </div>

    <div v-if="isCheck">
      แสดงข้อความออกมา เมื่อ check เป็น True
    </div>
    <div v-else="isCheck">
      แสดงข้อความออกมา เมื่อ check เป็น False
    </div>
    <button @click="toggleCheck()"
      class="inline-flex items-center px-3 py-2 text-sm font-medium text-center bg-blue-800 shadow-lg shadow-gray-500 rounded-lg hover:bg-blue-900 focus:outline-none">กดเพื่อเปิด</button>

    <!-- <div>{{ searchTexe }}</div>
  <input v-model="searchTexe"> -->

    <div class="pb-5">
      Counter {{ counter.count }}
      <button @click="increase()"
        class="inline-flex items-center px-3 py-2 text-sm font-medium text-center bg-blue-800 shadow-lg shadow-gray-500 rounded-lg hover:bg-blue-900 focus:outline-none">
        Add couter
      </button>
    </div>

    <div>
      <input v-model="message">
      <HelloWorld :messageText="message"></HelloWorld>
    </div>

    <div class="space-y-5 p-5 absolute right-5 -top-5 w-2/6 pt-10 fixed">
      <Alert :intent="getStatus" :show="showAlert" :on-dismiss="() => (showAlert = false)" title="Notify!">
        <div v-if="getStatus === 'success'">
          <p>Successfully</p>
        </div>
        <div v-else>
          <p>Error!</p>
        </div>
      </Alert>
      <!-- <div class="w-full">
      <img class="object-right-top object-none h-36 w-full bg-purple-800" src="http://pixelprowess.com/i/powship.svg" alt="POW Ship">
    </div> -->
    </div>
    <div class="App w-full bg-[#E7EDEF] dark:bg-[#202020] flex justify-center items-center relative">
      <div class="mt-34 my-30 w-11/12">
        <div
          class="relative text-md mb-10 w-2/4 p-7 rounded-md bg-[#202020]/[0.80] mx-auto flex justify-center shadow-md shadow-red-500">
          <div class='w-10/12'>
            <div
              class="flex justify-center items-center p-5 bg-[#101010]/[.60] w-auto py-2 rounded-sm my-30 mx-auto text-center text-white absolute right-5 -top-5 shadow-md shadow-red-500">
              <h1>Post Method API</h1>
            </div>
            <div>Fullname: {{ fullname }}</div>
            <div>Email: {{ email_ }}</div>
            <div class="pb-2">
              <div class="flex items-center text-white">
                <span class="text-white text-sm">Firstname</span>
              </div>
              <input class="w-full" type="text" v-model="firstname" placeholder="Firstname">
              <div class="mt-1 error">{{ errors.firstname }}</div>
            </div>
            <div class="text-back pb-2">
              <div class="flex items-center text-white">
                <span class="text-white text-sm">Lastname</span>
              </div>
              <input class="w-full" type="text" v-model="lastname" placeholder="Lastname">
              <div class="mt-1 error">{{ errors.lastname }}</div>
            </div>
            <div class="pt-2 pb-2 dark:text-white shadow-gray-500">
              <div class="flex items-center text-white">
                <span class="text-white text-sm">Email</span>
              </div>
              <input class="w-full" type="text" v-model="email_" placeholder="Email">
              <div class="mt-1 error">{{ errors.email_ }}</div>
            </div>

            <div v-if="isLoading" class="py-5 loading text-black absolute">
              <!-- Loading ... -->
              <!-- <CogIcon class="h-6 w-6 text-blue-500" /> -->
              <!-- <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-10 h-10 animate-spin">
                <path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12a7.5 7.5 0 0 0 15 0m-15 0a7.5 7.5 0 1 1 15 0m-15 0H3m16.5 0H21m-1.5 0H12m-8.457 3.077 1.41-.513m14.095-5.13 1.41-.513M5.106 17.785l1.15-.964m11.49-9.642 1.149-.964M7.501 19.795l.75-1.3m7.5-12.99.75-1.3m-6.063 16.658.26-1.477m2.605-14.772.26-1.477m0 17.726-.26-1.477M10.698 4.614l-.26-1.477M16.5 19.794l-.75-1.299M7.5 4.205 12 12m6.894 5.785-1.149-.964M6.256 7.178l-1.15-.964m15.352 8.864-1.41-.513M4.954 9.435l-1.41-.514M12.002 12l-3.75 6.495" />
              </svg> -->
              <img width="228" height="201" src="https://movie2free.tv/wp-content/uploads/2024/01/ZJFD.gif"
                alt="Loading..." class="perfmatters-lazy entered pmloaded"
                data-src="https://movie2free.tv/wp-content/uploads/2024/01/ZJFD.gif" data-ll-status="loaded">
            </div>

            <button :disabled="!isValid" @click="updateProfile()" :class="{ 'hover:bg-blue-800': isValid }"
              class="mt-3 inline-flex items-center px-3 py-2 text-sm font-medium text-center bg-blue-900 shadow-lg shadow-gray-500 rounded-lg focus:outline-none">
              Update profile
            </button>
            <br><br>
          </div>
        </div>
      </div>
    </div>
    <div class="App w-full bg-[#E7EDEF] dark:bg-[#202020] flex justify-center items-center">
      <div class="mt-34 my-30 w-11/12 py-36">
        <div
          class="relative text-md mb-10 w-2/4 p-7 rounded-md bg-[#202020]/[0.80] mx-auto flex justify-center shadow-md shadow-red-500">
          <div class='w-10/12'>
            <div
              class="flex justify-center items-center p-5 bg-[#101010]/[.60] w-auto py-2 rounded-sm my-30 mx-auto text-center text-white absolute right-5 -top-5 shadow-md shadow-red-500">
              <h1>Post Method API</h1>
            </div>
            <div class="pb-5">
              <div class="flex items-center text-white">
                <span class="text-white text-sm">Name</span>
              </div>
              <input class="w-11/12" type="text" name="name" v-model="name" placeholder="Name">
            </div>
            <div class="text-back pb-5">
              <div class="flex items-center text-white">
                <span class="text-white text-sm">Email</span>
              </div>
              <input class="w-11/12" type="text" name="email" v-model="email" placeholder="Email">
            </div>
            <div class="pt-2 pb-5 dark:text-white shadow-gray-500">
              <div class="flex items-center text-white">
                <span class="text-white text-sm">Password</span>
              </div>
              <input class="w-11/12" type="password" name="password" v-model="password" placeholder="Password">
            </div>
            <button v-on:click="addUser()"
              class="inline-flex items-center px-3 py-2 text-sm font-medium text-center bg-blue-800 shadow-lg shadow-gray-500 rounded-lg hover:bg-blue-900 focus:outline-none">
              Add New User
            </button>
            <br><br>
          </div>
        </div>
      </div>
    </div>
    <div>
      <br><br>
      <div>
        <label for="">Password</label>
        <input v-model="password" type="text" />
      </div>
      <br>
      <div>
        <label for="">Confirmpassword</label>
        <input v-model="confirmpassword" type="text" />
      </div>
    </div>
    <button :disabled="!testPassword"
      class="inline-flex items-center px-3 py-2 text-sm font-medium text-center bg-blue-800 shadow-lg shadow-gray-500 rounded-lg hover:bg-blue-900 focus:outline-none">
      Save
    </button>
    {{ password }}
    {{ confirmpassword }}
    {{ testPassword }}
  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}

.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}

.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}

.loading {
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgba(30, 29, 29, 0.875);
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
}

.button {
  width: 100%;
  height: 24px;
  margin-top: 20px;
}

.error {
  color: red;
}
</style>
