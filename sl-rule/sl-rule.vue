<template>
	<div>
    <div class="removerule" @touchstart="rulertouchstart" @touchmove="rulerUltouchmove">
      <div style="text-align: center;">
          <input type="text" v-show="false" v-model="num">
          <span id="num" class="num">{{num}}岁</span>
      </div>
      <div id="ruler-container" ref="ruleclass" class="ruler-container">
          <div id="triangle" class="triangle"></div>
          <div id="ruler" ref="ruler" class="ruler" data-offset="0" >
              <ul id="ruler-ul" class="ruler-ul">
                  <li v-for="(item,index) in rulerArray" :key="item.key">
                      <!-- <span>{{(index+1)*10}}</span> -->
                  </li>
              </ul>
          </div>
      </div>
      <div class="line-bottom"></div>
    </div>
  </div>
</template>
<script>
/**
 * sl-rule
 * @module components/sl-rule
 *
 * @param {} dataParams - 数据参数随功能复杂自行扩充
 * @param {} dataParams-num - 数据参数当前选中的刻度
 *
 * @example
 * <slRule :dataParams="dataParams"></slRule>
 */
import { Toast, MessageBox } from "mint-ui";
export default {
  name: "sl-rule",
  components: {},
  props: {
    dataParams: Object,
    value: String,
  },
  data() {
    return {
      rulerArray: [],
      rulerLength: 11, //尺子长度为110刻度
      num: this.value || 0,
      offsetX: 0,
      moveX: 0,
      moveBefore: 0,
      unit: 0.2,
      len: 0,
    };
  },
  computed: {},
  methods: {
    rulertouchstart() {
      //手指按下时的坐标
      this.offsetX = event.touches[0].clientX;
      //初始化第一次滑动的距离为0
      this.moveBefore = 0;
    },
    rulerUltouchmove() {
      //获取滑动时手指的动态坐标
      var move = event.touches[0].clientX;
      //上一次计算出的刻度尺移动距离
      var offset = this.$refs.ruler.dataset.offset;
      //原来是string，转换为float方便计算
      offset = parseFloat(offset);
      var tempMove = 0;
      var len = 0;
      //相对于手指按下时的距离，除以100是因为要将px转换为rem单位
      tempMove = move - this.offsetX;
      tempMove /= 100;
      //计算两次滑动间的距离
      len = offset + (tempMove - this.moveBefore);
      len = parseFloat(len);
      //边界判断，
      //当前限制0-102
      let lenmin = this.lenmin
      let lenmax = this.lenmax-0.075
      //2rem为一岁20小格子，，，即一小格子为0.1rem，102为204小格子即204*0.1=20.4
      if (len - 0.0 < lenmin && len > lenmax) {
        //此处预留0.075的偏移量
        // console.log(len);

        //将结果保存下来，下一次滑动时取出参与计算
        this.moveX = tempMove;
        this.$refs.ruler.dataset.offset = len;
        this.moveBefore = this.moveX;
        //设置样式
        this.$refs.ruler.style = "transform: translateX(" + len + "rem)";
        //显示刻度，保留2位小数
        this.num = -(len / this.unit).toFixed(0)+"";
        // console.log(this.num);
      }
    }
  },
  mounted() {
    //初始化刻度尺的位置 26*2*0.1
    let nummin = parseInt(this.dataParams.num)
    let nummax = parseInt(this.dataParams.nummax)
    let lenmin = -nummin*2*0.1
    let lenmax = -nummax*2*0.1
    this.lenmin = lenmin
    this.lenmax = lenmax
    this.$refs.ruler.dataset.offset = lenmin;
    this.$refs.ruler.style = "transform: translateX(" + lenmin + "rem)";
  },
  created() {
    this.rulerArray = [];
    for (let index = 0; index < this.rulerLength; index++) {
      this.rulerArray.push(1);
    }
  },
  watch: {
    value(val) {
      this.num = val;
    },
    num(val,oldval) {
      console.log('%c 监听到单选框值变更','color:#00CD00',val)
      this.$emit('input', val)
    },
  },
};
</script>
<style type="text/css" scoped="scoped">
.removerule{
  margin-top: 0.2rem;
  margin-bottom: 0.2rem;
}

.line-bottom {
  border-bottom: 0.02rem solid #eb7760;
  width: 5.9rem;
  margin: 0 auto;
}

.num {
  color: #eb7760;
  font-size: 0.28rem;
}

.ruler{
  margin-top: 0.1rem;
}

.ruler-container {
  position: relative;
  overflow: hidden;
  width: 3.616rem;
  height: 0.58rem;
  margin-top: 0.22rem;
  margin: 0 auto;
  /* border: 1px solid rgb(147, 184, 47); */
}

.triangle {
  /* width: 0;
  height: 0;
  margin: 0 auto;
  z-index: 199;
  border-top: 0.1rem solid rgb(190, 98, 75);
  border-left: 0.1rem solid transparent;
  border-right: 0.1rem solid transparent; */
  width: 0;
  margin: 0 50%;
  position: absolute;
  height: 0.58rem;
  border: 0.02rem solid rgb(190, 98, 75);
}

.ruler ul {
  /* transform: translateX(3.35rem); */
  transform: translateX(1.82rem);
  width: 22.1rem;
  height: 0.4rem;
  position: relative;
}

.ruler ul li {
  width: 2rem;
  height: 2rem;
  text-align: right;
  background: url(/static/img/changcheng/ruler.png) top left no-repeat;
  background-size: 112px auto;
  float: left;
  list-style: none;
}

.ruler ul li span {
  position: relative;
  top: 0.2rem;
  right: -0.03rem;
}
</style>
