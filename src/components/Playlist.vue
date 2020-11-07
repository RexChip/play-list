<template>
  <div class="main__list">
    <div class="total__top">
      <div class="total__list">
        <div
          class="total__item"
          v-for="(item, index) in computedList"
          :key="index"
          @click="changeVideo(item.id, index)"
          @mouseleave="checkStatus(index)"
        >
          <div class="total__img">
            <img :src="item.imgSrc" />
            <span class="total__duration">
              {{ durationToSeconds("changeToTime", item.duration) }}
            </span>
          </div>

          <div class="total__info">
            <span class="total__title">
              {{ item.title }}
            </span>
            <span class="total__channel">{{ item.channelTitle }}</span>
          </div>

          <div class="total-modify">
            <div class="total-modify__more" @click.stop="changeStatus(index)">
              <i class="fa fa-ellipsis-v" aria-hidden="true"></i>

              <div
                class="total-modify__box"
                :class="{ 'is-active': item.addOtherStatus }"
                @click="addOtherVideo(index)"
              >
                <div class="total-modify__item">
                  新增到其他列表
                </div>
              </div>
            </div>

            <div class="total-modify__remove" @click.stop="removeVideo(index)">
              <i class="fa fa-remove" aria-hidden="true"></i>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="total__bottom">
      總共 {{ computedList.length }} 首，共 {{ totalVideoTime() }} 分
    </div>
  </div>
</template>

<script>
export default {
  props: ["currentList", "currentIndex"],
  data() {
    return {
      addDatas: {
        video: {},
        active: false
      }
    };
  },
  computed: {
    computedList() {
      return this.currentList;
    }
  },
  methods: {
    durationToSeconds(key = "changeToTime", duration) {
      let time = [];
      let match = duration.match(/PT(\d+H)?(\d+M)?(\d+S)?/);

      match = match.slice(1).map(function(x) {
        if (x != null) {
          return x.replace(/\D/, "");
        }
      });

      let hours = parseInt(match[0]) || 0;
      let minutes = parseInt(match[1]) || 0;
      let seconds = parseInt(match[2]) - 1 || 0;
      // seconds -= 1;

      switch (key) {
        case "changeToTime":
          hours === 0
            ? time.push(minutes, seconds)
            : time.push(hours, minutes, seconds);

          return time.join(":");
        case "seconds":
          return hours * 3600 + minutes * 60 + seconds;
      }
    },
    totalVideoTime() {
      const vm = this;
      let totalDuration = 0;

      vm.computedList.forEach(item => {
        let tempDuration = vm.durationToSeconds("seconds", item.duration);
        totalDuration += tempDuration;
      });

      return Math.ceil(totalDuration / 60);
    },
    changeStatus(index) {
      this.computedList[index].addOtherStatus = !this.computedList[index]
        .addOtherStatus;
    },
    checkStatus(index) {
      if (this.computedList[index].addOtherStatus === true) {
        this.computedList[index].addOtherStatus = false;
      }
    },
    addOtherVideo(index) {
      this.addDatas.active = true;
      this.addDatas.video = this.currentList[index];

      // this.$emit("emit-add", this.addDatas);
      this.$bus.$emit("video:other", this.addDatas);
    },
    removeVideo(index) {
      this.$emit("emit-remove", index);
    },
    changeVideo(id, index) {
      this.$emit("emit-change", id, index);
    }
  }
  // beforeDestroy() {
  //   this.$bus.$emit("video:other", this.addDatas);
  // }
};
</script>

<style lang="scss">
@import "@/scss/_var.scss";

.total {
  &__top {
    position: relative;
    width: 100%;

    @media (min-width: 992px) {
      &::before {
        content: "";
        display: block;
        width: 100%;
        padding-top: 131.25%;
      }
    }
  }

  &__list {
    position: relative;

    width: 100%;
    height: 100%;

    overflow-y: auto;

    @media (min-width: 992px) {
      position: absolute;
      top: 0;
      left: 0;
    }

    @media (max-width: 991px) {
      max-height: 50vh;
    }

    &::-webkit-scrollbar {
      width: 8px;
      height: 8px;
    }
    &::-webkit-scrollbar-track {
      background-color: rgba($color-one, 0.2);
    }
    &::-webkit-scrollbar-thumb {
      background-color: $color-one;
    }
    &::-webkit-scrollbar-corner {
      opacity: 0;
    }
  }

  &__item {
    position: relative;

    display: flex;
    align-items: flex-start;

    cursor: pointer;

    &:hover {
      .total-modify {
        pointer-events: visible;
        opacity: 1;
      }

      .total__title {
        color: $color-one;
      }
    }

    &:not(:last-child) {
      margin: {
        bottom: 8px;
      }
    }
  }

  &__img {
    flex-shrink: 0;

    position: relative;
    cursor: pointer;

    img {
      display: block;

      @media (max-width: 1280px) {
        width: 100px;
      }
    }
  }
  &__duration {
    position: absolute;
    bottom: 5px;
    right: 5px;
    font-size: 12px;
    font-weight: 600;
    background-color: rgba(#000, 0.4);
    color: #fff;

    padding: {
      top: 5px;
      left: 8px;
      right: 8px;
      bottom: 5px;
    }
  }

  &__info {
    display: flex;
    flex-direction: column;
    padding: {
      left: 8px;
      right: 32px;
    }

    @media (min-width: 1281px) {
      max-width: 300px;
    }
  }

  &__title {
    display: -webkit-box;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;
    overflow: hidden;
    text-overflow: ellipsis;

    font-size: 16px;
    line-height: 1.4;
    letter-spacing: 0.02em;
    cursor: pointer;
    transition: all 0.3s;

    margin: {
      bottom: 8px;
    }

    @media (max-width: 1280px) {
      font-size: 14px;
    }

    &:hover {
      color: $color-one;
    }
  }

  &__channel {
    font-size: 14px;
    color: rgba(#000, 0.6);
  }

  &-modify {
    position: absolute;
    right: 4px;
    top: 0;

    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    transition: all 0.3s;

    pointer-events: none;
    opacity: 0;

    &__more {
      position: relative;

      margin: {
        bottom: 4px;
      }
    }

    &__box {
      position: absolute;
      top: calc(100% + 5px);
      right: 0;

      display: none;
      font-size: 14px;

      white-space: pre;

      background-color: #fff;
      box-shadow: 0 0 10px rgba(#000, 0.15);
      transition: all 0.3s;
      cursor: pointer;

      padding: {
        top: 10px;
        left: 15px;
        right: 15px;
        bottom: 10px;
      }

      &:hover {
        background-color: $color-one;
        color: #fff;
      }

      &.is-active {
        display: block;
      }
    }

    &__more,
    &__remove {
      display: flex;
      align-items: center;
      justify-content: center;
      width: 24px;
      height: 24px;
      font-size: 20px;
      color: rgba(#000, 0.4);
      cursor: pointer;

      i:hover {
        color: #d45729;
      }
    }
  }

  &__bottom {
    font-size: 14px;
    letter-spacing: 0.5px;
    line-height: 40px;
    text-align: right;

    margin: {
      top: $gap-small;
    }
  }
}
</style>
