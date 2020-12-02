<template>
  <div class="control">
    <div class="control__title">
      {{ returnTitle }}
    </div>

    <div class="control__box">
      <div title="Prev" class="control__prev" @click="prevVideo">
        <i class="fa fa-step-backward" aria-hidden="true"></i>
      </div>

      <div
        title="Play"
        class="control__play"
        v-if="playing == false"
        @click="playVideo"
      >
        <i class="fa fa-play" aria-hidden="true"></i>
      </div>

      <div
        title="Pause"
        class="control__pause"
        v-if="playing == true"
        @click="pauseVideo"
      >
        <i class="fa fa-pause" aria-hidden="true"></i>
      </div>

      <div title="Next" class="control__next" @click="nextVideo">
        <i class="fa fa-step-forward" aria-hidden="true"></i>
      </div>

      <div
        title="Random"
        class="control__random"
        :class="{ isRandom: returnRandom }"
        @click="isRandom"
      >
        <i class="fa fa-random" aria-hidden="true"></i>
      </div>

      <div
        title="Repeat"
        class="control__repeat"
        :class="{ isRepeat: returnRepeat }"
        @click="isRepeat"
      >
        <i class="fa fa-repeat" aria-hidden="true"></i>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["title", "random", "repeat"],
  data() {
    return {
      playing: false,
    };
  },
  computed: {
    returnTitle() {
      return this.title;
    },
    returnRandom() {
      return this.random;
    },
    returnRepeat() {
      return this.repeat;
    },
  },
  methods: {
    playVideo() {
      this.playing = !this.playing;
      this.$emit("emit-play");
    },
    pauseVideo() {
      this.playing = !this.playing;
      this.$emit("emit-pause");
    },
    stopVideo() {
      this.$emit("emit-stop");
    },
    prevVideo() {
      this.$emit("emit-prev");
    },
    nextVideo() {
      this.$emit("emit-next");
    },
    isRandom() {
      this.$emit("emit-random");
    },
    isRepeat() {
      this.$emit("emit-repeat");
    },
    getLocalInfo(el) {
      return JSON.parse(localStorage.getItem(el));
    },
    saveLocalInfo(el) {
      const vm = this;
      return localStorage.setItem(el, JSON.stringify(vm.videoInfo));
    },
  },
};
</script>

<style lang="scss">
@import "@/scss/_var.scss";

.control {
  position: fixed;
  bottom: 0;
  left: 50%;

  display: flex;
  align-items: center;
  flex-direction: column;
  gap: $gap;

  width: 100%;
  max-width: 740px;

  background-color: #fff;
  box-shadow: 0 3px 10px rgba(#000, 0.1);

  transition: all 0.3s ease-in-out;
  transform: translate(-50%, 0);

  z-index: 1;

  padding: {
    top: $gap;
    left: $gap;
    right: $gap;
    bottom: $gap;
  }

  @media (min-width: 992px) {
    transform: translate(-50%, calc(#{$gap} + 40px));

    padding: {
      left: calc(#{$gap} * 3);
      right: calc(#{$gap} * 3);
    }

    &:hover {
      transform: translate(-50%, 0);

      &::before {
        transform: rotate(225deg);
      }
    }

    &::before {
      content: "";
      position: absolute;
      left: 50%;
      bottom: calc(100% + #{$gap});

      width: 10px;
      height: 10px;

      transition: all 0.3s ease;
      transform: rotate(45deg);

      border: {
        top: solid 3px $color-two;
        left: solid 3px $color-one;
      }

      margin: {
        left: -5px;
      }
    }
  }

  @media (max-width: 991px) {
    width: calc(100% - 6%);
    box-shadow: 0 0 10px rgba(#000, 0.15);

    padding: {
      top: $gap;
    }
  }

  &__box {
    display: flex;
    gap: $gap-small;

    > div {
      flex-shrink: 0;

      display: flex;
      align-items: center;
      justify-content: center;

      width: 40px;
      height: 40px;

      font-size: 12px;
      color: #cdcdcd;

      border-radius: 50%;
      box-shadow: 3px 3px 6px #dedede;
      transition: all 0.3s;
      cursor: pointer;

      transition: all 0.3 ease-in-out;

      @media (min-width: 992px) {
        &:hover {
          background-color: $color-one;
          box-shadow: 0 0 10px rgba($color-one, 0.3);
          color: #fff;
        }
      }

      @media (max-width: 991px) {
        width: 35px;
        height: 35px;
      }
    }
  }

  &__title {
    flex-grow: 1;

    font-size: 22px;
    text-align: center;

    @media (max-width: 991px) {
      font-size: 18px;
    }
  }

  &__random {
    &.isRandom {
      border-color: $color-two;
      background-color: $color-two;
      color: #fff;
    }
  }

  &__repeat {
    &.isRepeat {
      border-color: $color-two;
      background-color: $color-two;
      color: #fff;
    }
  }
}
</style>