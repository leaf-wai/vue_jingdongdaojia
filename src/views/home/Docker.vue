<template>
  <div class="docker">
    <div
      class="docker__item"
      :class="{ docker__item: true, 'docker__item--active': index === currentIndex }"
      v-for="(item, index) in dockerList"
      :key="item.icon"
    >
      <router-link :to="item.to">
        <div class="iconfont" v-html="item.icon" v-show="!(index===currentIndex)"></div>
        <div class="iconfont" v-html="item.icon_active" v-show="index===currentIndex"></div>
        <div class="docker__title">{{ item.text }}</div>
      </router-link>
    </div>
  </div>
</template>
<script>
export default {
  name: 'Docker',
  props: ['currentIndex'],
  setup () {
    const dockerList = [
      {
        icon: '&#xe6b8;',
        icon_active: '&#xe6bb;',
        text: '首页',
        to: { name: 'Home' }
      },
      {
        icon: '&#xe6af;',
        icon_active: '&#xe6b9;',
        text: '购物车',
        to: { name: 'CartList' }
      },
      {
        icon: '&#xe714;',
        icon_active: '&#xe713;',
        text: '订单',
        to: { name: 'OrderList' }
      },
      {
        icon: '&#xe78b;',
        icon_active: '&#xe78c;',
        text: '我的',
        to: { name: 'Home' }
      }
    ]
    return { dockerList }
  }
}
</script>
<style lang="scss" scoped>
@import '../../style/viriables.scss';
.docker {
  display: flex;
  box-sizing: border-box;
  position: absolute;
  padding: 0 0.18rem;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 0.49rem;
  border-top: 0.01rem solid $content-bgColor;
  color: $content-fontcolor;
  &__item {
    flex: 1;
    text-align: center;
    .iconfont {
      margin: 0.07rem 0 0.02rem 0;
      font-size: 0.22rem;
    }
    a {
      text-decoration: none;
      color: $content-fontcolor;
    }
    &--active {
      a {
        color: #1fa4fc;
      }
    }
  }
  &__title {
    font-size: 0.2rem;
    transform: scale(0.5, 0.5);
    transform-origin: center top;
  }
}
</style>
