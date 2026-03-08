<template>
    <div class="ox-edit-page">
       <div>
           <div v-if="isPageLoading" class="loading-box">
                <v-progress-circular indeterminate color="primary" size="50" width="5" />
                <div class="mt-3 loading-text">กำลังโหลดข้อมูล...</div>
            </div>

            <form class="edit-form mt-6" @submit.prevent="saveData()" v-if="response && !isPageLoading">
                <div class="image-box mb-3">
                     <img class="w-full h-[90px]" :src="profileImage" alt="ox-profile" />
                    <div class="mt-2 d-flex align-center justify-center">
                        <v-btn small rounded color="primary" :loading="isImageLoading" :disabled="isImageLoading" @click="openImagePicker()">
                            เปลี่ยนรูป
                        </v-btn>
                        <v-btn v-if="form.image" small text color="error" class="ml-2" :disabled="isImageLoading" @click="removeImage()">
                            ลบรูป
                        </v-btn>
                    </div>
                    <input ref="imageInput" type="file" accept="image/*" class="d-none" @change="onChangeImage" />
                </div>

                <v-text-field dense class="p-2" outlined label="ชื่อ" v-model="form.name">
                    <template v-slot:prepend-inner>
                        <span class="em em-cow2 form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-text-field dense class="p-2" outlined type="number" label="เบอร์หู" v-model="form.ear_number">
                    <template v-slot:prepend-inner>
                        <span class="em em-ear form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-text-field dense class="p-2" outlined type="number" label="หมายเลข NID" v-model="form.nid_number">
                    <template v-slot:prepend-inner>
                        <span class="em em-1234 form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-text-field dense class="p-2" outlined type="number" label="หมายเลขไมโครชิป" v-model="form.chip_number">
                    <template v-slot:prepend-inner>
                        <span class="em em-computer form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-text-field dense class="p-2" outlined label="ระดับสายเลือด" v-model="form.blood">
                    <template v-slot:prepend-inner>
                        <span class="em em-heavy_heart_exclamation_mark_ornament form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>

                <v-select dense class="p-2" outlined :items="choices.gene" item-text="name" item-value="id" label="พันธุ์" v-model="form.gene">
                    <template v-slot:prepend-inner>
                        <span class="em em-label form-emoji" aria-hidden="true"></span>
                    </template>
                </v-select>
                <v-text-field dense class="p-2" outlined v-if="form.gene == (isWeb.fillData( choices.gene ,'type',true )).id " name="name" label="พันธุ์อื่นๆ" id="id">
                    <template v-slot:prepend-inner>
                        <span class="em em-label form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-select dense class="p-2" outlined :items="choices.gender" item-text="name" item-value="id" label="เพศ" v-model="form.gender">
                    <template v-slot:prepend-inner>
                        <span class="em em-female_sign form-emoji" aria-hidden="true"></span>
                    </template>
                </v-select>
                <v-text-field dense class="p-2" outlined v-if="form.gender == (isWeb.fillData( choices.gender ,'name','อื่นๆ' )).id " name="name" label="เพศอื่นๆ" id="id">
                    <template v-slot:prepend-inner>
                        <span class="em em-male_sign form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>

                <v-select dense class="p-2" outlined :items="choices.origin" item-text="name" item-value="id" label="แหล่งที่มา" v-model="form.origin">
                    <template v-slot:prepend-inner>
                        <span class="em em-round_pushpin form-emoji" aria-hidden="true"></span>
                    </template>
                </v-select>
                <v-text-field dense class="p-2" outlined v-if="form.origin == (isWeb.fillData( choices.origin ,'type',true )).id " label="แหล่งที่มาอื่นๆ">
                    <template v-slot:prepend-inner>
                        <span class="em em-round_pushpin form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-select dense class="p-2" outlined :items="choices.tooth" item-text="name" item-value="id" label="ฟัน" v-model="form.tooth">
                    <template v-slot:prepend-inner>
                        <span class="em em-tooth form-emoji" aria-hidden="true"></span>
                    </template>
                </v-select>
                <v-text-field dense class="p-2" outlined type="text" label="อายุจากการทำนายฟัน" v-model="form.age_predict">
                    <template v-slot:prepend-inner>
                        <span class="em em-stopwatch form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>

                <v-text-field dense class="p-2" outlined type="date" label="วันเกิด" v-model="form.birth_date">
                    <template v-slot:prepend-inner>
                        <span class="em em-spiral_calendar_pad form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-text-field dense class="p-2" outlined type="number" label="อายุ" v-model="form.age_age">
                    <template v-slot:prepend-inner>
                        <span class="em em-hourglass_flowing_sand form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-text-field dense class="p-2" outlined type="number" label="เดือน" v-model="form.age_month">
                    <template v-slot:prepend-inner>
                        <span class="em em-spiral_note_pad form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>

                <v-text-field dense class="p-2" outlined type="number" step="0.01" label="อายุปัจจุบัน (เดือน)" v-model="form.current_age">
                    <template v-slot:prepend-inner>
                        <span class="em em-hourglass form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-text-field dense class="p-2" outlined type="number" step="0.01" label="น้ำหนักปัจจุบัน (กิโลกรัม)" v-model="form.current_weight">
                    <template v-slot:prepend-inner>
                        <span class="em em-scales form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>

                <v-text-field dense class="p-2" outlined type="number" step="0.01" label="อายุเริ่มต้น (เดือน)" v-model="form.start_age">
                    <template v-slot:prepend-inner>
                        <span class="em em-hourglass_flowing_sand form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-text-field dense class="p-2" outlined type="number" step="0.01" label="น้ำหนักเริ่มต้น (กิโลกรัม)" v-model="form.start_weight">
                    <template v-slot:prepend-inner>
                        <span class="em em-scales form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>

                <v-text-field dense class="p-2" outlined type="number" step="0.01" label="อายุหย่านม (เดือน)" v-model="form.weaning_age">
                    <template v-slot:prepend-inner>
                        <span class="em em-baby_bottle form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-text-field dense class="p-2" outlined type="number" step="0.01" label="น้ำหนักหย่านม (กิโลกรัม)" v-model="form.weaning_weight">
                    <template v-slot:prepend-inner>
                        <span class="em em-scales form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>

                <v-text-field dense class="p-2" outlined type="number" label="ระยะห่างการคลอด (วัน)" v-model="form.calving_interval">
                    <template v-slot:prepend-inner>
                        <span class="em em-calendar form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>

                <v-select dense class="p-2" outlined :items="breedStatusOptions" label="ผสม / ไม่ผสม" v-model="form.breed_status">
                    <template v-slot:prepend-inner>
                        <span class="em em-couple form-emoji" aria-hidden="true"></span>
                    </template>
                </v-select>
                <v-select dense class="p-2" outlined :items="pregnancyStatusOptions" label="ท้อง / ไม่ท้อง" v-model="form.pregnancy_status">
                    <template v-slot:prepend-inner>
                        <span class="em em-pregnant_woman form-emoji" aria-hidden="true"></span>
                    </template>
                </v-select>

                <v-text-field dense class="p-2" outlined type="number" step="0.1" label="ประเมินคะแนน Rumen fill score (RFS)" v-model="form.rfs_score">
                    <template v-slot:prepend-inner>
                        <span class="em em-bar_chart form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-text-field v-if="form.sex == 'โคขุน'" dense class="p-2" outlined type="date" label="วันที่เข้าขุน" v-model="form.fatten_date">
                    <template v-slot:prepend-inner>
                        <span class="em em-star form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>

                <div class="flex p-2 align-center">
                    <v-text-field dense class="mr-2" outlined type="number" label="รอบอก (เซนติเมตร)" v-model="form.breast">
                        <template v-slot:prepend-inner>
                            <span class="em em-straight_ruler form-emoji" aria-hidden="true"></span>
                        </template>
                    </v-text-field>
                    <v-btn v-if="form.sex == 'โคขุน'" class="-mt-2" small rounded @click="form.weight = form.breast*2.23" color="primary">คำนวน</v-btn>
                </div>

                <v-text-field dense class="p-2" outlined type="number" label="ความสูง (เซนติเมตร)" v-model="form.height">
                    <template v-slot:prepend-inner>
                        <span class="em em-triangular_ruler form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-text-field dense class="p-2" outlined type="number" label="ความยาวลำตัว (เซนติเมตร)" v-model="form.long">
                    <template v-slot:prepend-inner>
                        <span class="em em-triangular_ruler form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>

                <v-text-field v-if="form.sex == 'โคขุน'" dense class="p-2" outlined type="number" label="น้ำหนักเข้าขุน (กิโลกรัม)" v-model="form.weight">
                    <template v-slot:prepend-inner>
                        <span class="em em-scales form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>

                <v-text-field dense class="p-2" outlined type="date" label="วัน/เดือน/ปีที่ซื้อ" v-model="form.buy_date">
                    <template v-slot:prepend-inner>
                        <span class="em em-money_with_wings form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <v-text-field dense class="p-2" outlined type="number" label="ราคา" v-model="form.price">
                    <template v-slot:prepend-inner>
                        <span class="em em-moneybag form-emoji" aria-hidden="true"></span>
                    </template>
                </v-text-field>
                <div class="flex p-2 align-center">
                    <v-select dense class="mr-2" outlined :items="choices.bsc" item-text="name" item-value="id" label="ประเมินคะแนนสภาพร่างกาย (BCS)" v-model="form.bsc">
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

                <v-btn class="w-full" large type='submit' rounded block color='success' :loading="isSaving" :disabled="isSaving || isDeleting || isImageLoading">บันทึก</v-btn>

                <v-btn text rounded color="red" dark class="mt-4 w-full" :loading="isDeleting" :disabled="isDeleting || isSaving" @click="deleteData()">ลบโคขุนนี้</v-btn>
            </form>
       </div>
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
import { Web } from '@/vuexes/web'
import moment from "moment";
import _ from 'lodash'
@Component({

    components: {},
})
class Farm extends Vue {

    currentId: any = this.$route.params.id;
    form: any = {}
    choices: any = {}
    dialog: boolean = false;
    isWeb: any = Web
    response: boolean = false;
    isPageLoading: boolean = true;
    isSaving: boolean = false;
    isDeleting: boolean = false;
    isImageLoading: boolean = false;
    breedStatusOptions: any[] = ['ผสม', 'ไม่ผสม']
    pregnancyStatusOptions: any[] = ['ท้อง', 'ไม่ท้อง']

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
        this.form = await Core.getHttp(`/api/v1/ox/ox/${this.currentId}/`)
    }

    async created() {
        this.isPageLoading = true;
        try {
            await this.getEnv();
            await this.getOxen();
            this.response = true;
        } finally {
            this.isPageLoading = false;
        }

    }

    @Watch('form.birth_date')
    async onChangeDate(val: string) {
        this.form.age = moment().diff(val, 'years', false);
    }

    @Watch('form.tooth')
    async onChangeAge(val: string) {
        let tooth:any = _.find(this.choices.tooth,{id:val}) 
        this.form.age_predict = tooth.age_val
    }

    get user() {
        return Auth.user;
    }

    get profileImage() {
        return this.form?.image || 'https://img2.pic.in.th/Gemini_Generated_Image_d6k88kd6k88kd6k8-1.png'
    }

    openImagePicker() {
        const input: any = this.$refs.imageInput
        if (input) {
            input.click()
        }
    }

    async onChangeImage(event: Event) {
        const target = event.target as HTMLInputElement
        const file = target.files && target.files[0]
        if (!file) {
            return
        }

        this.isImageLoading = true
        try {
            const base64 = await Core.getBase64(file)
            this.form.image = await this.resizeBase64Image(base64 as string, 900, 0.82)
        } finally {
            this.isImageLoading = false
            target.value = ''
        }
    }

    resizeBase64Image(base64: string, maxSize: number = 900, quality: number = 0.82): Promise<string> {
        return new Promise((resolve, reject) => {
            const img = new Image()
            img.onload = () => {
                let width = img.width
                let height = img.height

                if (width > height && width > maxSize) {
                    height = Math.round((height * maxSize) / width)
                    width = maxSize
                } else if (height >= width && height > maxSize) {
                    width = Math.round((width * maxSize) / height)
                    height = maxSize
                }

                const canvas = document.createElement('canvas')
                canvas.width = width
                canvas.height = height
                const ctx = canvas.getContext('2d')

                if (!ctx) {
                    resolve(base64)
                    return
                }

                ctx.drawImage(img, 0, 0, width, height)
                const output = canvas.toDataURL('image/jpeg', quality)
                resolve(output)
            }
            img.onerror = () => reject(new Error('image resize failed'))
            img.src = base64
        })
    }

    removeImage() {
        this.form.image = null
    }

    async saveData() {
        if (this.isSaving || this.isDeleting) {
            return
        }

        this.isSaving = true
        this.form.user = this.user.id 
        try {
            let ox = await Core.putHttp(`/api/v1/ox/ox/${this.currentId}/`, this.form)
            if (ox.id) {
                alert('บันทึกข้อมูลสำเร็จ')
                await this.getEnv();
                await this.getOxen();
            }
        } finally {
            this.isSaving = false
        }
    }

    async deleteData() {
        if (this.isDeleting || this.isSaving) {
            return
        }
        let check = confirm('คุณแน่ใจใช่ไหม')
        if (check) {
            this.isDeleting = true
            try {
                alert('ลบข้อมูลแล้ว');
                await Core.deleteHttp(`/api/v1/ox/ox/${this.form.id}/`)
                await this.$router.go(-1)
            } finally {
                this.isDeleting = false
            }
        }
    }

}

export default Farm
</script>

<style scoped>
@import url("https://emoji-css.afeld.me/emoji.css");

.ox-edit-page {
    min-height: 100%;
    background: linear-gradient(180deg, #f4f8ff 0%, #fbfdff 42%, #ffffff 100%);
}

.edit-form {
    border: 1px solid #dbeafe;
    border-radius: 14px;
    padding: 10px 6px 16px;
    background: rgba(255, 255, 255, 0.92);
}

.loading-box {
    min-height: 45vh;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
}

.loading-text {
    color: #1d4ed8;
    font-weight: 600;
}

.image-box {
    text-align: center;
}

.image-avatar {
    border: 3px solid #dbeafe;
}

.form-emoji {
    margin-right: 4px;
}
</style>
