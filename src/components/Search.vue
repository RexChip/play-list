<template>
  <div class="search">
    <div class="search__box">
      <input
        type="text"
        placeholder="請輸入 Youtube 影片網址"
        v-model="searchInput"
        @keyup.enter="addVideo"
      />
    </div>

    <div class="search__btn" @click="addVideo">ADD</div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      searchInput: "",
      temp: {},
    };
  },
  methods: {
    addVideo() {
      const vm = this;
      if (vm.searchInput === "") return false;
      if (vm.searchInput.indexOf("https://www.youtube.com/watch?v=") === -1) {
        alert(
          `請輸入 Youtube 影片網址，\ne.g., https://www.youtube.com/watch?v=jNQXAC9IVRw`
        );

        vm.searchInput = "";
        return false;
      }

      let match = vm.searchInput.match(/v=([\w-]*)/).slice(1);
      if (match !== null) {
        vm.searchVideo(match.toString().replace("v=", ""));
        vm.searchInput = "";
      }
    },
    searchVideo(val) {
      const vm = this;
      const newVideoSrc = `https://www.googleapis.com/youtube/v3/videos?id=${val}&key=AIzaSyCgylx6xrsalOftXeN2XtxeQHZ3j2azAZA&part=snippet,contentDetails`;

      axios
        .get(newVideoSrc)
        .then((res) => {
          if (res.data.items === undefined || res.data.items.length === 0) {
            alert(`請輸入正確的 Youtube 影片網址`);
            return false;
          }
          const item = res.data.items[0];
          // if (item.id == undefined) return alert
          vm.temp = {
            id: item.id,
            channelTitle: item.snippet.channelTitle,
            title: item.snippet.title,
            duration: item.contentDetails.duration,
            imgSrc: item.snippet.thumbnails.default.url,
            addOtherStatus: false,
          };
        })
        .finally(() => {
          if (vm.temp.id === undefined) return false;

          vm.$emit("emit-video", vm.temp);
          vm.temp = {};
        });
    },
  },
};
</script>


<style lang="scss">
@import "@/scss/_var.scss";

.search {
  position: relative;
  text-align: center;

  max-width: 560px;

  margin: {
    left: auto;
    right: auto;
  }

  padding: {
    bottom: 30px;
  }

  // @media (min-width: 1381px) {
  //   padding: {
  //     right: $gap;
  //   }
  // }

  @media (max-width: 991px) {
    padding: {
      bottom: $gap;
    }
  }

  @media (max-width: 767px) {
    max-width: 100%;

    padding: {
      left: 60px;
    }
  }

  input {
    width: 100%;

    font-size: 16px;
    line-height: 28px;
    outline: none;

    border: solid 1px #eee;

    padding: {
      top: 5px;
      left: 10px;
      right: 65px;
      bottom: 5px;
    }

    @media (max-width: 991px) {
      font-size: 14px;
    }

    &::placeholder {
      font-family: "Roboto", "Noto Sans TC";
      font-size: 14px;
      font-weight: 300;
      color: #999;
    }
  }

  &__btn {
    position: absolute;
    top: 0;
    right: 0;
    width: 60px;
    line-height: 40px;
    background-color: $color-one;
    color: #fff;
    text-align: center;
    cursor: pointer;
    transition: all 0.3s;

    // @media (min-width: 1381px) {
    //     right: $gap;
    // }

    @media (min-width: 992px) {
      &:hover {
        background-color: #d45729;
      }
    }

    @media (max-width: 991px) {
      width: 50px;
      font-size: 14px;
    }
  }
}
</style>
