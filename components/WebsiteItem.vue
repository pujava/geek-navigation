<template>
  <el-col :xs="24" :md="6" class="website-item">
    <div class="info" target="_blank" @click="handleClick">
      <div class="info-header">
        <el-image class="logo" :src="navData.logo" fit="cover" lazy />
        <span class="title">{{ navData.name }}</span>
      </div>
      <div class="desc">{{ navData.desc || "这个网站什么描述也没有..." }}</div>
    </div>
    <div class="website-item__footer">
      <span class="website-item__icon" :class="isView && 'active'"
        ><span class="iconfont icon-attentionfill"></span>{{ navData.view }}</span
      >
      <span
        class="website-item__icon"
        :class="isStar && 'active'"
        @click="handleStar"
        ><span class="iconfont icon-appreciatefill"></span>{{ navData.star }}</span
      >
    </div>
  </el-col>
</template>

<script>
export default {
  props: {
    data: {
      type: Object,
      default: function() {
        return {};
      }
    }
  },
  data() {
    return {
      isStar: false,
      isView: false,
      navData: this.data
    };
  },
  methods: {
    async handleClick() {
      const views = this.$storage.get('VIEWS') || {}
      const { href, view, _id: id } = this.navData;
      await this.$api.editNav({ id, view: view + 1 });
      views[id] = view + 1
      this.$storage.set('VIEWS', views)
      window.open(href, "_blank");
    },
    async handleStar() {
      const stars = this.$storage.get('STARS') || {}
      let { star, _id: id } = this.navData;
      if (this.isStar || stars[id]) return

      star++
      await this.$api.editNav({ id, star });
      stars[id] = star
      this.navData.star = star
      this.isStar = true
      this.$storage.set('STARS', stars)
    },
  },
};
</script>

<style lang="scss">
@mixin text-overflow($line) {
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: $line;
}
.website-item {
  font-size: 13px;
  margin-bottom: 10px;
  border-radius: 6px;

  a {
    color: #999;
  }

  .title {
    color: #3273dc;
    font-size: 16px;
    @include text-overflow(2);
  }

  .iconfont {
    margin-left: 15px;
    cursor: pointer;
  }

  .desc {
    margin-top: 10px;
    @include text-overflow(1);
  }

  &__footer {
    border-top: 1px solid #f2f2f2;
    background: #fff;
    padding: 10px 15px;
    text-align: right;
  }
  &__icon.active {
    &,
    .iconfont {
      color: #2a97ff;
    }
  }
}

.info {
  display: block;
  box-shadow: 1px 2px 3px #f2f6f8;
  transition: all 0.3s;
  background: #fff;
  padding: 10px;
  height: 100px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  &:hover {
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  }

  &-header {
    display: flex;
    align-items: center;
  }
}

.logo {
  min-width: 35px;
  width: 35px;
  height: 35px;
  border-radius: 50%;
  margin-right: 15px;
}
</style>
