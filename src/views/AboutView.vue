<template>
  <div class="about">
    <div class="welcome-title">宝子专属抽奖工具</div>
    <div class="lotto-wrapper">
      <div class="award-box" :class="item.flag ? 'taken-award' : 'un-taken-award'" v-for="item in awardsList" :key="item.sort"
           :style="{borderColor: active === item.sort ? 'red' : '#409EFF'}">
        <img :src="item.src" alt="">
        <span class="title">{{ item.title }}</span>
      </div>
    </div>
    <el-button type="primary" @click="beginLotto" :loading="btnLoading" :disabled="this.leftAward.length === 0 || this.times === 0">
      {{ times ? `您有${times}次抽奖机会` : '抽奖机会已用完' }}
    </el-button>
  </div>
</template>


<script>
export default {
  data() {
    return {
      awardsList: [
        {sort: 1, title: '毛巾', src: new URL('@/assets/img/towel.webp', import.meta.url).href},
        {sort: 2, title: '运动鞋', src: new URL('@/assets/img/shoes.jpg', import.meta.url).href},
        {sort: 3, title: '电动牙刷', src: new URL('@/assets/img/toothbrush.webp', import.meta.url).href},
        {sort: 4, title: '划船', src: new URL('@/assets/img/boat.jpg', import.meta.url).href},
        {sort: 5, title: '', src: ''},
        {sort: 6, title: '红肠', src: new URL('@/assets/img/sausage.webp', import.meta.url).href},
        {sort: 7, title: '裙子', src: new URL('@/assets/img/skirt.webp', import.meta.url).href},
        {sort: 8, title: '现金100', src: new URL('@/assets/img/money.jpg', import.meta.url).href},
        {sort: 9, title: '爬山', src: new URL('@/assets/img/mountain.webp', import.meta.url).href}
      ],
      awardSort: [1, 2, 3, 6, 9, 8, 7, 4],
      leftAward: [],
      active: 0,
      btnLoading: false,
      interval: null,
      times: 3
    }
  },
  methods: {
    async beginLotto() {

      this.btnLoading = true
      // 旋转顺序
      let sort = [1, 2, 3, 6, 9, 8, 7, 4]
      // 旋转3到6圈
      let round = Math.floor(Math.random() * 3) + 3
      // 获取随机奖品
      let randomIndex = Math.floor(Math.random() * this.leftAward.length)
      let step = this.awardSort.indexOf(this.leftAward[randomIndex]) + 1
      this.leftAward.splice(randomIndex, 1)
      let slow = Math.floor(Math.random() * 5) + 2
      let index = 0
      let delay = 100
      for (let i = 0; i < round * 8 + step; i++) {
        // 前2/3恒速,一圈减速
        if (i < (round - 1) * 8) {
          await this.waitTime(delay)
        } else if (i < round * 8 + step - slow) {
          delay += 40
          await this.waitTime(delay)
        } else {
          delay += 200
          await this.waitTime(delay)
        }
        index += 1
        if (index > 7) {
          index = 0
        }
        this.active = sort[index]
      }
      await this.waitTime(1000)
      this.btnLoading = false
      this.$message.success(`恭喜你获得 [ ${this.awardsList[this.active - 1].title} ]`)
      // 做中奖纪录
      this.awardsList[this.active - 1].flag = true
      this.times -= 1
    },
    waitTime(delayInMS) {
      return new Promise((resolve) => setTimeout(resolve, delayInMS))
    }
  },
  mounted() {
    this.leftAward = [...this.awardSort]
  }
}

</script>


<style scoped lang="scss">
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;

    .welcome-title {
      height: 50px;
      font-size: 40px;
      font-weight: bold;
      margin-bottom: 30px;
    }

    .lotto-wrapper {
      width: 500px;
      height: 500px;
      display: flex;
      flex-wrap: wrap;
      justify-content: space-around;
      align-items: center;


      .award-box {
        width: 33%;
        height: 33%;
        border-radius: 25px;
        transition: all .2s;
        overflow: hidden;
        border: 10px solid;
        position: relative;

        img {
          height: 100%;
          width: 100%;
        }

        .title {
          position: absolute;
          left: 50%;
          top: 50%;
          transform: translate(-50%, -50%);
          visibility: hidden;
          color: white;
          background-color: black;
          padding: 0 5px;
          border-radius: 5px;
        }

        &:hover .title {
          visibility: visible;
        }
      }

      .taken-award {
        position: relative;

        &:after {
          content: '';
          height: 100%;
          width: 100%;
          display: block;
          background-color: gray;
          opacity: 80%;
          z-index: 9;
          top: -151px;
          left: 0;
        }
      }

      div:nth-child(5) {
        background-color: #fff;
        visibility: hidden;
      }
    }


    .el-button {
      margin-top: 20px;
      width: 300px;
      height: 100px;
      font-size: 30px;
      font-weight: bold;
    }
  }
}
</style>
