<template>
  <div id="app">
    <Search @emit-video="computedVideo" />

    <div class="main">
      <div class="main__video">
        <div class="main__play">
          <div id="player"></div>
        </div>

        <Control
          :title="currentTitle"
          :random="videos.isRandom"
          :repeat="videos.isRepeat"
          @emit-play="playVideo"
          @emit-pause="pauseVideo"
          @emit-stop="stopVideo"
          @emit-prev="prevVideo"
          @emit-next="nextVideo"
          @emit-random="isRandom"
          @emit-repeat="isRepeat"
        />

        <div class="main__secret" v-if="secretTechnique.active">
          <div class="main__add">
            <input
              type="text"
              class="main__add-input"
              v-model="secretTechnique.tempInfo"
              @keyup.enter="getInfo"
            />
            <span @click="getInfo">新增</span>
          </div>

          <div class="main__copy">
            <span @click="copyInfo">複製播放清單</span>
            <input type="text" class="main__copy-input" />
          </div>
        </div>
      </div>

      <Playlist
        :current-list="currentVideo.list"
        :current-index="currentVideo.index"
        @emit-change="changeVideo"
        @emit-remove="removeVideo"
      />
    </div>

    <MenuArea
      :prop-list="list"
      @change-list="changeCurrentList"
      @push-list="addNewList"
      @remove-list="removeList"
    />

    <AddToOther
      :prop-list="list"
      :prop-list-index="currentVideo.listIndex"
      @add-other-list="addOtherVideo"
    />
  </div>
</template>

<script>
import Search from "./components/Search.vue";
import Control from "./components/Control.vue";
import Playlist from "./components/Playlist.vue";
import MenuArea from "./components/MenuArea.vue";
import AddToOther from "./components/AddToOther.vue";

export default {
  name: "App",
  components: {
    Search,
    Control,
    Playlist,
    MenuArea,
    AddToOther,
  },
  data() {
    return {
      player: "",
      videos: {
        isRandom: false,
        isRepeat: false,
        list: [],
      },
      list: [],
      currentVideo: {
        id: "",
        title: "",
        index: 0,
        listIndex: 0,
        list: [],
      },
      activeState: {
        more: false,
        other: false,
      },
      secretTechnique: {
        active: false,
        code: "38384040373937396665",
        temp: "",
        tempInfo: "",
      },
    };
  },
  methods: {
    computedVideo(item) {
      const vm = this;

      vm.videos.list[vm.currentVideo.listIndex].info.push(item);
      vm.saveInfo("play-videos", vm.videos);

      // 沒有預設資料的話，新增影片後設定播放器內容
      if (vm.videos.list[0].info.length == 1) {
        setTimeout(() => {
          vm.setVideo();
        }, 1000);
      }
    },
    isRandom() {
      const vm = this;

      vm.videos.isRandom = !vm.videos.isRandom;
      vm.saveInfo("play-videos", vm.videos);
    },
    isRepeat() {
      const vm = this;

      vm.videos.isRepeat = !vm.videos.isRepeat;
      vm.saveInfo("play-videos", vm.videos);
    },
    playVideo() {
      this.player.playVideo();
    },
    pauseVideo() {
      this.player.pauseVideo();
    },
    stopVideo() {
      this.player.stopVideo();
    },
    prevVideo() {
      const vm = this;
      let _video = vm.currentVideo;

      if (!vm.videos.isRandom) {
        // 用 findIndex 找 array 裡面的值的 index
        let tempIndex = _video.list.findIndex((item) => item.id === _video.id);

        if (tempIndex - 1 !== -1) {
          tempIndex -= 1;
        } else {
          tempIndex = _video.list.length - 1;
        }

        _video.id = _video.list[tempIndex].id;
        _video.title = _video.list[tempIndex].title;
        _video.index = tempIndex;
      } else {
        let tempIndex = Math.floor(Math.random() * _video.list.length);
        _video.id = _video.list[tempIndex].id;
        _video.title = _video.list[tempIndex].title;
        _video.index = tempIndex;
      }
      vm.loadVideo(_video.id);
    },
    nextVideo() {
      const vm = this;
      let _video = vm.currentVideo;

      if (!vm.videos.isRandom) {
        let tempIndex = _video.list.findIndex((item) => item.id === _video.id);

        if (tempIndex + 1 === _video.list.length) {
          tempIndex = 0;
        } else {
          tempIndex += 1;
        }

        _video.id = _video.list[tempIndex].id;
        _video.title = _video.list[tempIndex].title;
        _video.index = tempIndex;
      } else {
        let tempIndex = Math.floor(Math.random() * _video.list.length);
        _video.id = _video.list[tempIndex].id;
        _video.title = _video.list[tempIndex].title;
        _video.index = tempIndex;
      }

      vm.loadVideo(_video.id);
    },
    loadVideo(changeId) {
      this.player.loadVideoById({
        videoId: changeId,
      });
    },
    setVideo() {
      this.player = new window.YT.Player("player", {
        height: "100%",
        width: "100%",
        videoId: this.currentVideo.id,
        playerVars: {
          id_load_policy: 3, // 不顯示影片註解
          rel: 0, // 不顯示相關影片
        },
        events: {
          // onReady: this.playVideo,
          onStateChange: this.onStateChange,
        },
      });
    },
    onStateChange(event) {
      const vm = this;

      // event.data 回傳現在影片的狀態 -1 尚未開始, 0 結束, 1 正在播放, 2 暫停
      if (event.data === 0 && vm.videos.isRepeat) {
        vm.player.playVideo();
      } else if (event.data === 0) {
        vm.nextVideo();
      }
    },
    changeVideo(id, index) {
      this.currentVideo.id = id;
      this.currentVideo.index = index;
      this.currentVideo.title = this.currentVideo.list[index].title; // 修改當前標題文字

      this.player.loadVideoById({
        videoId: id,
      });
    },
    removeVideo(index) {
      const vm = this;

      vm.videos.list[vm.currentVideo.listIndex].info.splice(index, 1);
      vm.saveInfo("play-videos", vm.videos);
    },
    copyInfo() {
      const vm = this;
      let _temp = document.querySelector(".main__copy-input");

      _temp.value = JSON.stringify(vm.videos);
      _temp.focus();
      _temp.select();
      document.execCommand("copy");
      _temp.value = "";
    },
    getInfo() {
      const vm = this;
      vm.saveInfo("play-videos", JSON.parse(vm.secretTechnique.tempInfo));

      vm.secretTechnique.tempInfo = "";
    },
    // menu methods
    changeCurrentList(index) {
      const vm = this;

      vm.currentVideo.list = vm.videos.list[index].info;
      vm.currentVideo.listIndex = index; // 紀錄現在 list 的位置，用在 computedVideo
    },
    addNewList(title) {
      const vm = this;

      vm.list.push(title);
      vm.videos.list.push({ title: title, info: [] });
      vm.saveInfo("play-videos", vm.videos);
    },
    removeList(index) {
      const vm = this;

      vm.list.splice(index, 1);
      vm.videos.list.splice(index, 1);
      vm.saveInfo("play-videos", vm.videos);
    },
    addOtherVideo(item) {
      const vm = this;

      vm.videos.list[item.listIndex].info.push(item.data);
      vm.saveInfo("play-videos", vm.videos);
    },
    localInfo(storage) {
      return JSON.parse(localStorage.getItem(storage));
    },
    saveInfo(storage, item) {
      return localStorage.setItem(storage, JSON.stringify(item));
    },
  },
  computed: {
    currentTitle() {
      return this.currentVideo.title;
    },
  },
  mounted() {
    const vm = this;
    let _video = vm.currentVideo;

    let tag = document.createElement("script");
    tag.src = "https://www.youtube.com/iframe_api";

    let firstScriptTag = document.getElementsByTagName("script")[0];
    firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);

    if (localStorage.getItem("play-videos")) {
      vm.videos = vm.localInfo("play-videos");
    }

    if (vm.videos.list.length !== 0) {
      _video.index = 0;
      _video.list = vm.videos.list[0].info;
      _video.title = _video.list[0].title;
      _video.id = _video.list[0].id;
    } else {
      const initVideo = [
        {
          title: "Custom list",
          info: [
            {
              id: "DA5NLKYDxfQ",
              channelTitle: "TheMurkyCrows",
              imgSrc: "https://i.ytimg.com/vi/DA5NLKYDxfQ/default.jpg",
              title: "昏鴉 - 王國三部曲之壹 《萬中選一的青年》Lyrics Video",
              duration: "PT4M30S",
              addOtherStatus: false,
            },
            {
              id: "YRhkduoNlzA",
              channelTitle: "TheMurkyCrows",
              imgSrc: "https://i.ytimg.com/vi/YRhkduoNlzA/default.jpg",
              title: "昏鴉 - 王國三部曲之貳 《為了已死去的王子》Lyrics Video",
              duration: "PT5M12S",
              addOtherStatus: false,
            },
            {
              id: "zmsnipltJTM",
              channelTitle: "TheMurkyCrows",
              imgSrc: "https://i.ytimg.com/vi/zmsnipltJTM/default.jpg",
              title: "昏鴉 - 王國三部曲之參 《願你的王國榮耀》Lyrics Video",
              duration: "PT5M24S",
              addOtherStatus: false,
            },
          ],
        },
      ];

      vm.videos.list = initVideo;

      _video.index = 0;
      _video.list = vm.videos.list[0].info;
      _video.title = _video.list[0].title;
      _video.id = _video.list[0].id;

      vm.saveInfo("play-videos", vm.videos);
    }

    vm.videos.list.forEach((item) => {
      vm.list.push(item.title);
    });

    window.addEventListener("keydown", function (e) {
      if (vm.secretTechnique.active == false) {
        switch (e.keyCode) {
          case 37:
            vm.secretTechnique.temp += e.keyCode;
            break;

          case 38:
            vm.secretTechnique.temp += e.keyCode;
            break;

          case 39:
            vm.secretTechnique.temp += e.keyCode;
            break;

          case 40:
            vm.secretTechnique.temp += e.keyCode;
            break;

          case 65:
            vm.secretTechnique.temp += e.keyCode;
            break;

          case 66:
            vm.secretTechnique.temp += e.keyCode;
            break;

          default:
            vm.secretTechnique.temp = "";
        }

        if (vm.secretTechnique.temp == vm.secretTechnique.code) {
          vm.secretTechnique.active = true;
        }
      }
    });
  },
  created() {
    setTimeout(() => {
      this.setVideo();
    }, 1500);
  },
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@100;300;400&family=Roboto&family=Noto+Serif+TC&display=swap");
@import url("https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css");

@import "@/scss/_var.scss";
@import "@/scss/main.scss";
</style>
