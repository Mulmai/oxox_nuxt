<template>
    <div class="ox-detail-page">
        <v-toolbar flat color="transparent" class="detail-toolbar">
            <v-btn icon class="mr-2" @click="$router.go(-1)">
                <v-icon>mdi-arrow-left</v-icon>
            </v-btn>
            <div>
                <div class="detail-title">รายละเอียด</div>
                <div class="detail-subtitle">{{ form.name || '-' }}</div>
            </div>
            <v-spacer></v-spacer>
        </v-toolbar>

        <v-container class="pb-6">
            <v-card class="detail-card" elevation="6" rounded="xl">
                <v-card-text class="pa-2 pa-md-4">
                    <div v-if="isPageLoading" class="loading-box">
                        <v-progress-circular indeterminate color="primary" size="52" width="5" />
                        <div class="mt-3 loading-text">กำลังโหลดข้อมูลโค...</div>
                    </div>

                    <div v-else>
                        <v-tabs v-if="form.sex == 'โคขุน'" v-model="tab">
                            <v-tab><span class="em em-information_source tab-emoji" aria-hidden="true"></span>ทั่วไป</v-tab>
                            <v-tab-item>
                                <Ox-Edit v-if="tab == 0" />
                            </v-tab-item>
                            <v-tab><span class="em em-chart_with_upwards_trend tab-emoji" aria-hidden="true"></span>ประสิทธิภาพการผลิต</v-tab>
                            <v-tab-item>
                                <OxManager-TestOx v-if="tab == 1"></OxManager-TestOx>
                            </v-tab-item>
                            <v-tab><span class="em em-herb tab-emoji" aria-hidden="true"></span>ให้อาหาร</v-tab>
                            <v-tab-item>
                                <OxManager-Food v-if="tab == 2"></OxManager-Food>
                            </v-tab-item>
                            <v-tab><span class="em em-syringe tab-emoji" aria-hidden="true"></span>ทำวัคซีน</v-tab>
                            <v-tab-item>
                                <OxManager-Vaccine v-if="tab == 3"></OxManager-Vaccine>
                            </v-tab-item>
                            <v-tab><span class="em em-bug tab-emoji" aria-hidden="true"></span>ถ่ายพยาธิ</v-tab>
                            <v-tab-item>
                                <OxManager-Worm v-if="tab == 4"></OxManager-Worm>
                            </v-tab-item>
                            <v-tab><span class="em em-pill tab-emoji" aria-hidden="true"></span>โปรแกรมบำรุง</v-tab>
                            <v-tab-item>
                                <OxManager-Maintain v-if="tab == 5"></OxManager-Maintain>
                            </v-tab-item>
                            <v-tab><span class="em em-hospital tab-emoji" aria-hidden="true"></span>ประวัติการรักษา</v-tab>
                            <v-tab-item>
                                <OxManager-Heal v-if="tab == 6"></OxManager-Heal>
                            </v-tab-item>
                            <v-tab><span class="em em-moneybag tab-emoji" aria-hidden="true"></span>การจำหน่าย</v-tab>
                            <v-tab-item>
                                <OxManager-Sell v-if="tab == 7"></OxManager-Sell>
                            </v-tab-item>
                        </v-tabs>

                        <v-tabs v-if="form.sex != 'โคขุน'" v-model="tab">
                            <v-tab><span class="em em-information_source tab-emoji" aria-hidden="true"></span>ทั่วไป</v-tab>
                            <v-tab-item>
                                <Ox-Edit v-if="tab == 0" />
                            </v-tab-item>
                            <v-tab><span class="em em-syringe tab-emoji" aria-hidden="true"></span>ทำวัคซีน</v-tab>
                            <v-tab-item>
                                <OxManager-Vaccine v-if="tab == 1"></OxManager-Vaccine>
                            </v-tab-item>

                            <v-tab><span class="em em-bug tab-emoji" aria-hidden="true"></span>ถ่ายพยาธิ</v-tab>
                            <v-tab-item>
                                <OxManager-Worm v-if="tab == 2"></OxManager-Worm>
                            </v-tab-item>

                            <v-tab><span class="em em-pill tab-emoji" aria-hidden="true"></span>โปรแกรมบำรุง</v-tab>
                            <v-tab-item>
                                <OxManager-Maintain v-if="tab == 3"></OxManager-Maintain>
                            </v-tab-item>

                            <v-tab><span class="em em-hospital tab-emoji" aria-hidden="true"></span>ประวัติการรักษา</v-tab>
                            <v-tab-item>
                                <OxManager-Heal v-if="tab == 4"></OxManager-Heal>
                            </v-tab-item>

                            <v-tab><span class="em em-moneybag tab-emoji" aria-hidden="true"></span>การจำหน่าย</v-tab>
                            <v-tab-item>
                                <OxManager-Sell v-if="tab == 5"></OxManager-Sell>
                            </v-tab-item>

                            <v-tab v-if="form.sex != 'โคขุน' && form.sex != 'โคแรกเกิด'"><span class="em em-revolving_hearts tab-emoji" aria-hidden="true"></span>การผสมพันธุ์</v-tab>
                            <v-tab-item>
                                <OxManager-BreedMale v-if="form.sex == 'โคพ่อพันธุ์' && tab == 6"></OxManager-BreedMale>
                                <OxManager-BreedFemale v-if="form.sex == 'โคแม่พันธุ์' && tab == 6"></OxManager-BreedFemale>
                            </v-tab-item>
                        </v-tabs>
                    </div>
                </v-card-text>
            </v-card>
        </v-container>
    </div>
</template>

<script lang="ts">
import {
    Component,
    Vue,
    Watch
} from "nuxt-property-decorator";
import {
    Core
} from "@/vuexes/core";
import {
    Auth
} from "@/vuexes/auth";
import {
    Web
} from "@/vuexes/web";
@Component({
    components: {},
})
class Farm extends Vue {
    form: any = {};
    currentId: any = this.$route.params.id;
    tab: number = 0;
    isPageLoading: boolean = true;

    async getEnv() {
        this.currentId = this.$route.params.id;
        // this.form = await Core.getHttp(`/api/v1/ox/ox/${this.currentId }`)
    }

    async getOxen() {
        this.form = await Core.getHttp(`/api/v1/ox/ox/${this.currentId}/`);
    }

    async created() {
        this.isPageLoading = true;
        await Web.switchLoad(true);
        try {
            await this.getEnv();
            await this.getOxen();
        } finally {
            this.isPageLoading = false;
            await Web.switchLoad(false);
        }
    }

    get user() {
        return Auth.user;
    }
}

export default Farm
</script>

<style scoped>
@import url("https://emoji-css.afeld.me/emoji.css");

.ox-detail-page {
    min-height: 100vh;
    background: linear-gradient(180deg, #f1f7ff 0%, #f8fbff 45%, #ffffff 100%);
}

.detail-toolbar {
    padding: 8px 8px 0;
}

.detail-title {
    font-size: 0.95rem;
    color: #6b7280;
}

.detail-subtitle {
    font-size: 1.2rem;
    font-weight: 700;
    color: #ca8a04;
}

.detail-card {
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

.tab-emoji {
    margin-right: 6px;
}
</style>
