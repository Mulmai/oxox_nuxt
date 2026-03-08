<template>
<div class="h-screen flex justify-center items-center p-2 bgh">
    <v-card class="w-full md:w-1/2 rounded-xl elevation-8">
        <v-card-title>
          <v-icon @click="$router.go('-1')">mdi-arrow-left</v-icon>
            
            <h2 class="ml-2 text-xl font-bold text-green-600">ลงทะเบียนเข้าใช้งาน </h2>
        </v-card-title>
        <v-card-text>
            <div v-if="errorReg">
                <div v-if="errorReg.username">
                    <v-alert dense v-for="err,index in errorReg.username" :key="index" type="error">
                        {{err}}
                    </v-alert>
                </div>
                <div v-if="errorReg.email">
                    <v-alert dense v-for="err,index in errorReg.email" :key="index" type="error">
                        {{err}}
                    </v-alert>
                </div>
                <div v-if="errorReg.password">
                    <v-alert dense v-for="err,index in errorReg.password" :key="index" type="error">
                        {{err}}
                    </v-alert>
                </div>
                <div v-if="errorReg.password_confirm">
                    <v-alert dense v-for="err,index in errorReg.password_confirm" :key="index" type="error">
                        {{err}}
                    </v-alert>
                </div>
            </div>
            <form @submit.prevent="registerData()">
                <v-checkbox v-model="useTel" label="ใช้เบอร์โทรศัพท์ลงทะเบียน"></v-checkbox>
                <v-text-field type="number" required v-model="formReg.tel" filled rounded label="เบอร์โทรศัพท์" :rules="rules" counter maxlength="10" prepend-inner-icon="mdi-cellphone"></v-text-field>
                <v-text-field v-if="!useTel" required v-model="formReg.username" filled rounded label="ชื่อผู้ใช้" prepend-inner-icon="mdi-face"></v-text-field>
                <v-text-field type="password" required v-model="formReg.password" filled rounded label="รหัสผ่าน" prepend-inner-icon="mdi-form-textbox-password"></v-text-field>
                <v-text-field type="password" required v-model="formReg.password_confirm" filled rounded label="ยืนยันรหัสผ่าน" prepend-inner-icon="mdi-form-textbox-password"></v-text-field>
                <!-- <v-text-field required v-model="formReg.personal_id" type="number" filled rounded label="เลขบัตรประจำตัวประชาชน" :rules="rulesIDcard" counter maxlength="13" prepend-inner-icon="mdi-card-account-details-outline"></v-text-field> -->

                <v-btn v-if="formReg.password == formReg.password_confirm && formReg.password != ''" rounded large color="success" class="w-full" type="submit">สมัครสมาชิก</v-btn>
            </form>
        </v-card-text>
        <v-toolbar color="transparent" flat>
          <v-btn @click="$router.push('/auth/download')" color="indigo accent-3" dark depressed rounded>Download APK</v-btn>
          <v-spacer></v-spacer>
          <Button-Google />
        </v-toolbar>

    </v-card>

</div>
</template>

<script lang="ts">
import {
    Component,
    Vue,
    Watch,
} from "nuxt-property-decorator"
import { Auth } from '@/vuexes/auth'
import { Core } from '@/vuexes/core'
declare module 'vue/types/options' {
    interface ComponentOptions < V extends Vue > {
        layout ? : string
    }
}

@Component({

    layout: 'root',

})
export default class Root extends Vue {
    errorReg: any = null
    useTel: boolean = true
    formReg: any = {}
    
    rules:any = [
        (v:string) => !!v || 'กรอกเบอร์โทรศัพท์มือถือ 10 หลัก',
        (v:string) => ( v && v.length >= 10 ) || 'กรุณากรอกเบอร์โทรศัพท์มือถือ 10 หลัก',
        (v:string) => ( v && v.length <= 10 ) || 'เบอร์โทรศัพท์มือถือเกิน 10 หลัก',
    ]

    rulesIDcard:any = [
        (v:string) => !!v || 'กรอกเลขบัตรประชาชน 13 หลัก',
        (v:string) => ( v && v.length >= 13 ) || 'กรุณากรอกเลขบัตรประชาชน 13 หลัก',
        (v:string) => ( v && v.length <= 13 ) || 'เลขบัตรประชาชนเกิน 13 หลัก',
    ]

    async registerData() {
        if (this.useTel) {
            this.formReg.username = this.formReg.tel
        }
        let user = await Core.postHttp(`/accounts/register/`, this.formReg)
        if (user.id) {
            alert('สมัครสมาชิกสำเร็จแล้ว');
            await this.$router.replace('/auth/login')
        } else {
            alert('ไม่สามารถสมัครสมาชิกได้ กรุณาตรวจสอบข้อมูลการสมัครให้ถูกต้อง')
            this.errorReg = user
        }

    }

}
</script>

<style  >
.bgh{
  background: rgb(53,184,140);
  background: linear-gradient(180deg, rgba(53,184,140,1) 18%, rgba(17,140,87,1) 100%);
}
</style>
