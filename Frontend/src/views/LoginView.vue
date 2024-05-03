<template>
    <div class="pt-16">
        <h1 class="text-3xl font-semibold mb-4">Enter your phone number</h1>
        <form v-if="!waitingOnVerification" action="#" @submit.prevent="handleLogin">
            <div class="overflow-hidden shadow sm:rounded-md max-w-sm mx-auto text-left">
                <div class="bg-white px-4 py-5 sm:p-6">
                    <div>
                        <input type="text" v-model="credentials.phone" v-maska data-maska="# (###) ###-####" name="phone" id="phone" placeholder="1 (234) 567-8910"
                            class="mt-1 block w-full px-3 py-2 rounded-md border border-gray-300 shadow-sm focus:border-black focus:outline-none">
                    </div>
                </div>
                <div class="bg-gray-50 px-4 py-3 text-right sm:px-6">
                    <button type="submit" @submit.prevent="handleLogin"
                        class="inline-flex justify-center rounded-md border border-transparent bg-black py-2 px-4 text-sm font-medium text-white shadow-sm hover:bg-gray-600 focus:outline-none">Continue</button>
                </div>
            </div>
        </form>
        <form v-else action="#" @submit.prevent="handleVerification">
            <div class="overflow-hidden shadow sm:rounded-md max-w-sm mx-auto text-left">
                <div class="bg-white px-4 py-5 sm:p-6">
                    <div>
                        <input type="text" v-model="credentials.login_code" v-maska data-maska="######" name="login_code" id="login_code" placeholder="123456"
                            class="mt-1 block w-full px-3 py-2 rounded-md border border-gray-300 shadow-sm focus:border-black focus:outline-none">
                    </div>
                </div>
                <div class="bg-gray-50 px-4 py-3 text-right sm:px-6">
                    <button type="submit" @submit.prevent="handleVerification"
                        class="inline-flex justify-center rounded-md border border-transparent bg-black py-2 px-4 text-sm font-medium text-white shadow-sm hover:bg-gray-600 focus:outline-none">Verify</button>
                </div>
            </div>
        </form>
    </div>
</template>

<script setup>
import { useRouter } from 'vue-router'
import { vMaska } from "maska"
import { onMounted, reactive, ref } from "vue";
import axios from "axios";

const router = useRouter()
const waitingOnVerification = ref(false)
const credentials = reactive({
    phone: null,
    login_code: null
})

// Xử lý trong trạng thái mount
// Tự điều hướng đến landing nếu đã tồn tại token
onMounted(() => {
    if (localStorage.getItem('token')) {
        router.push({
            name: 'landing'
        })
    }
})

// Object chứa data đã được format lại
const getFormattedCredentials = () => {
    return {
        phone: credentials.phone.replaceAll(' ', '').replace('(','').replace(')','').replace('-',''),
        login_code: credentials.login_code
    }
}

// Xử lý login với data đã được format
const handleLogin = () => {
    axios.post('http://127.0.0.1:8000/api/login', getFormattedCredentials())
    .then((response) => {
        console.log(response.data)
        waitingOnVerification.value = true
    })
    .catch((error) => {
        console.log(error)
        alert(error.response.data.message)
    })
}

// Xử lý verify với data đã được format
const handleVerification = () => {
    axios.post('http://127.0.0.1:8000/api/login/verify', getFormattedCredentials())
    .then((response) => {
        console.log(response.data)
        localStorage.setItem('token', response.data)
        router.push({
            name: 'landing'
        })
    })
    .catch((error) => {
        console.log(error)
        alert(error.response.data.message)
    })
}
</script>

<style lang="scss" scoped>

</style>