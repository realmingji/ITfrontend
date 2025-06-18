<template>
    <div class="nav">
        <div class="logo" @click="toMain"> <img :src="Logo" alt="LS Signature Logo" /> </div>
        <div class="menuZone">
      
                
            <div v-for="link in routerLinks" :key="link.path" class="ml-4">
            <p v-if="!link.children" :to="link.path" class="text-white font-weight-medium">
                {{ link.name }}
            </p>
      
                <v-menu v-else offset-y>
                    
                <template #activator="{ props }">
                    <v-btn v-bind="props" variant="text" class="text-white font-weight-medium">
                        {{ link.name }}
                    </v-btn>
                </template>
             
                    <v-list >
                    <v-list-item  v-for="child in link.children" :key="child.path" :to="`${link.path}/${child.path}`"
                        link router>
                        <v-list-item-title>{{ child.name }}</v-list-item-title>
                    </v-list-item>
                </v-list>
                
            
            </v-menu>
      

            </div>
           
        </div>
        <div class="zone">
            <v-icon color="#495057" >mdi-bell</v-icon>
            <v-icon color="#495057" >mdi-cog</v-icon>
            <v-icon color="#495057" @click="logout">mdi-logout</v-icon>
        </div>


    </div>

       
     
   
</template>

<script setup>
import { ref ,onMounted} from 'vue'
import { useRouter } from "vue-router";
import Logo from '@/assets/img/LS_THiRAUTECH_SIGNATURE.png'
import { useAuthStore } from '@/stores/user'

const router = useRouter();
const authStore = useAuthStore()

const routerLinks = ref([
    
    {
        path: '/common',
        name: '프로그램관리',
        children: [
            { path: 'commonCode', name: '공통코드' },

        ]
    },
    
    {
        path: '/test/demo',
        name: 'test',
        children: [
            { path: 'demo', name: '데모페이지' },

        ]
    },
    
    {
        path: '/education',
        name: '교육용',
        children: [
            { path: 'databinding', name: '뷰기초문법' },
            { path: 'emitProps', name: 'props/emit' },
            { path: 'Watch', name: 'watch/computed 차이' },


        ]
    },

     {
        path: '/lde',
        name: '프로젝트 관리',
        children: [
            { path: 'lde009', name: '프로젝트' },
            { path: 'lde010', name: '회사' },
            { path: 'lde011', name: '설문대상' },
            { path: 'lde012', name: '회원' },
        ]
    }



])

const toMain = () => {
    router.push({
        path: "/",
    });
}

const logout = async () => {
    await authStore.logout()
    router.push('/Login')
}




</script>

<style lang="scss">




</style>