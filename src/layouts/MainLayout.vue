<template>
  <div
    class="page-main"
    ref="pageMain"
    @scroll="onSetFooterShow"
  >
    <q-layout class="main-box">
      <q-header
        elevated
        class="page-header"
      >
        <div
          class="home-title"
          :style="{width:pageWidth+'px'}"
        >
          <!-- <img
            class="title-logo"
            @click="toHome"
            src="../assets/logo.svg"
            alt=""
            width="100"
            height="50"
          > -->
          <miSelect
            v-model="lang"
            style="width:100px"
            :borderless="false"
            outlined
            :clearable="false"
            :use-input="false"
            hide-bottom-space
            @onchange="changeLang"
            :options="[
              {label:'中文',value:'zh'},
              {label:'English',value:'en'},
            ]"
          ></miSelect>
          <headBarsVue ref="headerBar"></headBarsVue>

          <div v-if="!user.userName">
            <div class="login-title-box">
              <mi-button
                :label="$t('login')"
                flat
                @click="openLoginPage('1')"
              ></mi-button>/<mi-button
                :label="$t('register')"
                flat
                @click="openLoginPage('2')"
              ></mi-button>
            </div>
          </div>
          <div
            class="login-title-box login-title-user"
            v-else
          >
            <miButton
              @click="openQiandao"
              style="margin-right:15px"
              color="primary"
              :label="$t('qiandao')"
            ></miButton>
            <q-chip
              class="user-chip"
              text-color="#333"
            >
              <q-avatar><img
                  :src="user.avartar"
                  alt=""
                ></q-avatar>
              {{user.nickName}}
              <q-popup-proxy
                :offset="[20, 10]"
                v-model="userPop"
                transition-show="flip-up"
                transition-hide="flip-down"
              >
                <userPanel
                  @logout="logout"
                  @toPersonal="toPersonal"
                ></userPanel>
              </q-popup-proxy>
            </q-chip>

          </div>
        </div>
      </q-header>

      <q-page-container style="padding-top:0">
        <div
          v-if="userIsPass"
          class="page-container"
          :style="{width:pageWidth+'px'}"
        >
          <router-view />
          <div class="page-footer">
            <div>
              <div
                class="footer-line"
                style="font-size:18px"
              >©2024 hzh copyright</div>
              <div
                class="footer-line"
                style="font-size:14px"
              ><a
                  target="__blank"
                  href="https://beian.miit.gov.cn "
                >桂ICP备2024031972号</a>
                <img
                  style="margin-left:10px"
                  width="16"
                  src="../assets/images/common/gaongan.png"
                  alt=""
                >
                <a
                  target="__blank"
                  href="https://beian.mps.gov.cn/#/"
                >桂公网安备45030302000299号</a>
              </div>
            </div>
          </div>
        </div>
      </q-page-container>

      <miDialog
        height="570px"
        v-model="loginVisible"
      >
        <loginPage @close="closeLogin"></loginPage>
      </miDialog>
    </q-layout>

    <div
      class="loading-overlay"
      v-show="isShowbar"
    ></div>
    <q-spinner-puff
      class="spinner-audio"
      v-if="isShowbar"
      color="primary"
      size="80px"
    />
    <div
      class="pet-box"
      :style="{
        left:petPosition.left+'px',
        top:petPosition.top+'px',
        width:petWidth+'px',
        height:petHeight+'px'
      }"
      v-if="petBoxVisible"
    >
      <img
        class="pet-box-close"
        @click="petBoxVisible=false"
        src="../assets/images/common/close.svg"
        alt=""
      >
      <miButton
        style="margin-left:5px"
        v-if="!petVisible"
        :label="$t('loadPet')"
        @click="showPet"
      ></miButton>
      <pet
        :height="petHeight"
        :width="petWidth"
        :left="defaultPosition.left"
        :top="defaultPosition.top"
        @changePosition="changePosition"
        @setPosition="setPosition"
        v-if="petVisible"
      ></pet>
    </div>
    <miDialog
      v-model="qiandaoVisible"
      width="600px"
      height="400px"
    >
      <qiandaoDia></qiandaoDia>
    </miDialog>

  </div>

</template>

<script setup>
import { computed, inject, nextTick, onMounted, ref, watch } from 'vue'
import miButton from "src/components/form/mi-button.vue"
import loginPage from "src/components/login.vue"
import miDialog from "src/components/mi-dialog.vue"
import { useRoute, useRouter } from "vue-router"
import headBarsVue from './header-bars.vue'
import { useUserStore } from 'src/stores/user'
import userPanel from "./userPanel.vue"
import { useCommonStore } from 'src/stores/common'
import { useHttpStore } from 'src/stores/http'
import { config } from 'src/config'
import { tokenUser } from 'src/http/user'
import pet from "./pet.vue"
import { defaultPageWidth, downloadFile } from 'src/utils'
import qiandaoDia from "src/components/qiandao.vue"
import { getClientIP, getWeather } from 'src/http/common'
import miSelect from "src/components/form/mi-select.vue"
import { useI18n } from "vue-i18n"
const i18n = useI18n()

const headerBar = ref(null)
const petVisible = ref(false)
const petBoxVisible = ref(true)
const showHideBtn = ref(false)

const lang = computed({
  get() {
    let v = sessionStorage.getItem("lang")
    if (!v) {
      const isChinese =
        navigator.language.toLowerCase() === "zh-cn" ||
        navigator.language.toLowerCase() === "zh";
      v = isChinese
        ? "zh"
        : "en";
    }
    return v
  },
  set(val) {
    sessionStorage.setItem("lang", val)
  }
})



const userStore = useUserStore()

const pageWidth = ref(window.innerWidth >= defaultPageWidth ? defaultPageWidth : window.innerWidth)

const router = useRouter()
const toHome = () => {
  router.push({ path: "/" })
}

const changeLang = (key) => {
  i18n.locale.value = key
  nextTick(() => {
    headerBar.value.barLineMove()
  })

}

const commonStore = useCommonStore()

const footerShow = computed({
  get() {
    return commonStore.footerShow
  },
})

const pageMain = ref(null)

const onSetFooterShow = () => {
  commonStore.onSetFooterShow()
}

const qiandaoVisible = ref(false)

const openQiandao = () => {
  qiandaoVisible.value = true
}

const petWidth = ref(185)
const petHeight = ref(230)

//鼠标按下时位置
const defaultPosition = ref({ left: window.innerWidth - petWidth.value, top: window.innerHeight - petHeight.value })

//当前位置
const petPosition = ref({ left: window.innerWidth - petWidth.value, top: window.innerHeight - petHeight.value })

//鼠标move
const changePosition = (left, top) => {
  petPosition.value.left = defaultPosition.value.left + left
  petPosition.value.top = defaultPosition.value.top + top
}

//鼠标up
const setPosition = (left, top) => {
  defaultPosition.value.left = left
  defaultPosition.value.top = top
}

const showPet = () => {
  petVisible.value = true
}

const common = useCommonStore()

const user = computed({
  get() {
    return userStore.user
  },
  set(val) {
    userStore.user = val
  }
})

const userPop = ref(false)

const loginVisible = computed({
  get() {
    return common.loginVisible
  },
  set(val) {
    common.loginVisible = val
  }
})

const openLoginPage = (type) => {
  if (type === "1") {
    loginVisible.value = true
  } else {
    let a = document.createElement("a")
    a.target = "_blank"
    a.href = window.location.origin + "/register"
    a.click()
  }
}

const logout = () => {
  userStore.logout()
  router.push({ path: "/" })
}

const toPersonal = () => {
  router.push({ name: "personalCenter" })
}

const closeLogin = () => {
  loginVisible.value = false
}

const httpStore = useHttpStore()
const isShowbar = ref(false)
watch(() => httpStore.isRequested, (nv) => {
  if (nv) {
    isShowbar.value = true
  } else {
    setTimeout(() => {
      isShowbar.value = false
    }, 500);
  }
}, {
  deep: true,
})

const userIsPass = ref(false)

onMounted(async () => {
  let user = JSON.parse(localStorage.getItem("_user"))
  let token = localStorage.getItem("token")
  if (user && token) {
    let res = await tokenUser({ token })
    if (res.code === 0) {
      let userStore = useUserStore()
      userStore.setUser(res.data)
      userStore.setIslogin(true)
    }
    userIsPass.value = true
  } else {
    userIsPass.value = true
  }

  window.addEventListener("resize", () => {
    if (defaultPosition.value.left >= window.innerWidth - petWidth.value) {
      defaultPosition.value.left = window.innerWidth - petWidth.value
      petPosition.value.left = window.innerWidth - petWidth.value
    }
    if (defaultPosition.value.top >= window.innerHeight - petHeight.value) {
      defaultPosition.value.top = window.innerHeight - petHeight.value
      petPosition.value.top = window.innerHeight - petHeight.value
    }
    if (pageWidth.value > window.innerWidth) {
      pageWidth.value = window.innerWidth
    } else {
      if (window.innerWidth < defaultPageWidth) {
        pageWidth.value = window.innerWidth
      } else {
        pageWidth.value = defaultPageWidth
      }
    }
  })
})
</script>

<style lang="scss" scoped>
.page-main {
  overflow: auto;
  margin-right: 6px;
  margin-top: 55px;
  height: calc(100vh - 60px);
  width: calc(100vw - 6px);
  position: relative;
  .page-header {
    position: fixed;
    display: flex;
    justify-content: center;
    background-color: #fff;
    color: #333;
  }

  .title-logo {
    cursor: pointer;
  }
  .login-title-box {
    .user-chip {
      padding: 20px;
      cursor: pointer;
    }
  }
  .login-title-user {
    display: flex;
    align-items: center;
  }
  .home-title {
    display: flex;
    align-items: center;
    height: 50px;
  }
  .main-box {
    // min-height: auto !important;
    min-height: calc(100vh - 60px) !important;
    display: flex;
    justify-content: center;
  }
  .page-container {
  }
  .spinner-audio {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
  .loading-overlay {
    position: absolute;
    left: 0;
    right: 0;
    bottom: 0;
    top: 0;
    background-color: rgba(33, 33, 33, 0);
  }
  .pet-box {
    position: fixed;
    width: 175px;
    height: 220px;
    .pet-box-close {
      position: absolute;
      top: 0;
      left: -30px;
      transform: translate(-50%, -50%);
      width: 20px;
      height: 20px;
      cursor: pointer;
      &:hover {
        opacity: 0.7;
      }
      &:active {
        opacity: 0.9;
      }
    }
  }
}
.page-footer {
  left: 0;
  right: 0;
  height: 80px;
  color: #333;
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  bottom: -80px;
  transition: all 0.1s;
  overflow: hidden;
  background-color: #fff;
  border-top: 1px solid #aaa;
  .footer-line {
    display: flex;
    align-items: center;
  }
}
</style>