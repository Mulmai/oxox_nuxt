<template>
    <div class="farm-page">
        <v-toolbar flat color="transparent" class="farm-toolbar">
            <v-btn icon class="mr-2" @click="$router.go(-1)">
                <v-icon>mdi-arrow-left</v-icon>
            </v-btn>
            <v-toolbar-title class="font-weight-bold">ข้อมูลฟาร์ม</v-toolbar-title>
        </v-toolbar>

        <v-container grid-list-xs>
            <v-card class="farm-card" elevation="6" rounded="xl">
                <v-card-text class="pa-4 pa-md-6">
                    <div v-if="isPageLoading" class="loading-box">
                        <v-progress-circular color="success" indeterminate size="52" width="5" />
                        <div class="mt-3 loading-text">กำลังโหลดข้อมูล...</div>
                    </div>

                    <form v-else @submit.prevent="saveData()">
                        <v-text-field required dense class="p-2" disabled outlined label="ชื่อผู้ใช้" v-model="form.username" prepend-inner-icon="mdi-face" />
                        <v-text-field required dense class="p-2" outlined label="ชื่อ" v-model="form.first_name" prepend-inner-icon="mdi-card-account-details-outline" />
                        <v-text-field required dense class="p-2" outlined label="สกุล" v-model="form.last_name" prepend-inner-icon="mdi-card-account-details-outline" />

                        <v-select required dense class="p-2" outlined :items="['ชาย','หญิง']" label="เพศ" v-model="form.gender" prepend-inner-icon="mdi-gender-male-female" />

                        <!-- <v-text-field required dense class="p-2" type="number" label="เลขบัตรประชาชน" v-model="form.personal_id" :rules="rulesIDcard" counter maxlength="13" prepend-inner-icon="mdi-card-account-details-star-outline" /> -->
                        <v-text-field required dense class="p-2" outlined type="number" label="เบอร์โทรศัพท์" v-model="form.tel" :rules="rules" counter maxlength="10" prepend-inner-icon="mdi-cellphone" />
                        <v-text-field required dense class="p-2" outlined label="ชื่อฟาร์ม" v-model="form.name_farm" prepend-inner-icon="mdi-barn" />
                        <v-text-field required dense class="p-2" outlined label="ที่อยู่" v-model="form.address" prepend-inner-icon="mdi-home" />
                        <v-text-field required dense class="p-2" outlined label="หมู่บ้าน" v-model="form.swine" prepend-inner-icon="mdi-home-group" />

                        <v-text-field required dense class="p-2" outlined label="พิกัด" v-model="form.location" prepend-inner-icon="mdi-google-maps" />
                        <v-text-field required dense class="p-2" outlined :readonly="(CityFrom)?true:false" v-model="CityFrom" @click="openCityDialog" label="จังหวัด อำเภอ ตำบล" prepend-inner-icon="mdi-map-search-outline"></v-text-field>
                        <v-text-field required dense class="p-2" outlined type="number" label="รหัสไปรษณีย์" v-model="form.zipcode" prepend-inner-icon="mdi-post-outline" />

                        <v-select required dense class="p-2" outlined :items="groups" item-text="name" item-value="id" label="กลุ่ม" v-model="form.farm_group" prepend-inner-icon="mdi-account-group" />
                        <v-text-field v-if="form.farm_group == 199 " required dense class="p-2" outlined type="text" label="ระบุชื่อวิสาหกิจชุมชนกลุ่มอื่น ๆ" v-model="form.farm_group_other" prepend-inner-icon="mdi-account-group" />

                        <v-btn type='submit' rounded block large color='success' :loading="isSaving" :disabled="isSaving">
                            บันทึก
                        </v-btn>
                    </form>
                </v-card-text>
            </v-card>
        </v-container>
    </div>
</template>

<script lang="ts">
import {
    Component,
    Vue,
    Watch,
} from "nuxt-property-decorator"
import { Core } from '@/vuexes/core'
import { Auth } from '@/vuexes/auth'
import { City } from "@/vuexes/city";
@Component({

    components: {},
})
class Farm extends Vue {

    form: any = null
    groups: any = null
    response: boolean = false;
    isPageLoading: boolean = true;
    isSaving: boolean = false;

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

    async getEnv() {
        this.form = await Auth.getUser();
        await this.setCity();
        this.groups = await Core.getHttp(`/api/v1/tool/group/`)
    }

    async created() {
        this.isPageLoading = true;
        await this.getEnv();
        this.response = true;
        this.isPageLoading = false;
    }

    get user() {
        return Auth.user;
    }

    async saveData() {
        if (this.isSaving) {
            return
        }

        this.isSaving = true;
        try {
            this.form.geo = City.currentGeo ?.id
            this.form.province = City.currentProvince ?.id
            this.form.amphur = City.currentAmphur ?.id
            this.form.district = City.currentDistrict ?.id
            let user = await Core.putHttp(`/api/v1/farmer/userprofile/${this.form.id}/`, this.form)
            if (user.id) {
                await this.getEnv();
                alert('บันทึกข้อมูลสำเร็จ')
            }
        } finally {
            this.isSaving = false;
        }
    }

    async setCity() {
        City.currentGeo = await Core.getHttp(`/api/v1/location/geography/${this.form.geo}/`)
        City.currentProvince = await Core.getHttp(`/api/v1/location/province/${this.form.province}/`)
        City.currentAmphur = await Core.getHttp(`/api/v1/location/amphur/${this.form.amphur}/`)
        City.currentDistrict = await Core.getHttp(`/api/v1/location/district/${this.form.district}/`)
        await City.setShowName()
    }
    async openCityDialog() {
        City.dialogCityState = true
    }
    get CityFrom() {
        return City.showName
    }

}

export default Farm
</script>

<style scoped>
.farm-page {
    min-height: 100vh;
    background: linear-gradient(180deg, #eefaf4 0%, #f8fffb 35%, #ffffff 100%);
}

.farm-toolbar {
    padding: 8px 8px 0;
}

.farm-card {
    border: 1px solid #d9f2e5;
    backdrop-filter: blur(2px);
}

.loading-box {
    min-height: 45vh;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
}

.loading-text {
    color: #2e7d32;
    font-weight: 600;
}
</style>
