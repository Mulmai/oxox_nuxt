<template>
  <div>
    <v-toolbar class="m-1" flat color="transparent">
      การผสมพันธุ์ (FM)
      <v-spacer></v-spacer>
      <v-btn @click="addBreedFemale()" rounded color="primary">เพิ่ม</v-btn>
    </v-toolbar>
    <div>
      <div
        @click="getBreeding(list)"
        v-for="(list, index) in lists"
        :key="index"
      >
        <Core-Menu
          :name="convertDate(list.created_at)"
          icon="medical-report.png"
          text="ประวัติการผสมพันธุ์"
        ></Core-Menu>
      </div>
    </div>
    <v-dialog v-model="dialog" fullscreen>
      <v-card>
        <v-card-title>
          <span>{{ form.id ? "แก้ไข" : "เพิ่ม" }}การผสมพันธุ์</span>
          <v-spacer></v-spacer>
          <v-btn
            @click="(form = {}) && (dialog = false)"
            small
            fab
            color="error"
            >X</v-btn
          >
        </v-card-title>
        <v-card-text v-if="dialog">
          <form @submit.prevent="form.id ? updateData() : saveData()">
            <v-text-field
              type="date"
              label="วัน/เดือน/ปีที่ผสมพันธุ์"
              v-model="form.date_induction"
            >
              <template v-slot:prepend-inner>
                <span class="em em-calendar form-emoji" aria-hidden="true"></span>
              </template>
            </v-text-field>
            <v-select
              :items="['ธรรมชาติ', 'รีดน้ำเชื้อ']"
              item-text="name"
              item-value="id"
              label="การให้น้ำเชื้อ"
              v-model="form.semen"
            >
              <template v-slot:prepend-inner>
                <span class="em em-test_tube form-emoji" aria-hidden="true"></span>
              </template>
            </v-select>
            <v-select
              :items="choices.maker"
              item-text="name"
              item-value="id"
              label="ผู้ทำ"
            >
              <template v-slot:prepend-inner>
                <span class="em em-clipboard form-emoji" aria-hidden="true"></span>
              </template>
            </v-select>
            <v-text-field
              label="Note"
              v-model="form.note"
            >
              <template v-slot:prepend-inner>
                <span class="em em-memo form-emoji" aria-hidden="true"></span>
              </template>
            </v-text-field>

            <v-btn class="w-full" type="submit" rounded large color="success"
              >บันทึก</v-btn
            >
            <v-btn
              class="w-full mt-4"
              dark
              v-if="form.id"
              @click="removeData()"
              color="red"
              rounded
              large
              >ลบ</v-btn
            >
          </form>
        </v-card-text>
      </v-card>
    </v-dialog>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from "nuxt-property-decorator";
import _ from "lodash";
import { Core } from "@/vuexes/core";
import { Auth } from "@/vuexes/auth";
import { Web } from "@/vuexes/web";
import { Ox } from "@/vuexes/ox";
const api = "/api/v1/ox_manager";
const apis = "/api/v1";
const tool = "/api/v1/tool";
@Component({
  components: {},
})
class Food extends Vue {
  currentId: any = this.$route.params.id;
  response: boolean = true;
  dialog: boolean = false;
  lists: any = [];
  form: any = {};
  choices: any = {};

  dataBreed: any = {};
  checkForm: any = {};

  async getBreeding(breed:any){
    await Ox.setBreed(breed)
    await this.$router.push(`/ox/breed/?id=${breed.id}`)
  }

  async getEnv() {
    this.choices = {
      maker: await Core.getHttp(tool + `/healmaker/`),
    };
    this.lists = await Core.getHttp(
      `${apis}/breed/female/breed/?ox=${this.currentId}`
    );
    this.response = true;
  }
  async addBreedFemale() {
    {
      this.checkForm = _.filter(this.lists, ["finished", false]);
      this.dataBreed = { step: 1, finished: false, ox: this.currentId };
      console.log("check: ", this.checkForm.length);
      if (this.checkForm.length == 0) {
        console.log("check length: ", this.checkForm.length);
        let store = await Core.postHttp(
          `${apis}/breed/female/breed/`,
          this.dataBreed
        );
        if (store.id) {
          await Core.putHttp(`/api/v1/ox/ox/${this.currentId}/`, {
            status: "สำเร็จ",
          });
          alert("สร้างสำเร็จ กำลังไปหน้าขั้นตอนการผสมพันธุ์");
          await this.$router.push(`/ox/breed?id=${store.id}`)
          // await this.getEnv();
        }
      }else{
          alert("สร้างไม่สำเร็จ มีขั้นตอนการผสมพันธุ์ค้างอยู่");
      }

    }
  }
  async saveData() {
    this.form.ox = this.currentId;
    let store = await Core.postHttp(
      `${apis}/breed/female/breed/?ox=${this.currentId}`,
      this.form
    );
    if (store.id) {
      await Core.putHttp(`/api/v1/ox/ox/${this.currentId}/`, {
        status: "สำเร็จ",
      });
      alert("บันทึกข้อมูลสำเร็จแล้ว");
      await this.getEnv();
      this.form = {};
      this.dialog = false;
    }
  }
  async updateData() {
    this.form.ox = this.currentId;
    let store = await Core.putHttp(
      `${apis}/breed/female/breed/${this.form.id}/`,
      this.form
    );
    if (store.id) {
      await Core.putHttp(`/api/v1/ox/ox/${this.currentId}/`, {
        status: "สำเร็จ",
      });
      alert("บันทึกข้อมูลสำเร็จแล้ว");
      await this.getEnv();
      this.form = {};
      this.dialog = false;
    }
  }

  async removeData() {
    let check = confirm("คุณแน่ใจใช่ไหม");
    if (check) {
      await Core.putHttp(`/api/v1/ox/ox/${this.currentId}/`, { status: "" });
      await Core.deleteHttp(`${apis}/breed/female/breed/${this.form.id}/`);
      alert("ลบข้อมูลสำเร็จแล้ว");
      await this.getEnv();
      this.form = {};
      this.dialog = false;
    }
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