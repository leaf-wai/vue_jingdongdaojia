<template>
  <div class="wrapper">
    <div class="header">
      <div class="header__all"></div>
      <div class="header__title">购物车</div>
      <div class="header__clear" @click="cleanCartAllProducts">
        <span class="header__clear__btn">全部清空</span>
      </div>
    </div>
    <div class="cart">
      <div
        class="cart__shop"
        v-for="(shopItem, index) in cartList"
        :key="index"
      >
        <div class="cart__shop__header">
          <span
            class="cart__shop__header__iconAll iconfont"
            v-html="calculations(index).allChecked ? '&#xe656;' : '&#xe657;'"
            @click="() => setCartItemsChecked(index,calculations(index).allChecked)"
          ></span>
          <router-link :to="`/shop/${index}`">
            <span class="cart__shop__header__title">{{
              shopItem.shopName
            }}</span>
            <span class="cart__shop__header__icon iconfont">&#xe600;</span>
          </router-link>
        </div>

        <div
          class="product__item"
          v-for="item in shopItem.productList"
          :key="item._id"
        >
          <div
            class="product__item__checked iconfont"
            v-html="item.check ? '&#xe656;' : '&#xe657;'"
            @click="
              () => {
                changeCartItemChecked(index, item._id)
              }
            "
          ></div>
          <img class="product__item__img" :src="item.imgUrl" />
          <div class="product__item__detail">
            <h4 class="product__item__title">{{ item.name }}</h4>
            <p class="product__item__price">
              <span class="product__item__yen">&yen;</span>
              {{ item.price }}
              <span class="product__item__origin"
                >&yen;{{ item.oldPrice }}</span
              >
            </p>
          </div>
          <div class="product__number">
            <span
              class="product__number__minus iconfont"
              @click="
                () => {
                  changeCartItemInfo(index, item._id, item, -1)
                }
              "
              >&#xe781;</span
            >
            {{ item.count }}
            <span
              class="product__number__plus iconfont"
              @click="
                () => {
                  changeCartItemInfo(index, item._id, item, 1)
                }
              "
              >&#xe7ba;</span
            >
          </div>
        </div>
        <div class="check">
          <div class="check__icon">
            <img
              src="http://www.dell-lee.com/imgs/vue3/basket.png"
              class="check__icon__img"
            />
            <div class="check__icon__tag">{{ calculations(index).total }}</div>
          </div>
          <div class="check__info">
            总计：
            <span class="check__info__price"
              >&yen; {{ calculations(index).price }}</span
            >
          </div>
          <div class="check__btn" v-show="calculations(index).total > 0 && calculations(index).price > 0">
            <router-link :to="{ path: `/orderConfirmation/${index}` }"
              >去结算</router-link
            >
          </div>
        </div>
      </div>
    </div>
  </div>
  <docker :currentIndex="1" />
</template>

<script>
import { computed } from 'vue'
import { useStore } from 'vuex'
import Docker from '../home/Docker.vue'
const useCartListEffect = () => {
  const store = useStore()
  const cartList = computed(() => {
    const list = store.state.cartList
    for (const i in list) {
      const notEmptyProductList = {}
      for (const j in list[i].productList) {
        const product = list[i].productList[j]
        if (product.count > 0) {
          notEmptyProductList[j] = product
        }
      }
      list[i].productList = notEmptyProductList
    }
    return list
  })
  const calculations = computed(() => {
    return (shopId) => {
      const cList = store.state.cartList
      const productList = cList[shopId]?.productList
      const result = { total: 0, price: 0, allChecked: true }
      if (productList) {
        for (const i in productList) {
          const product = productList[i]
          result.total += product.count
          if (product.check) {
            result.price += product.count * product.price
          }
          if (product.count > 0 && !product.check) {
            result.allChecked = false
          }
        }
      }
      result.price = result.price.toFixed(2)
      return result
    }
  })
  const changeCartItemInfo = (shopId, productId, productInfo, num) => {
    store.commit('changeCartItemInfo', {
      shopId,
      productId,
      productInfo,
      num
    })
  }
  const changeCartItemChecked = (shopId, productId) => {
    store.commit('changeCartItemChecked', { shopId, productId })
  }
  const setCartItemsChecked = (shopId, allChecked) => {
    const checked = !allChecked
    store.commit('setCartItemsChecked', { shopId, checked })
  }
  const cleanCartAllProducts = () => {
    store.commit('cleanCartAllProducts')
  }
  return { cartList, calculations, changeCartItemChecked, changeCartItemInfo, setCartItemsChecked, cleanCartAllProducts }
}
export default {
  name: 'CartList',
  components: {
    Docker
  },
  setup () {
    const { cartList, calculations, changeCartItemChecked, changeCartItemInfo, setCartItemsChecked, cleanCartAllProducts } = useCartListEffect()
    return {
      cleanCartAllProducts,
      setCartItemsChecked,
      changeCartItemInfo,
      changeCartItemChecked,
      calculations,
      cartList,
      Docker
    }
  }
}
</script>

<style lang="scss" scoped>
@import '../../style/viriables.scss';
@import '../../style/mixins.scss';
a {
  text-decoration: none;
}
.wrapper {
  background: #f8f8f8;
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0.5rem;
  right: 0;
}
.header {
  z-index: 1;
  display: flex;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  line-height: 0.44rem;
  background: $bgColor;
  border-bottom: 0.01rem solid $content-bgColor;
  font-size: 0.14rem;
  color: $content-fontcolor;
  &__all {
    width: 0.64rem;
    margin-left: 0.18rem;
  }
  &__title {
    flex: 3;
    text-align: center;
    font-size: 0.18rem;
    color: $content-fontcolor;
  }
  &__clear {
    flex: 1;
    margin-right: 0.16rem;
    text-align: right;
    &__btn {
      display: inline-block;
    }
  }
}
.cart {
  position: absolute;
  left: 0%;
  right: 0%;
  top: 0.45rem;
  bottom: 0%;
  overflow-y: scroll;
  &__shop {
    position: relative;
    flex: 1;
    background: $bgColor;
    margin: 0.1rem 0.16rem;
    border-radius: 0.05rem;
    &__header {
      display: flex;
      position: relative;
      line-height: 0.2rem;
      padding: 0.12rem 0;
      margin: 0 0.16rem;
      border-bottom: 0.01rem solid $content-bgColor;
      &__iconAll {
        align-self: center;
        color: $btn-bgColor;
        margin-right: 0.1rem;
        font-size: 0.2rem;
      }
      &__title {
        font-size: 0.16rem;
        font-weight: 600;
        color: $content-fontcolor;
      }
      &__icon {
        font-size: 0.2rem;
        position: relative;
        color: $content-fontcolor;
      }
    }
  }
}
.product {
  &__item {
    position: relative;
    display: flex;
    padding: 0.12rem 0;
    margin: 0 0.16rem;
    border-bottom: 0.01rem solid $content-bgColor;
    &__checked {
      line-height: 0.5rem;
      margin-right: 0.2rem;
      color: $btn-bgColor;
      font-size: 0.2rem;
    }
    &__detail {
      overflow: hidden;
    }
    &__img {
      width: 0.46rem;
      height: 0.46rem;
      margin-right: 0.16rem;
    }
    &__title {
      margin: 0;
      line-height: 0.2rem;
      font-size: 0.14rem;
      color: $content-fontcolor;
      @include ellipsis;
    }
    &__price {
      margin: 0.06rem 0 0 0;
      line-height: 0.2rem;
      font-size: 0.14rem;
      color: $hightlight-fontColor;
    }
    &__yen {
      font-size: 0.12rem;
    }
    &__origin {
      margin-left: 0.06rem;
      line-height: 0.2rem;
      font-size: 0.12rem;
      color: $light-fontColor;
      text-decoration: line-through;
    }
    .product__number {
      position: absolute;
      right: 0;
      bottom: 0.26rem;
      line-height: 0.18rem;
      &__minus {
        position: relative;
        top: 0.02rem;
        color: $medium-fontColor;
        margin-right: 0.05rem;
      }
      &__plus {
        position: relative;
        top: 0.02rem;
        color: $btn-bgColor;
        margin-left: 0.05rem;
      }
    }
  }
}
.check {
  display: flex;
  height: 0.49rem;
  &__icon {
    position: relative;
    width: 0.84rem;
    &__img {
      display: block;
      width: 0.28rem;
      height: 0.26rem;
      margin: 0.12rem auto;
    }
    &__tag {
      position: absolute;
      left: 0.4rem;
      top: 0.04rem;
      padding: 0 0.04rem;
      min-width: 0.2rem;
      height: 0.2rem;
      line-height: 0.2rem;
      background-color: $hightlight-fontColor;
      border-radius: 0.1rem;
      font-size: 0.12rem;
      text-align: center;
      color: $bgColor;
      transform: scale(0.5);
      transform-origin: left center;
    }
  }
  &__info {
    flex: 1;
    color: $content-fontcolor;
    font-size: 0.12rem;
    &__price {
      line-height: 0.49rem;
      color: $hightlight-fontColor;
      font-size: 0.14rem;
    }
  }
  &__btn {
    width: 0.9rem;
    line-height: 0.34rem;
    margin: 0.08rem 0.16rem;
    background: #4fb0f9;
    border-radius: 0.25rem;
    color: $bgColor;
    text-align: center;
    font-size: 0.14rem;
    a {
      color: $bgColor;
      text-decoration: none;
    }
  }
}
</style>
