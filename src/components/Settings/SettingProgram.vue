<template>
  <div class="uk-padding-large uk-padding-remove-top">
    <h2>프로그램 세부 설정</h2>
    시작 시 프로그램 자동 실행
    <input
      type="checkbox"
      class="uk-checkbox"
      v-model="onstartup"
      @change="changeMode"
    />
    <p>
      달력 갱신 시간
      <select
        class="uk-select uk-width-1-6"
        v-model="setting.refreshTime"
        @change="setTime"
      >
        <option value="60" v-if="setting.refreshTime == 60" selected="selected">1분</option>
        <option value="300" v-if="setting.refreshTime == 300" selected="selected">5분</option>
        <option value="600" v-if="setting.refreshTime == 600" selected="selected">10분</option>
        <option value="1800" v-if="setting.refreshTime == 1800" selected="selected">30분</option>
        <option value="3600" v-if="setting.refreshTime == 3600" selected="selected">1시간</option>
        <option value="10800" v-if="setting.refreshTime == 10800" selected="selected">3시간</option>
      </select>
    </p>
    <p>
      <button class="uk-button uk-button-small uk-button-danger" @click="restartApp"
        >초기화</button
      >
    </p>
  </div>
</template>

<script>
import * as remote from "@electron/remote";
import fs from "fs";

const app = remote.app

export default {
  data() {
    return {
      onstartup: false
    };
  },
  name: "setting-program",
  mounted() {
    var temp = app.getLoginItemSettings().openAtLogin
    if (process.platform == 'win32')
      temp = app.getLoginItemSettings().executableWillLaunchAtLogin
    this.onstartup = temp
    console.log(temp)
  },
  methods: {
    changeMode(e) {
      if (e.target.checked) {
        app.setLoginItemSettings({
          openAtLogin: true,
          name: "Desktop Calendar"
        })
      } else {
        app.setLoginItemSettings({
          openAtLogin: false,
          name: "Desktop Calendar"
        })
      }
    },
    restartApp() {
      localStorage.clear();
      fs.unlink(this.appdata + "/calendar.json", e => {
        if (e)
          console.log(e)
        fs.unlink(this.appdata + "/token.json", e => {
          if (e)
            console.log(e)
          remote.app.relaunch()
          remote.app.exit(0)
        });
      });
    },
    setTime(e) {
      this.setting.changeOption("refreshTime", e.target.value)
    }
  },
  props: ["set"],
  computed: {
    setting() {
      return this.set
    }
  }
};
</script>

<style></style>
