<template>
  <div class="bgh">
    <div class="h-screen flex justify-center items-center p-2">
      <v-card class="w-full md:w-1/2 rounded-xl elevation-8">
        <v-card-title>
          <p class="text-xl font-bold text-green-600">เข้าสู่ระบบ<br> <span class="text-base font-thin">ระบบจัดการโคขุน</span></p>
          <br>
        </v-card-title>
        <v-card-text>
          <form @submit.prevent="login()">
            <v-text-field required v-model="form.username" filled label="ชื่อผู้ใช้ หรือ เบอร์โทรศัพท์" rounded prepend-inner-icon="mdi-account"></v-text-field>
            <v-text-field required v-model="form.password" type="password" filled label="รหัสผ่าน" rounded prepend-inner-icon="mdi-form-textbox-password"></v-text-field>
            <v-btn depressed large color="success" class="w-full" type="submit" rounded>เข้าสู่ระบบ</v-btn>
            <br><br>
            <v-btn text @click="$router.push('/auth/register')" large rounded color="primary" class="w-full font-bold">สมัครสมาชิก</v-btn>
          </form>
        </v-card-text>
        <!-- <v-toolbar color="transparent" flat v-if="$vuetify.breakpoint.mobile != true">
          <v-btn @click="$router.push('/auth/download')" color="indigo accent-3" dark depressed rounded>Download APK</v-btn>
          <v-spacer></v-spacer>
          <Button-Google />
        </v-toolbar> -->
      </v-card>
    </div>
  </div>
</template>

<script  >
import {
  Component,
  Vue,
  Watch,
} from "nuxt-property-decorator"
import {Core} from '@/vuexes/core'
import {Auth} from '@/vuexes/auth'

export default {
  layout: 'root',
  data: () => ({
    form:{}
  }),
  async created(){
    await this.checkUser();
  },
  methods: {
    async login(){
      await Auth.login(this.form);
      let user = await Auth.getUser(); 
      if(user.id){
        //await location.reload();
        await this.$router.replace('/')
        await location.reload();
      }
    },
    async checkUser(){
      let user = await Auth.getUser(); 
      if(user.id){
        await this.$router.replace('/')
      }
    }
  }
}


</script>


<style>
.bgh{
  background: rgb(53,184,140);
  background: linear-gradient(180deg, rgba(53,184,140,1) 18%, rgba(17,140,87,1) 100%);
}

.bg{
  background-image: url("https://images.pexels.com/photos/5867858/pexels-photo-5867858.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=650&w=940");
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}

</style>
