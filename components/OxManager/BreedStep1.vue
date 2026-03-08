<template>
  <div>
    <h1>step1</h1>
    <!-- <pre>{{ breed }}</pre> -->
    <v-select
      :items="['เหนี่ยวนำ', 'ไม่เหนี่ยวนำ']"
      item-text="name"
      item-value="id"
      label="การเหนี่ยวนำการเป็นสัด"
      v-model="form.is_induction"
    >
      <template v-slot:prepend-inner>
        <span class="em em-link form-emoji" aria-hidden="true"></span>
      </template>
    </v-select>
    <v-text-field
      v-if="form.is_induction == 'เหนี่ยวนำ'"
      label="วิธีที่ใช้เหนี่ยวนำ(ระบุ)"
      v-model="form.how_induction"
    >
      <template v-slot:prepend-inner>
        <span class="em em-memo form-emoji" aria-hidden="true"></span>
      </template>
    </v-text-field>
    <v-text-field
      v-if="form.is_induction == 'เหนี่ยวนำ'"
      type="date"
      label="วัน/เดือน/ปี ที่เหนี่ยวนำ"
      v-model="form.date_induction"
    >
      <template v-slot:prepend-inner>
        <span class="em em-calendar form-emoji" aria-hidden="true"></span>
      </template>
    </v-text-field>
    <v-btn
      @click="saveStepOne()"
      class="w-full"
      type="submit"
      rounded
      large
      color="success"
      >ต่อไป</v-btn
    >
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from "nuxt-property-decorator";
import _ from "lodash";
import { Core } from "@/vuexes/core";
import { Auth } from "@/vuexes/auth";
import { Web } from "@/vuexes/web";
const api = "/api/v1/ox_manager";
const apis = "/api/v1";
const tool = "/api/v1/tool";
import { Ox } from "@/vuexes/ox";

@Component({
  components: {},
})
class Food extends Vue {
  breed: any = Ox.currentBreed;
  currentId: any = this.$route.query.id;
  response: boolean = true;
  dialog: boolean = false;
  lists: any = [];
  form: any = {};
  choices: any = {};

  dataBreed: any = {};
  checkForm: any = {};

  async saveStepOne() {
    this.form.breed = this.breed.id;
    let store = await Core.postHttp(
      `${apis}/breed/female/step1_induction/`,
      this.form
    );
    await Core.putHttp(`/api/v1/breed/female/breed/${this.currentId}/`, {
      step: 2,
      ox: this.breed.ox,
    });
    if (store.id) {
      await Core.putHttp(`/api/v1/ox/ox/${this.currentId}/`, {
        status: "สำเร็จ",
      });
      // await this.$router.push(`/ox/breed?id=${this.currentId}`);
      await this.$emit('changeTab',1)
    }else{
       await this.$emit('changeTab',1)
    }

    console.log(this.currentId);
  }

  convertDate(date: any) {
    return Web.convertDate(date);
  }
}

export default Food
</script>

<style scoped>
@import url("https://emoji-css.afeld.me/emoji.css");

.form-emoji {
  margin-right: 4px;
}
</style>