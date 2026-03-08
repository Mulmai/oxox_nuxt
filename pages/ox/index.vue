<template>
    <div class="ox-page">
        <v-toolbar flat color="transparent" class="ox-toolbar">
            <v-btn icon class="mr-2" @click="$router.go(-1)">
                <v-icon>mdi-arrow-left</v-icon>
            </v-btn>
            <h2 class="page-title">{{$route.query.type}}</h2>
            <v-spacer></v-spacer>
            <v-btn @click="$router.push(`/ox/create/?type=${$route.query.type}`)" rounded color="primary" elevation="2">สร้าง</v-btn>
        </v-toolbar>

        <v-container class="pb-6 pt-2">
            <div v-if="isLoading" class="loading-box">
                <v-progress-circular indeterminate color="primary" size="48" width="5" />
                <div class="mt-3 loading-text">กำลังโหลดรายการ...</div>
            </div>

            <div v-else-if="!oxen || oxen.length === 0" class="empty-box">
                <v-icon size="44" color="grey">mdi-cow-off</v-icon>
                <div class="mt-2 empty-text">ยังไม่มีข้อมูลในหมวดนี้</div>
            </div>

            <div v-else>
                <div v-for="ox,index in oxen" :key="index" class="mb-2">
                    <Menu :route="`/ox/${ox.id}/`" icon="017-cow.png" :name="ox.name" :text="convertDate(ox.created_at)"></Menu>
                </div>
            </div>
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
import { Web } from '@/vuexes/web'
@Component({

    components: {},
})
class Farm extends Vue {

    oxen: any = null
    isLoading: boolean = true

    async getOxen() {
        this.isLoading = true
        try {
            let user = await Auth.getUser()
            this.oxen = await Core.getHttp(`/api/v1/ox/ox/?sex=${this.$route.query.type}&status=อยู่ในฟาร์ม&user=${user.id}`)
        } finally {
            this.isLoading = false
        }
    }

    async created() {
        await this.getOxen();
    }

    get user() {
        return Auth.user;
    }

    convertDate(date:any){
        return Web.convertDate(date);
    }

}

export default Farm
</script>

<style scoped>
.ox-page {
    min-height: 100vh;
    background: linear-gradient(180deg, #f1f7ff 0%, #f8fbff 45%, #ffffff 100%);
}

.ox-toolbar {
    padding: 8px 8px 0;
}

.page-title {
    margin: 0;
    font-size: 1.15rem;
    font-weight: 700;
    color: #1e40af;
}

.loading-box,
.empty-box {
    min-height: 40vh;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
}

.loading-text {
    color: #1d4ed8;
    font-weight: 600;
}

.empty-text {
    color: #6b7280;
}
</style>
