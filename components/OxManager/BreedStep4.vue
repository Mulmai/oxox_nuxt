<template>
  <div>
    <v-text-field
      type="date"
      label="วัน/เดือน/ปี ที่ผสม"
      v-model="form.date_back_estrus"
    >
      <template v-slot:prepend-inner>
        <span class="em em-calendar form-emoji" aria-hidden="true"></span>
      </template>
    </v-text-field>
    <v-select
      :items="['กลับสัด', 'ไม่กลับสัด']"
      item-text="name"
      item-value="id"
      label="ผลการตรวจการกลับสัด"
      v-model="form.is_back_estrus"
    >
      <template v-slot:prepend-inner>
        <span class="em em-repeat form-emoji" aria-hidden="true"></span>
      </template>
    </v-select>
    <v-text-field
      v-if="form.is_back_estrus == 'ไม่กลับสัด'"
      type="date"
      label="วันที่ควรตรวจท้อง"
      v-model="form.date_test_pregnant"
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
  async getEnv() {
    this.choices = {
      gene: await Core.getHttp("/api/v1/tool/gene/"),
      marker: await Core.getHttp("/api/v1/tool/maintainmaker/"),
    };
  }
  async saveStepOne() {
    this.form.breed = this.breed.id;
    let store = await Core.postHttp(
      `${apis}/breed/female/step4_back_estrus/`,
      this.form
    );
    await Core.putHttp(`/api/v1/breed/female/breed/${this.currentId}/`, {
      step: 4,
      ox: this.breed.ox,
    });
    if (store.id) {
      await Core.putHttp(`/api/v1/ox/ox/${this.currentId}/`, {
        status: "สำเร็จ",
      });
      // await this.$router.push(`/ox/breed?id=${this.currentId}`);
      await this.$emit("changeTab", 4);
    } else {
      await this.$emit("changeTab", 4);
    }

    console.log(this.currentId);
  }
  async created() {
    await this.getEnv();
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