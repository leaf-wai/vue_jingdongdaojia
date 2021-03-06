<template>
  <div class="wrapper">
    <static-part />
    <nearby />
  </div>
  <docker :currentIndex="0" />
</template>
<script>
import Docker from './Docker.vue'
import Nearby from './Nearby.vue'
import StaticPart from './StaticPart'
export default {
  name: 'home',
  components: {
    StaticPart,
    Nearby,
    Docker
  }
}
</script>
<style lang="scss">
@import '../../style/viriables.scss';
@import '../../style/mixins.scss';
.wrapper {
  overflow-y: auto;
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0.5rem;
  right: 0;
  padding: 0 0.18rem 0.1rem 0.18rem;
}
</style>
