<template>
  <div class="menu" :class="{ 'menu--active': menuStatus }">
    <div class="menu-mask" @click="menuStatus = !menuStatus"></div>

    <div class="menu-button" @click.stop="menuStatus = !menuStatus">
      <i class="fa fa-bars" aria-hidden="true"></i>
    </div>

    <div class="menu-box" :class="{ 'is-active': menuStatus }">
      <div class="menu-box__top">
        <h2 class="menu-box__title">播放清單</h2>
        <input
          type="text"
          placeholder="輸入清單名稱"
          class="menu-box__new"
          :class="{ 'is-active': activeInput }"
          v-model="newListName"
          @keyup.enter="addList"
        />
        <div class="menu-box__add">
          <template v-if="activeInput == false">
            <p @click="activeInput = !activeInput">新增播放清單</p>
          </template>
          <template v-else>
            <p @click="addList">新增清單</p>
          </template>
        </div>
      </div>

      <div class="menu-list">
        <div
          class="menu-list__item"
          v-for="(item, index) in datas"
          :key="index"
          @click="changeList(index)"
        >
          <p>{{ item }}</p>
          <div class="menu-list__remove" @click.stop="removeList(index)">
            <i class="fa fa-remove" aria-hidden="true"></i>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["propList"],
  data() {
    return {
      datas: this.propList,
      newListName: "",
      activeInput: false,
      menuStatus: false
    };
  },
  methods: {
    changeList(index) {
      this.$emit("change-list", index);
    },
    addList() {
      const vm = this;

      vm.activeInput = !vm.activeInput;
      if (vm.newListName == "") return false;

      vm.$emit("push-list", vm.newListName);
      vm.newListName = "";
    },
    removeList(index) {
      this.$emit("remove-list", index);
    }
  }
};
</script>

<style lang="scss">
@import "@/scss/_var.scss";

.menu {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 1;

  &--active {
    pointer-events: auto;

    .menu-mask {
      opacity: 1;
    }
  }

  &-mask {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(#000, 0.3);
    transition: all 0.3s;
    opacity: 0;
  }

  &-button {
    position: absolute;
    top: 40px;
    left: 40px;

    display: flex;
    align-items: center;
    justify-content: center;
    width: 40px;
    height: 40px;

    font-size: 18px;
    color: #555;
    cursor: pointer;

    transition: all 0.3s;
    pointer-events: auto;

    background-color: #fff;
    box-shadow: 0 0 10px rgba(black, 0.15);
    border-radius: 50%;

    @media (max-width: 991px) {
      top: 20px;
      left: 20px;
    }

    &:hover {
      color: $color-one;
    }
  }

  &-box {
    position: absolute;
    top: 0;
    left: 0;
    width: 360px;
    height: 100%;
    background-color: #fff;
    color: rgba(#000, 0.6);

    transform: translateX(-100%);
    transition: all 0.6s;

    padding: {
      top: 40px;
      left: 20px;
      right: 20px;
      bottom: 40px;
    }

    &.is-active {
      transform: translateX(0);
    }

    &__top {
      position: relative;
    }

    &__title {
      font-family: "Noto Serif TC", serif;
      font-size: 16px;
      font-weight: 400;
      line-height: 40px;
      color: rgba(#000, 0.3);

      margin: {
        bottom: 8px;
      }
    }

    &__add {
      position: absolute;
      top: 0;
      right: 0;

      p {
        font-size: 14px;
        font-weight: 300;
        line-height: 40px;
        background-color: $color-one;
        color: #fff;
        cursor: pointer;

        padding: {
          left: 20px;
          right: 20px;
        }
      }
    }

    &__new {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      font-family: "Roboto", "Noto Sans TC", "微軟正黑體";
      font-size: 16px;
      font-weight: 300;
      letter-spacing: 0.05em;
      line-height: 39px;
      outline: none;
      transition: all 0.3s;
      transform-origin: right;
      transform: scaleX(0);

      border: {
        top: none;
        left: none;
        right: none;
        bottom: solid 1px #56c3b7;
      }

      padding: {
        left: 5px;
        right: 101px;
      }

      &::placeholder {
        font-family: "Roboto", "Noto Sans TC";
        font-size: 14px;
        font-weight: 300;
      }

      &.is-active {
        transform: scaleX(1);
      }
    }
  }

  &-list {
    padding: {
      top: 40px;
    }

    &__item {
      position: relative;
      cursor: pointer;

      &:hover {
        p {
          color: $color-one;
        }
        .menu-list__remove {
          opacity: 1;
        }
      }

      p {
        font-size: 18px;
        letter-spacing: 0.05em;
        line-height: 40px;

        transition: all 0.3s;
      }
    }

    &__remove {
      position: absolute;
      top: 0;
      right: 0;

      width: 40px;
      height: 40px;
      line-height: 40px;
      text-align: center;
      color: #777;

      transition: all 0.3s;
      opacity: 0;

      &:hover {
        color: $color-one;
      }
    }
  }
}
</style>