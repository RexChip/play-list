<template>
  <div class="other" v-if="otherStates">
    <div class="other__mask" @click="states = false"></div>

    <div class="other__list">
      <div class="other__item" v-for="(item, index) in list" :key="index">
        <p v-if="index !== propListIndex" @click="addToOther(index)">
          {{ item }}
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["propList", "propListIndex"],
  data() {
    return {
      list: this.propList,
      emitData: {
        listIndex: 0,
        data: {}
      },
      states: false
    };
  },
  methods: {
    addToOther(index) {
      const vm = this;

      vm.states = false;
      vm.emitData.listIndex = index;
      vm.$emit("add-other-list", vm.emitData);
    }
  },
  computed: {
    otherStates() {
      return this.states && this.propList.length - 1 > 0;
    }
  },
  created() {
    const vm = this;

    vm.$bus.$on("video:other", item => {
      vm.emitData.data = item.video;
      vm.states = item.active;
    });
  },
  beforeDestroy() {
    this.$bus.$off("video:other");
  }
};
</script>

<style lang="scss">
@import "@/scss/_var.scss";

.other {
  position: fixed;
  top: 0;
  left: 0;

  display: flex;
  align-items: center;
  justify-content: center;

  width: 100%;
  height: 100%;

  z-index: 2;

  &__mask {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(black, 0.3);
  }

  &__list {
    position: relative;

    width: 80%;
    max-width: 300px;

    background-color: #fff;
    box-shadow: 0 0 15px rgba(#000, 0.15);

    padding: {
      top: 10px;
      left: 10px;
      right: 10px;
      bottom: 10px;
    }
  }

  p {
    transition: all 0.3s;
    cursor: pointer;

    padding: {
      top: 10px;
      left: 10px;
      right: 10px;
      bottom: 10px;
    }

    &:hover {
      background-color: #eee;
    }
  }
}
</style>