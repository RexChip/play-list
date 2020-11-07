<template>
  <div class="control">
    <div class="control__title">
      {{ returnTitle }}
    </div>

    <div class="control__box">
      <div title="Prev" class="control__prev" @click="prevVideo">
        <i class="fa fa-step-backward" aria-hidden="true"></i>
      </div>

      <div title="Play" class="control__play" @click="playVideo">
        <i class="fa fa-play" aria-hidden="true"></i>
      </div>

      <div title="Pause" class="control__pause" @click="pauseVideo">
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
  computed: {
    returnTitle() {
      return this.title
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
      this.$emit("emit-play");
    },
    pauseVideo() {
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
    }
  }
};
</script>

<style lang="scss">
@import "@/scss/_var.scss";

.control {
  display: flex;
  gap: $gap-small;

  padding: {
    top: $gap-small;
  }

  @media (max-width: 991px) {
    flex-direction: column;

    padding: {
      top: $gap;
    }
  }

  &__box {
    display: flex;
    gap: $gap-small;
  }

  &__prev,
  &__play,
  &__pause,
  &__next,
  &__random,
  &__repeat {
    flex-shrink: 0;

    display: flex;
    align-items: center;
    justify-content: center;

    width: 40px;
    height: 40px;

    font-size: 12px;
    color: #ababab;

    border-radius: 50%;
    border: solid 2px #ababab;
    transition: all 0.3s;
    cursor: pointer;

    @media (max-width: 991px) {
      width: 30px;
      height: 30px;
    }
  }

  &__title {
    flex-grow: 1;

    font-size: 22px;
    line-height: 1.4;

    @media (max-width: 991px) {
      font-size: 18px;
    }
  }

  &__prev {
    border: solid 2px $color-one;
    color: $color-one;
    &:hover {
      background-color: $color-one;
      color: #fff;
    }
  }

  &__play {
    border: solid 2px $color-one;
    color: $color-one;
    &:hover {
      background-color: $color-one;
      color: #fff;
    }
  }

  &__pause {
    border: solid 2px $color-one;
    color: $color-one;

    &:hover {
      background-color: $color-one;
      color: #fff;
    }
  }

  &__next {
    border: solid 2px $color-one;
    color: $color-one;

    &:hover {
      background-color: $color-one;
      color: #fff;
    }
  }

  &__random {
    &:hover {
      opacity: 0.9;
    }

    &.isRandom {
      border-color: $color-two;
      background-color: $color-two;
      color: #fff;
    }
  }

  &__repeat {
    &:hover {
      opacity: 0.9;
    }

    &.isRepeat {
      border-color: $color-two;
      background-color: $color-two;
      color: #fff;
    }
  }
}
</style>