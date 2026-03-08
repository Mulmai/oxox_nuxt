<template>
 <div style="background-color:transparent!important;"  >

   <v-toolbar class="mt-6" color="transparent" flat>
     <h2 class="text-xl p-4 font-bold" >เมนูหลัก</h2>
     <v-spacer></v-spacer>
 
     <v-chip @click="dialog =true"
       class="ma-2"
       color="cyan"
         rounded
       text-color="white"
     >
       <v-icon left>
         mdi-bell
       </v-icon>
       <b>{{noti.count}}</b>
     </v-chip>

   </v-toolbar>

    
 
   <v-divider class="ml-2 w-11/12"></v-divider>

    <h2 class="text-xl p-4 font-bold">โค</h2>
    <div class="flex flex-row flex-wrap justify-start">
        <div v-for="(ox,index) in listOx" :key="index" class="w-1/2">
          <div>
            <MenuOX :route="ox.route" :color="ox.color" :icon="ox.icon" :name="ox.name" :text="ox.text"></MenuOX>
          </div>
        </div>
    </div>

      <h2 class="text-xl p-4 font-bold">กระบือ</h2>
    <div class="flex flex-row flex-wrap justify-start">
        <div v-for="(buffalo,index) in listBuffalo" :key="index" class="w-1/2">
          <div>
            <MenuOX :route="buffalo.route" :color="buffalo.color" :icon="buffalo.icon" :name="buffalo.name" :text="buffalo.text"></MenuOX>
          </div>
        </div>
    </div>


    <Menu   v-for="menu,index in listPages" :key="index" :color="menu.color"
       :route="menu.route"   :name="menu.name" :text="menu.text" :icon="menu.icon"></Menu>
       <br> <br> <br>

      <v-dialog v-model="dialog" fullscreen persistent>
        <v-card>
           <Core-Noti @close="dialog=false" :noti="noti"></Core-Noti>
        </v-card>
      </v-dialog>

 </div>
</template>

<script lang="ts">
import {
  Component,
  Vue,
  Watch,
} from "nuxt-property-decorator"
import {Core} from '@/vuexes/core'
import {Auth} from '@/vuexes/auth'
import {Web} from "~/vuexes/web";
declare module 'vue/types/options' {
    interface ComponentOptions < V extends Vue > {
        layout ? : string
    }
}

@Component({
layout: 'home',
  components: { },
})
export default class MyComponent extends Vue {
  dialog:boolean = false
  text:string = 'hello world'
   listOx:any = [
     {
       name:'พ่อพันธุ์',
       text:'เพิ่ม แก้ไข รายการโคขุนในฟาร์ม',
       icon:'019-cow.png',
       route:'/ox/?type=โคพ่อพันธุ์',
       color:'border-yellow-500'
     },
     {
       name:'แม่พันธุ์',
       text:'เพิ่ม แก้ไข รายการโคขุนในฟาร์ม',
       icon:'019-cow.png',
       route:'/ox/?type=โคแม่พันธุ์',
       color:'border-pink-500'
     },
      {
       name:'แรกเกิด',
       text:'เพิ่ม แก้ไข รายการโคขุนในฟาร์ม',
       icon:'019-cow.png',
       route:'/ox/?type=โคแรกเกิด',
       color:'border-blue-500'
     },
     {
       name:'โคขุน',
       text:'เพิ่ม แก้ไข รายการโคขุนในฟาร์ม',
       icon:'019-cow.png',
       route:'/ox/?type=โคขุน',
       color:'border-green-500'
     },
   ];

    listBuffalo:any = [
     {
       name:'พ่อพันธุ์',
       text:'เพิ่ม แก้ไข รายการกระบือในฟาร์ม',
       icon:'019-buffalo.png',
       route:'/ox/?type=กระบือพ่อพันธุ์',
       color:'border-yellow-500'
     },
     {
       name:'แม่พันธุ์',
       text:'เพิ่ม แก้ไข รายการกระบือในฟาร์ม',
       icon:'019-buffalo.png',
       route:'/ox/?type=กระบือแม่พันธุ์',
       color:'border-pink-500'
     },
      {
       name:'แรกเกิด',
       text:'เพิ่ม แก้ไข รายการกระบือในฟาร์ม',
       icon:'019-buffalo.png',
       route:'/ox/?type=กระบือแรกเกิด',
       color:'border-blue-500'
     },
     {
       name:'กระบือรุ่น',
       text:'เพิ่ม แก้ไข รายการกระบือในฟาร์ม',
       icon:'019-buffalo.png',
       route:'/ox/?type=กระบือรุ่น',
       color:'border-red-500'
     },
      {
       name:'กระบือขุน',
       text:'เพิ่ม แก้ไข รายการกระบือในฟาร์ม',
       icon:'019-buffalo.png',
       route:'/ox/?type=กระบือขุน',
       color:'border-green-500'
     },
   ];

   listPages:any = [
     {
       name:'ฟาร์ม',
       text:'แก้ไขข้อมูล ชื่อ ที่ตั้ง',
       icon:'004-barn.png',
       route:'/farm/',
       color:'border-green-500'
     }, 
     {
       name:'การจำหน่าย',
       text:'นำโค/กระบือในฟาร์มจำหน่ายออก',
       icon:'040-chariot.png',
       route:'/sell/',
       color:'border-green-500'
     },
     {
       name:'องค์ความรู้',
       text:'คลังข้อมูลความรู้สำหรับการเลี้ยงโคขุน',
       icon:'044-smart.png',
       route:'/knowledge/',
       color:'border-green-500'
     },
     {
       name:'การเงิน',
       text:'จัดการข้อมูลรายรับรายจ่าย',
       icon:'wallet.png',
       route:'/money/',
       color:'border-green-500'
     },
     {
       name:'รายงาน',
       text:'ข้อมูลสรุปรายระเอียดต่างๆ',
       icon:'business-report.png',
       route:'/report/',
       color:'border-green-500'
     },
   ]
  user:any = {}
  noti:any = { count:0}
  async created() {
    this.user = await Auth.getUser();
    this.noti = await Core.getHttp(`/api/v1/notification/${this.user.id}/`)
  
  }


  convertDate(date:any){
    return Web.convertDate(date);
  }



}
</script>


<style>

    .xbgx{
background-color: #fcfcfc;
background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 1600 900'%3E%3Cdefs%3E%3ClinearGradient id='a' x1='0' x2='0' y1='1' y2='0'%3E%3Cstop offset='0' stop-color='%230FF'/%3E%3Cstop offset='1' stop-color='%23CF6'/%3E%3C/linearGradient%3E%3ClinearGradient id='b' x1='0' x2='0' y1='0' y2='1'%3E%3Cstop offset='0' stop-color='%23F00'/%3E%3Cstop offset='1' stop-color='%23FC0'/%3E%3C/linearGradient%3E%3C/defs%3E%3Cg fill='%23FFF' fill-opacity='0' stroke-miterlimit='10'%3E%3Cg stroke='url(%23a)' stroke-width='2'%3E%3Cpath transform='translate(0 0)' d='M1409 581 1450.35 511 1490 581z'/%3E%3Ccircle stroke-width='4' transform='rotate(0 800 450)' cx='500' cy='100' r='40'/%3E%3Cpath transform='translate(0 0)' d='M400.86 735.5h-83.73c0-23.12 18.74-41.87 41.87-41.87S400.86 712.38 400.86 735.5z'/%3E%3C/g%3E%3Cg stroke='url(%23b)' stroke-width='4'%3E%3Cpath transform='translate(0 0)' d='M149.8 345.2 118.4 389.8 149.8 434.4 181.2 389.8z'/%3E%3Crect stroke-width='8' transform='rotate(0 1089 759)' x='1039' y='709' width='100' height='100'/%3E%3Cpath transform='rotate(0 1400 132)' d='M1426.8 132.4 1405.7 168.8 1363.7 168.8 1342.7 132.4 1363.7 96 1405.7 96z'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E");
background-attachment: fixed;
background-size: cover;
    }
</style>
