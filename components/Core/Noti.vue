<template>
  <div class="noti-wrap">
    <v-card-title class="noti-title">
      <div class="d-flex align-center">
        <v-icon left color="primary">mdi-bell-ring-outline</v-icon>
        <span class="font-weight-bold">การแจ้งเตือน</span>
      </div>
      <v-spacer></v-spacer>
      <v-btn color="error" rounded dark fab x-small @click="$emit('close')">
        <v-icon small>mdi-close</v-icon>
      </v-btn>
    </v-card-title>

    <v-card-text class="pt-2">
      <v-tabs v-model="tab" background-color="transparent" color="primary" class="noti-tabs" show-arrows>
        <v-tab><span class="em em-syringe tab-emoji" aria-hidden="true"></span>วัคซีน</v-tab>
        <v-tab><span class="em em-bug tab-emoji" aria-hidden="true"></span>พยาธิ</v-tab>
        <v-tab><span class="em em-zap tab-emoji" aria-hidden="true"></span>เหนี่ยวนำ</v-tab>
        <v-tab><span class="em em-hospital tab-emoji" aria-hidden="true"></span>รักษา</v-tab>
      </v-tabs>

      <v-tabs-items v-model="tab" class="transparent-tab-items">
        <v-tab-item>
          <div class="section-head">ข้อมูลการทำวัคซีน</div>
          <div v-if="vaccines.length">
            <div v-for="(vaccine,index) in vaccines" :key="`v-${index}`">
              <Core-Menu :name="convertDate(vaccine.date_notificate)" icon="syringe.png" :text="`วันที่ทำ ` + convertDate(vaccine.date)"></Core-Menu>
            </div>
          </div>
          <div v-else class="empty-box">
            <v-icon class="empty-icon" color="grey">mdi-database-off-outline</v-icon>
            <div>ไม่มีข้อมูล</div>
          </div>
        </v-tab-item>

        <v-tab-item>
          <div class="section-head">ข้อมูลการถ่ายพยาธิ</div>
          <div v-if="worms.length">
            <div v-for="(worm,index) in worms" :key="`w-${index}`">
              <Core-Menu :name="convertDate(worm.date_notificate)" icon="parasite.png" :text="`วันที่ทำ ` + convertDate(worm.date)"></Core-Menu>
            </div>
          </div>
          <div v-else class="empty-box">
            <v-icon class="empty-icon" color="grey">mdi-database-off-outline</v-icon>
            <div>ไม่มีข้อมูล</div>
          </div>
        </v-tab-item>

        <v-tab-item>
          <div class="section-head">ข้อมูลการเหนี่ยวนำ</div>
          <div v-if="inductions.length">
            <div v-for="(item,index) in inductions" :key="`i-${index}`">
              <Core-Menu :name="convertDate(item.date_notificate)" icon="parasite.png" :text="`วันที่ทำ ` + convertDate(item.date)"></Core-Menu>
            </div>
          </div>
          <div v-else class="empty-box">
            <v-icon class="empty-icon" color="grey">mdi-database-off-outline</v-icon>
            <div>ไม่มีข้อมูล</div>
          </div>
        </v-tab-item>

        <v-tab-item>
          <div class="section-head">ข้อมูลการรักษา</div>
          <div v-if="heals.length">
            <div v-for="(item,index) in heals" :key="`h-${index}`">
              <Core-Menu :name="convertDate(item.date_notificate)" icon="parasite.png" :text="`วันที่ทำ ` + convertDate(item.date)"></Core-Menu>
            </div>
          </div>
          <div v-else class="empty-box">
            <v-icon class="empty-icon" color="grey">mdi-database-off-outline</v-icon>
            <div>ไม่มีข้อมูล</div>
          </div>
        </v-tab-item>
      </v-tabs-items>
    </v-card-text>
    </div>
</template>
<script>
import { Web } from '@/vuexes/web'

export default {
  props: {
    noti: { default: () => ({}) }
  },
  data() {
    return {
      tab: 0,
    }
  },
  computed: {
    vaccines() {
      return this.noti?.vaccines || []
    },
    worms() {
      return this.noti?.worms || []
    },
    inductions() {
      return this.noti?.inductions || []
    },
    heals() {
      return this.noti?.heals || []
    },
  },
  methods: {
    convertDate(date) {
      if (!date) {
        return '-'
      }
      return Web.convertDate(date)
    }
    }
}
</script>
<style scoped>
@import url("https://emoji-css.afeld.me/emoji.css");

.noti-wrap {
  background: linear-gradient(180deg, #f8fbff 0%, #ffffff 100%);
}

.noti-title {
  border-bottom: 1px solid #e5eefc;
}

.noti-tabs {
  border-bottom: 1px solid #eef2ff;
}

.transparent-tab-items {
  background: transparent;
}

.section-head {
  font-weight: 700;
  color: #334155;
  margin: 12px 0 8px;
}

.empty-box {
  text-align: center;
  color: #94a3b8;
  padding: 20px 0;
}

.empty-icon {
  display: block;
  margin-bottom: 6px;
}

.tab-emoji {
  margin-right: 6px;
}
</style>