<template>
    <div class="create-page">
        <v-toolbar flat color="transparent" class="create-toolbar">
            <v-btn icon class="mr-2" @click="$router.go(-1)">
                <v-icon>mdi-arrow-left</v-icon>
            </v-btn>
            <v-toolbar-title class="font-weight-bold">เพิ่มข้อมูลโค</v-toolbar-title>
        </v-toolbar>

        <v-container>
            <v-card class="create-card" elevation="6" rounded="xl">
                <v-card-text class="pa-4 pa-md-6">
                    <div v-if="isPageLoading" class="loading-box">
                        <v-progress-circular indeterminate color="primary" size="52" width="5" />
                        <div class="mt-3 loading-text">กำลังเตรียมข้อมูล...</div>
                    </div>

                    <form v-else @submit.prevent="saveData()">
                        <v-text-field required dense outlined class="p-2" label="ชื่อ" v-model="form.name">
                            <template v-slot:prepend-inner>
                                <span class="em em-cow2 form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>
                        <v-text-field dense outlined class="p-2" type="number" label="เบอร์หู" v-model="form.ear_number">
                            <template v-slot:prepend-inner>
                                <span class="em em-ear form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>
                        <v-text-field dense outlined class="p-2" type="number" label="หมายเลข NID" v-model="form.nid_number">
                            <template v-slot:prepend-inner>
                                <span class="em em-1234 form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>
                        <v-text-field dense outlined class="p-2" type="number" label="หมายเลขไมโครชิป" v-model="form.chip_number">
                            <template v-slot:prepend-inner>
                                <span class="em em-computer form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>
                        <v-text-field dense outlined class="p-2" label="ระดับสายเลือด" v-model="form.blood">
                            <template v-slot:prepend-inner>
                                <span class="em em-heavy_heart_exclamation_mark_ornament form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>

                        <v-select required dense outlined class="p-2" :items="choices.gene" item-text="name" item-value="id" label="พันธุ์" v-model="form.gene">
                            <template v-slot:prepend-inner>
                                <span class="em em-label form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-select>
                        <v-text-field v-if="form.gene == 6" dense outlined class="p-2" label="พันธุ์อื่นๆ" v-model="form.gene_ect">
                            <template v-slot:prepend-inner>
                                <span class="em em-label form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>

                        <v-select v-if="showGenderSelector" required dense outlined class="p-2" :items="genderChoicesForType" item-text="name" item-value="id" label="เพศ" v-model="form.gender">
                            <template v-slot:prepend-inner>
                                <span class="em em-female_sign form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-select>
                        <v-text-field v-if="showGenderSelector && isOtherGenderSelected" dense outlined class="p-2" label="เพศอื่นๆ" v-model="form.gender_ect">
                            <template v-slot:prepend-inner>
                                <span class="em em-male_sign form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>

                        <v-select dense outlined class="p-2" :items="choices.origin" item-text="name" item-value="id" label="แหล่งที่มา" v-model="form.origin">
                            <template v-slot:prepend-inner>
                                <span class="em em-round_pushpin form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-select>
                        <v-text-field v-if="form.origin == 5" dense outlined class="p-2" label="แหล่งที่มาอื่นๆ" v-model="form.origin_ect">
                            <template v-slot:prepend-inner>
                                <span class="em em-round_pushpin form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>

                        <v-select dense outlined class="p-2" :items="choices.tooth" item-text="name" item-value="id" label="ฟัน" v-model="form.tooth">
                            <template v-slot:prepend-inner>
                                <span class="em em-tooth form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-select>
                        <v-text-field dense outlined class="p-2" type="text" label="อายุจากการทำนายฟัน" v-model="form.age_predict">
                            <template v-slot:prepend-inner>
                                <span class="em em-stopwatch form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>

                        <v-text-field dense outlined class="p-2" type="date" label="วันเกิด" v-model="form.birth_date">
                            <template v-slot:prepend-inner>
                                <span class="em em-spiral_calendar_pad form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>
                        <v-text-field dense outlined class="p-2" type="number" label="อายุ" v-model="form.age_age">
                            <template v-slot:prepend-inner>
                                <span class="em em-hourglass_flowing_sand form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>
                        <v-text-field dense outlined class="p-2" type="number" label="เดือน" v-model="form.age_month">
                            <template v-slot:prepend-inner>
                                <span class="em em-spiral_note_pad form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>

                        <v-text-field v-if="$route.query.type == 'โคขุน'" dense outlined class="p-2" type="date" label="วันที่เข้าขุน" v-model="form.fatten_date">
                            <template v-slot:prepend-inner>
                                <span class="em em-star form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>

                        <div class="flex p-2 align-center">
                            <v-text-field dense outlined class="mr-2" type="number" label="รอบอก (เซนติเมตร)" v-model="form.breast">
                                <template v-slot:prepend-inner>
                                    <span class="em em-straight_ruler form-emoji" aria-hidden="true"></span>
                                </template>
                            </v-text-field>
                            <v-btn v-if="$route.query.type == 'โคขุน'" class="-mt-2" small rounded color="primary" @click="form.weight = form.breast*2.23">คำนวน</v-btn>
                        </div>

                        <v-text-field dense outlined class="p-2" type="number" label="ความสูง (เซนติเมตร)" v-model="form.height">
                            <template v-slot:prepend-inner>
                                <span class="em em-triangular_ruler form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>
                        <v-text-field dense outlined class="p-2" type="number" label="ความยาวลำตัว (เซนติเมตร)" v-model="form.long">
                            <template v-slot:prepend-inner>
                                <span class="em em-triangular_ruler form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>

                        <v-text-field v-if="$route.query.type == 'โคขุน'" dense outlined class="p-2" type="number" label="น้ำหนักเข้าขุน (กิโลกรัม)" v-model="form.weight">
                            <template v-slot:prepend-inner>
                                <span class="em em-weight_lifter form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>

                        <v-text-field dense outlined class="p-2" type="date" label="วัน/เดือน/ปีที่ซื้อ" v-model="form.buy_date">
                            <template v-slot:prepend-inner>
                                <span class="em em-money_with_wings form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>
                        <v-text-field dense outlined class="p-2" type="number" label="ราคา" v-model="form.price">
                            <template v-slot:prepend-inner>
                                <span class="em em-moneybag form-emoji" aria-hidden="true"></span>
                            </template>
                        </v-text-field>

                        <div class="flex p-2 align-center">
                            <v-select dense outlined class="mr-2" :items="choices.bsc" item-text="name" item-value="id" label="ประเมินคะแนนสภาพร่างกาย (BCS)" v-model="form.bsc">
                                <template v-slot:prepend-inner>
                                    <span class="em em-bar_chart form-emoji" aria-hidden="true"></span>
                                </template>
                            </v-select>
                            <v-dialog scrollable v-model="dialog" width="500">
                                <template v-slot:activator="{ on, attrs }">
                                    <v-btn fab small class="-mt-2" color="primary" dark v-bind="attrs" v-on="on">
                                        !
                                    </v-btn>
                                </template>

                                <v-card>
                                    <v-card-title class="text-xs grey lighten-2">
                                        ประเมินคะแนนสภาพร่างกาย (BCS)
                                    </v-card-title>

                                    <v-card-text>
                                        <center>
                                            <img src="~/static/1.png" class="w-full" />
                                            <img src="~/static/2.png" class="w-full h-full" />
                                        </center>
                                    </v-card-text>

                                    <v-divider></v-divider>

                                    <v-card-actions>
                                        <v-spacer></v-spacer>
                                        <v-btn color="error" text @click="dialog = false">
                                            ปิด
                                        </v-btn>
                                    </v-card-actions>
                                </v-card>
                            </v-dialog>
                        </div>

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
import {
    Core
} from '@/vuexes/core'
import {
    Auth
} from '@/vuexes/auth'
import moment from "moment";
import _ from 'lodash'
@Component({

    components: {},
})
class Farm extends Vue {

    oxen: any = null
    form: any = {
        sex: this.$route.query.type
    }
    group: any = null
    dialog: boolean = false;
    choices: any = {}
    isPageLoading: boolean = true
    isSaving: boolean = false

    toothVal: any = {}
    SEX: any = this.$route.query.type

    get oxType() {
        return String(this.$route.query.type || '')
    }

    get showGenderSelector() {
        return this.isNewbornType(this.oxType)
    }

    get genderChoicesForType() {
        const genders: any[] = Array.isArray(this.choices.gender) ? this.choices.gender : []
        if (!this.showGenderSelector) {
            return genders
        }
        return genders.filter((item: any) => this.isMaleGender(item) || this.isFemaleGender(item))
    }

    get isOtherGenderSelected() {
        const selected: any = _.find(this.choices.gender, { id: this.form.gender })
        return this.isOtherGender(selected)
    }

    isNewbornType(type: string) {
        return /แรกเกิด/.test(type)
    }

    isMaleParentType(type: string) {
        return /พ่อพันธุ์/.test(type)
    }

    isFemaleParentType(type: string) {
        return /แม่พันธุ์/.test(type)
    }

    isMaleGender(item: any) {
        const name = String(item?.name || '')
        return /เพศผู้|ผู้|male|ชาย/i.test(name)
    }

    isFemaleGender(item: any) {
        const name = String(item?.name || '')
        return /เพศเมีย|เมีย|female|หญิง/i.test(name)
    }

    isOtherGender(item: any) {
        const name = String(item?.name || '')
        return /อื่น/.test(name)
    }

    applyGenderByType() {
        const genders: any[] = Array.isArray(this.choices.gender) ? this.choices.gender : []
        if (!genders.length) {
            return
        }

        if (this.isMaleParentType(this.oxType)) {
            const male: any = _.find(genders, (item: any) => this.isMaleGender(item))
            if (male) {
                this.form.gender = male.id
                this.form.gender_ect = ''
            }
            return
        }

        if (this.isFemaleParentType(this.oxType)) {
            const female: any = _.find(genders, (item: any) => this.isFemaleGender(item))
            if (female) {
                this.form.gender = female.id
                this.form.gender_ect = ''
            }
            return
        }

        if (this.showGenderSelector) {
            const validIds = this.genderChoicesForType.map((item: any) => item.id)
            if (this.form.gender && !validIds.includes(this.form.gender)) {
                this.form.gender = null
                this.form.gender_ect = ''
            }
            return
        }

        this.form.gender_ect = ''
    }

    async getEnv() {

        this.choices = {
            gene: await Core.getHttp('/api/v1/tool/gene/'),
            gender: await Core.getHttp('/api/v1/tool/gender/'),
            tooth: await Core.getHttp('/api/v1/tool/tooth/'),
            origin: await Core.getHttp('/api/v1/tool/origin/'),
            bsc: await Core.getHttp('/api/v1/tool/bsc/'),
        }
    }

    async getOxen() {
        this.oxen = await Core.getHttp(`/api/v1/ox/ox/`)
    }

    async created() {
        this.isPageLoading = true
        try {
            await this.getEnv();
            this.applyGenderByType();
            await this.getOxen();
        } finally {
            this.isPageLoading = false
        }
    }

    @Watch('form.birth_date')
    async onChangeDate(val: string) {
        this.form.age = moment().diff(val, 'years', false);
    }

    @Watch('form.tooth')
    async onChangeAge(val: string) {
        let tooth: any = _.find(this.choices.tooth, {
            id: val
        })
        this.form.age_predict = tooth.age_val
    }

    get user() {
        return Auth.user;
    }

    async saveData() {
        if (this.isSaving) {
            return
        }

        this.isSaving = true
        this.form.status = "อยู่ในฟาร์ม";
        this.form.user = this.user.id
        try {
            let ox = await Core.postHttp(`/api/v1/ox/ox/`, this.form)
            if (ox.id) {
                alert('บันทึกข้อมูลสำเร็จ')
                await this.$router.go(-1)
            }
        } finally {
            this.isSaving = false
        }
    }

}

export default Farm
</script>

<style scoped>
@import url("https://emoji-css.afeld.me/emoji.css");

.create-page {
    min-height: 100vh;
    background: linear-gradient(180deg, #f1f7ff 0%, #f9fcff 40%, #ffffff 100%);
}

.create-toolbar {
    padding: 8px 8px 0;
}

.create-card {
    border: 1px solid #dbeafe;
}

.loading-box {
    min-height: 55vh;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
}

.loading-text {
    color: #1d4ed8;
    font-weight: 600;
}

.form-emoji {
    margin-right: 4px;
}
</style>
