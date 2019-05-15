<template>
  <div
    class="tab-box"
    :class="{'with-animation': !paging}"
    :style="{'transform': 'translateX(' + transformX + 'px)'}">
    <div
      ref="tab"
      class="tab"
      v-for="item in items"
      v-bind:key="item.id">
      {{item.name}}
    </div>  
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
  props: ['items', 'page', 'transformXPercent'],
  data: function() {
    return {
      paging: false,
      itemsWidth: [],
      browserWidth: window.innerWidth,
      transformX: 0
    }
  },
  watch: {
    page : function () {
      this.paging = false;
      this.updateTransformX();
    },
    transformXPercent: function () {
      if (this.transformXPercent === null){
        // paging中にnullが来た場合はキャンセル処理
        if (this.paging) {
          this.paging = false;
          this.updateTransformX();
        }
        return;
      }
      this.paging = true;
      this.updateTransformX();
    }
  },
  methods: {
    updateTransformX: function () {
      var transformX = 0;
      // 中央値からどれだけ左右に移動したかのpercent
      const percent = 0.5 + this.transformXPercent;

      // pageまでのタブのサイズをすべて足し合わせる
      for (var i = 0; i < this.itemsWidth.length; i++) {
        if (i === this.page) {
          // 対象タブに移動percentをかけている
          transformX += this.itemsWidth[i] * percent;
          break;
        }
        transformX += this.itemsWidth[i];
      }

      this.transformX = (this.browserWidth / 2) - transformX;
    },
    $_tabCrousel_nextTick: function () {
      if (this.itemsWidth.length === this.items.length) return;
      if (this.itemsWidth.length != this.items.length) this.itemsWidth = new Array(this.items.length);
      this.$refs.tab.forEach((element, index) => {
        this.itemsWidth[index] = element.clientWidth;
      });
      this.updateTransformX();
    }
  },
  updated: function () {
    this.$nextTick(this.$_tabCrousel_nextTick);
  },
  mounted: function () {
    this.$nextTick(this.$_tabCrousel_nextTick);
  }
});
</script>


<style scoped>
  .tab-box {
    width: 100%;
    height: 30px;
    display: flex;
    align-items: center;
    pointer-events: none;
    flex: 0 0 auto;
  }

  .with-animation {
    transition: 300ms transform ease-in-out 0ms;
  }

  .tab {
    padding: 0 16px;
    color: #999;
    flex-shrink: 0;
  }
</style>

