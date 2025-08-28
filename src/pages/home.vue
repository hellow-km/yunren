<template>
  <div class="home-page">
    <div>
      <q-carousel
        transition-prev="scale"
        transition-next="scale"
        animated
        v-model="slide"
        arrows
        navigation
        infinite
        control-color="teal"
        height="650px"
      >
        <q-carousel-slide
          v-for="item in 8"
          :key="item"
          :name="item"
          class="column no-wrap flex-center"
        >
          <div style="height:90%">
            <img
              style="max-width:100%"
              height="100%"
              :src="`/images/home/2 (${item}).png`"
              alt=""
            >
          </div>
        </q-carousel-slide>
      </q-carousel>
      <div style="text-align:center">
        <span style="margin-right:8px">
          <miButton label="下载windows桌面版v1.0.3"></miButton>
          <q-menu>
            <div style="padding:15px">
              <div>
                <a
                  target="_blank"
                  href="https://github.com/hzh-Neo/yunren_tools_release/releases/"
                >
                  <miButton label="github下载"></miButton>
                </a>
              </div>
              <div style="margin-top:10px">
                <a
                  target="_blank"
                  href="https://www.yunren.online:14243/apk/yunren_tools-win32-x64_1.0.3.rar"
                >
                  <miButton label="服务器下载"></miButton>
                </a>
              </div>
            </div>
          </q-menu>
        </span>
        <div
          style="font-size:14px;color:#aaa;margin-top:10px"
          v-show="false"
        >最近会做个视频转gif放到软件自己用来转，大概放到v1.0.3吧，刚刚想用发现网上那些套路多......</div>
      </div>
    </div>
    <div
      class="menu-box"
      :style="{
        height:Math.ceil(menuList.length/4)*170+200+'px'
      }"
      v-show="isFullWidth"
    >
      <div
        class="text-h4 menu-box-title upToAnime"
        data-top="50"
      >{{$t("onlineTools")}}</div>
      <div class="home-tools-box">
        <div class="home-tools-item">
          <div
            class="tool-itembox upToAnime"
            :data-top="item.top"
            :data-left="item.left"
            v-for="item in menuList"
            :key="item.title"
          >
            <a :href="item.path">
              <img
                class="anime-img"
                :src="item.imgSrc"
                alt=""
              >
              <div class="overlay-box">
                <div class="tool-item-overlay">
                  <div class="item-overlay-box">
                    <h1 class="item-overlay-title text-h5">{{$t(item.title)}}</h1>
                    <h2 class="item-overlay-desc">{{$t(item.desc)}}</h2>
                  </div>
                </div>

              </div>
              <h1 class="tool-item-overlay tool-item-overlay-right">{{$t(item.title)}}</h1>
            </a>
          </div>
        </div>
      </div>
      <div
        class="menu-box-overlay"
        ref="menuBoxLayout"
      ></div>
    </div>
    <div
      class="menu-box-notfull"
      v-show="!isFullWidth"
    >
      <div class="text-h4 menu-box-title">{{$t("onlineTools")}}</div>
      <div class="home-tools-box">
        <div
          class="tool-itembox"
          :data-top="item.top"
          :data-left="item.left"
          v-for="item in menuList"
          :key="item.title"
        >
          <a :href="item.path">
            <img
              class="anime-img"
              :src="item.imgSrc"
              alt=""
            >
            <div class="overlay-box">
              <div class="tool-item-overlay">
                <div class="item-overlay-box">
                  <h1 class="item-overlay-title text-h5">{{$t(item.title)}}</h1>
                  <h2 class="item-overlay-desc">{{$t(item.desc)}}</h2>
                </div>
              </div>
              <div class="tool-item-overlay tool-item-overlay-right">{{$t(item.title)}}</div>
            </div>
          </a>
        </div>
      </div>
    </div>
    <div class="article-box">
      <div>
        <div class="hot-lists">
          <div
            class="hot-context hot-articles"
            :class="{'hot-context-no':!isFullWidth}"
          >
            <miButton
              @click="uploadArticle"
              :label="$t('uploadYourArticle')"
            ></miButton>
            <div class="hot-box">
              <div
                class="hot-item"
                v-for="(item,index) in hotArticles"
                :key="item.index"
                @click="clickHotArticle(item)"
                :class="{
                  'hot-item-no1':index===0,
                  'hot-item-no2':index===1,
                  'hot-item-no3':index===2
                }"
              >
                <span :class="{
                  'index-no1':index===0,
                  'index-no2':index===1,
                  'index-no3':index===2
                }">{{index+1}}</span>
                <span class="hot-item-text"><img
                    class="hot-img"
                    v-show="item.imgSrc"
                    :src="item.imgSrc"
                    alt=""
                  >{{item.title}}</span>
                <span><img
                    class="hot-icon"
                    width="16px"
                    src="../assets/images/tools/fire.png"
                    alt=""
                  >{{item.hotNum?pointNumZeroClear(Number(item.hotNum)):0}}</span>
              </div>
            </div>
          </div>
          <!-- <div
            class="hot-context hot-games"
            :class="{'hot-context-no':!isFullWidth}"
          > -->
          <!-- <div class="text-h6">站内小游戏排行</div>
            <div class="hot-box">
              <div
                class="hot-item"
                @click="clickHotGame(item)"
                v-for="(item,index) in hotGames"
                :key="item.index"
                :class="{
                  'hot-item-no1':index===0,
                  'hot-item-no2':index===1,
                  'hot-item-no3':index===2
                }"
              >
                <q-tooltip>
                  <span style="font-size:18px">{{item.desc}}</span>
                </q-tooltip>
                <span :class="{
                  'index-no1':index===0,
                  'index-no2':index===1,
                  'index-no3':index===2
                }">{{index+1}}</span>

                <span class="hot-item-text"><img
                    class="hot-img"
                    :src="item.imgSrc"
                    alt=""
                  >{{item.name}}</span>
                <span><img
                    class="hot-icon"
                    width="16px"
                    src="../assets/images/tools/fire.png"
                    alt=""
                  >{{item.hotNum}}</span>
              </div>
            </div> -->
          <!-- </div> -->
        </div>
      </div>
    </div>
    <weather class="home-weather"></weather>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import imgIP from "src/assets/images/tools/ip.png"
import miButton from "src/components/form/mi-button.vue"
import { useRoute, useRouter } from "vue-router";
import { getArticles } from "src/http/article";
import gamesJSON from "./gamesJSON"
import { defaultPageWidth, isNewDay, pointNumZeroClear } from "src/utils";
import { useUserStore } from "src/stores/user";
import { useCommonStore } from "src/stores/common";
import { getHotGames } from "src/http/games"
import { openGamesWindows } from "src/utils/games"
import { useQuasar } from "quasar";
import { toolsImg } from "src/utils/images"
import { baidiTuisong, getClientIP, getWeather } from "src/http/common";
import weather from "src/pages/components/weather.vue"
import { xlsText, xlsDesc, imageMerge } from "src/utils/string"


const $q = useQuasar()
const router = useRouter()
const route = useRoute()
const hotArticles = ref([])
const userStore = useUserStore()
const hotGames = ref([])
const common = useCommonStore()
const slide = ref(1)
const itemHeight = ref(300)

defineOptions({
  name: 'IndexPage'
});

//获取文章数据
const getHotArticle = async () => {
  let res = await getArticles({ tag: "", page: 1, size: 10 })
  let data = res.data
  let d = data.data.map(item => {
    //order by goodNum desc,likeNum desc ,readUserNum desc,readNum desc ,article_ID desc
    item.hotNum = item.goodNum * 5 + item.likeNum * 3 + item.readUserNum * 2 + item.readNum * 0.1
    return item
  })
  d.sort((a, b) => b.hotNum - a.hotNum)
  hotArticles.value = d
}

//跳转文章详情
const clickHotArticle = (item) => {
  router.push({
    name: "articleDetail",
    params: {
      aID: item.article_ID
    }
  })
}

//跳转文章详情
const clickHotGame = (item) => {
  openGamesWindows(item.href, item.name)
}

const gamesMenu = ref(gamesJSON)

const allGames = ref(gamesMenu.value.reduce((prev, cur) => {
  prev.push(...cur.list.map(item => {
    return item
  }))
  return prev
}, []))



//获取文章数据
const getHomeHotGames = async () => {
  let res = await getHotGames({})
  let homeHotGames = []
  if (res.code === 0) {
    homeHotGames = res.data
  }
  let hoGames = localStorage.getItem("hotGames")
  if (hoGames) {
    let hg = JSON.parse(hoGames)
    if (hg.date) {
      let isNewD = isNewDay(new Date(hg.date), new Date())
      if (!isNewD) {
        hotGames.value = hg.games
        return
      }
    }
  }

  let randomNum = []
  function pushRandom() {
    let num = Math.ceil(Math.random() * (allGames.value.length - 1))
    if (!randomNum.includes(num)) {
      randomNum.push(num)
    } else {
      pushRandom()
    }
  }
  hotGames.value = []
  for (let i = 0; i < homeHotGames.length; i++) {
    let g = homeHotGames[i]
    hotGames.value.push({
      ...allGames.value.find(item => item.name == g.gameName),
      hotNum: g.clickNum
    })
  }

  for (let i = 0; i < 10 - homeHotGames.length; i++) {
    pushRandom()
  }

  randomNum.forEach(num => {
    hotGames.value.push({ ...allGames.value[num], hotNum: 0 })
  })
  localStorage.setItem("hotGames", JSON.stringify({ date: new Date().getTime(), games: hotGames.value }))
}

const menuList = ref([
  {
    title: "translations.SpriteMake",
    path: "/SpriteMake",
    imgSrc: toolsImg.SpriteMake,
    desc: "translations.SpriteMakeDesc"
  },
  {
    title: "imageMerge",
    path: "/imageMerge",
    desc: "translations.uploadMaterialDesc",
    imgSrc: toolsImg.imageMerge
  },
  {
    imgSrc: toolsImg.XLS,
    title: "xlsText",
    desc: "xlsDesc",
    path: "/xlsEdit"
  },
  {
    imgSrc: toolsImg.imgCompress,
    title: "translations.imageCompressTitle",
    desc: "translations.imageCompressDesc",
    path: "/imgCompress"
  },
  {
    imgSrc: toolsImg.imgConvert,
    title: "translations.imageConvertTitle",
    desc: "translations.imageConvertDesc",
    path: "/imgConverter"
  },
  {
    title: "translations.textToImage",
    path: "/textToImage",
    imgSrc: toolsImg.textImage,
    desc: "translations.textToImage",
  },
  {
    title: "translations.qrRegion",
    path: "/qrRegion",
    imgSrc: toolsImg.qrRegion,
    desc: "translations.qrRegionDesc",
  },
  {
    title: "translations.qrCreate",
    path: "/qrCreate",
    imgSrc: toolsImg.qrCreate,
    desc: "translations.qrCreateDesc",
  },


  {
    imgSrc: toolsImg.JSCompress,
    title: "translations.jsCompressTitle",
    desc: "translations.jsCompressDesc",
    path: "/jsCompress"
  },
  {
    imgSrc: toolsImg.CSSCompress,
    title: "translations.cssCompressTitle",
    desc: "translations.cssCompressDesc",
    path: "/cssCompress"
  },
  {
    imgSrc: toolsImg.JSCompress,
    title: "translations.jsonCompressTitle",
    desc: "translations.jsonCompressDesc",
    path: "/jsonCompress"
  },
  {
    imgSrc: toolsImg.JSSecret,
    title: "translations.jsEncryptTitle",
    desc: "translations.jsEncryptDesc",
    path: "/jsObfuscator"
  },
  {
    imgSrc: toolsImg.htmlXml,
    title: "translations.htmlToXMLTitle",
    desc: "translations.htmlToXMLDesc",
    path: "/htmlToXML"
  },
  {
    imgSrc: toolsImg.imgInfo,
    title: "translations.pictureInfoTitle",
    desc: "translations.pictureInfoDesc",
    path: "/pictureInfo"
  },
  {
    imgSrc: toolsImg.imgIP,
    title: "translations.ipQueryTitle",
    desc: "translations.ipQueryDesc",
    path: "/IPSelect"
  },
].map((item, index) => {
  let moI = index % 4, chuI = Math.floor(index / 4)
  item.left = moI * 25 + "%"
  if (chuI == 0) {
    item.top = 150
  } else {
    item.top = 150 + 170 * chuI
  }
  return item
}))

console.log("menuList", menuList.value.map(item => {
  return `<url><loc>https://www.yunren.online${item.path}</loc></url>`
}).join("\n") + `\n<url><loc>https://www.yunren.online/articles</loc></url>`);

// console.log("menuList", menuList.value.map(item => {
//   return `https://www.yunren.online${item.path}`
// }).join("\n"));

const toPath = (path) => {
  router.push(path)
  gtag("event", "click", {
    event_category: path,
    event_label: "首页",
    value: 1,
  });
}

const uploadArticle = () => {
  if (userStore.isLogin) {
    router.push({
      name: "uploadArticle",
      params: {
        uID: userStore.user.user_ID
      }
    })
  } else {
    common.loginVisible = true
  }
}

const animeBox = () => {
  let animeItems = document.getElementsByClassName("upToAnime")
  for (let i = 0; i < animeItems.length; i++) {
    const el = animeItems[i];
    setTimeout(() => {
      el.style.top = el.dataset.top + "px"
      el.style.left = el.dataset.left
      el.style.transform = "translateY(0)"
      let imgEl = el.getElementsByClassName("anime-img")[0]
      if (!imgEl) {
        return
      }
      setTimeout(() => {
        animeImg(imgEl)
      }, 500);
      //100 * (i + 1)
    }, 0);
  }
}

const animeImg = (imgEl) => {
  if (imgEl.classList) {
    imgEl.classList.add("bounce")
  }
}
const menuBoxLayout = ref(null)

//页面滚动时
const scrollPageEvent = () => {
  let pageMain = document.getElementsByClassName("page-main")[0]
  let max = Math.ceil(menuList.value.length / 3) * 300
  pageMain.onscroll = function (e) {
    if (route.name == "home") {
      if (e.target.scrollTop <= max) {
        if (e.target.scrollTop > 100) {
          let radius = e.target.scrollTop / max * 100
          Object.assign(menuBoxLayout.value.style, {
            height: radius + "%",
          })
        } else {
          Object.assign(menuBoxLayout.value.style, {
            height: "0px"
          })
        }
      }
    }
  }
}

const isFullWidth = ref(window.innerWidth >= defaultPageWidth)

const onResize = () => {
  if (window.innerWidth >= defaultPageWidth) {
    isFullWidth.value = true
  } else {
    isFullWidth.value = false
  }
}

onMounted(async () => {
  scrollPageEvent()
  animeBox()
  getHotArticle()
  getHomeHotGames()
  window.addEventListener("resize", onResize)
  // let res = await baidiTuisong(menuList.value.map(item => {
  //   return `https://www.yunren.online${item.path}`
  // }).join("\n"))
  // console.log("Res", JSON.parse(res.data.result));
})
</script>

<style lang="scss" scoped>
.menu-box {
  position: relative;
  a {
    color: #333 !important;
  }
  .menu-box-title {
  }
  .menu-box-overlay {
    transition: all 0.2s;
    position: absolute;
    background-color: #fff;
    z-index: 1;
    right: unset;
    bottom: unset;
    top: 0;
    left: 0;
    width: 0px;
    height: 0px;
  }
  .miniScreen {
    position: relative;
  }
  .upToAnime {
    position: absolute;
    top: -100px;
    left: 0;
    transition: all 0.5s;
    transform: translateY(-100%);
  }
  .home-tools-item {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    .tool-itembox {
      min-width: 150px;
      width: 20%;
      border: 1px solid #eee;
      height: 150px;
      text-align: center;
      border-radius: 6px;
      cursor: pointer;
      overflow: hidden;
      .overlay-box {
        display: flex;
      }
      &:hover {
        .tool-item-overlay {
          transform: translateX(0);
        }
        .tool-item-overlay-right {
          left: 100%;
        }
        .item-overlay-desc {
          white-space: normal;
        }
      }
      .tool-item-overlay {
        transition: all 0.5s;
        position: absolute;
        top: 0;
        right: 0;
        transform: translateX(-100%);
        left: 0;
        bottom: 0;
        background-color: rgba(0, 0, 0, 0.1);
        overflow: hidden;
        white-space: nowrap;
        .item-overlay-box {
          position: absolute;
          height: 150px;
          display: flex;
          align-items: center;
          flex-wrap: wrap;
          justify-content: center;
          background-color: rgba(0, 0, 0, 0.5);
          color: #fff;
          left: 0;
          right: 0;
          bottom: 0;
          h1,
          h2 {
            font-size: 20px;
            line-height: 1.5;
            margin: 0;
            padding: 0;
          }
          h2 {
            font-size: 16px;
          }
          .item-overlay-desc {
            white-space: normal;
            padding: 0 10px;
          }
          .item-overlay-title {
            width: 100%;
          }
        }
      }
      .tool-item-overlay-right {
        transform: translateX(0);
        background-color: transparent;
        top: unset;
        bottom: 0;
        right: 0;
        color: #fff;
        font-size: 20px;
        height: 30px;
        line-height: 1.5;
        margin: 0;
        padding: 0;
        background-color: rgba(0, 0, 0, 0.3);
      }
    }
  }

  .anime-img {
    margin-top: 10px;
    height: 100px;
    width: calc(100% - 100px);
    object-fit: scale-down;
  }
  .bounce {
    animation: bounceAnimation 0.5s ease 2 alternate;
  }

  @keyframes bounceAnimation {
    0% {
      transform: translateY(0);
    }
    100% {
      transform: translateY(-20px);
    }
  }
}

.menu-box-notfull {
  a {
    color: #333;
  }
  margin: 25px 0;
  .menu-box-title {
    width: 100%;
    text-align: center;
  }
  .home-tools-box {
    display: flex;
    flex-wrap: wrap;
    .tool-itembox {
      width: 49%;
      position: relative;
      border: 1px solid #eee;
      height: 150px;
      border-radius: 6px;
      cursor: pointer;
      margin-top: 15px;
      margin-right: 3px;
      display: flex;
      align-items: center;
      justify-content: center;
      overflow: hidden;
      img {
        height: 100px;
      }
      &:hover {
        .tool-item-overlay {
          transform: translateX(0);
        }
        .item-overlay-desc {
          white-space: normal;
        }
        .tool-item-overlay-right {
          left: 100%;
        }
      }
      .tool-item-overlay {
        transition: all 0.5s;
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        transform: translateX(-100%);
        bottom: 0;
        background-color: rgba(0, 0, 0, 0.1);

        white-space: nowrap;
        .item-overlay-box {
          position: absolute;
          height: 150px;
          display: flex;
          align-items: center;
          flex-wrap: wrap;
          justify-content: center;
          background-color: rgba(0, 0, 0, 0.5);
          color: #fff;
          left: 0;
          right: 0;
          bottom: 0;
          .item-overlay-title {
            text-align: center;
            width: 100%;
          }
        }
      }
      .tool-item-overlay-right {
        transform: translateX(0);
        padding-top: 10px;
        background-color: transparent;
        font-size: 20px;
        right: 0;
        padding-left: 20px;
      }
    }
  }
}

.article-box {
  margin-bottom: 55px;
}

.hot-lists {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  .hot-articles {
    margin-bottom: 10px;
  }
  .hot-context {
    width: 80%;
    min-width: 350px;
    margin-right: 5%;
  }
  .hot-context-no {
    margin-right: 5px !important;
  }
  .hot-box {
    border-radius: 6px;
    border: 1px solid #eee;
    min-height: 200px;
    margin-top: 20px;
    .hot-item {
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 16px;
      border-bottom: 1px solid #eee;
      padding: 15px 15px;
      cursor: pointer;
      .hot-icon {
        margin-right: 5px;
      }
      &:hover {
        background-color: #eee;
        .hot-img {
          height: 50px !important;
        }
      }
      .hot-item-index {
        margin-right: 30px;
      }
      .hot-item-text {
        display: flex;
        align-items: center;
        margin: 0 auto;
        position: relative;
        .hot-img {
          left: -15px;
          transform: translateX(-100%);
          position: absolute;
          width: auto;
          height: 0px;
          transition: all 0.3s;
        }
      }
      .index-no1 {
        color: rgb(255, 0, 128);
      }
      .index-no2 {
        color: #ffc107;
        font-size: 20px;
      }
      .index-no3 {
        color: #26a69a;
        font-size: 18px;
      }

      &:last-child {
        border-bottom: 0;
        margin: 0;
      }
    }
    .hot-item-no1 {
      font-size: 22px;
    }
    .hot-item-no2 {
      font-size: 20px;
    }
    .hot-item-no3 {
      font-size: 18px;
    }
  }
}
.home-weather {
  position: absolute;
  top: 30px;
  right: 30px;
}
</style>
