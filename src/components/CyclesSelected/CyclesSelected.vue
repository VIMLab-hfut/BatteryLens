<template>
    <div class="container">
      <div class="SC_legend">
        <img src="../../assets/SC_Lenged.png" alt="">
      </div>
        <div class="main-title">
          <div class="number-box">A</div>
          <div class="title-box">
            Cycles Selection View
          </div>
        </div>
        <div class="top">
            <div class="legend">
                <div class="title">Edge Layer Legend</div>
                <div class="legends" v-for="(item, index) in leftLegends" :key="index">
                    <div class="color"
                        :style="{ backgroundColor: item.color }"
                        style="margin-right: 4px;"></div>
                    <div class="text">{{ item.text }}</div>
                </div>
            </div>
            <div class="btn-box">
              <div class="btn" v-for="(btn, id) in selectionBtns" :key="id">
                <el-button
                    style="width: 80%; height: 100%; border-radius: 3px" @click="clickBtn(id)"
                    :style="{backgroundColor: id === selectedBtnId ? mainColor.green : 'white',
                            color: id === selectedBtnId ? 'white' : mainColor.green}">{{ btn }}</el-button>
              </div>
            </div>
            <div class="legend">
                <div class="title" style="text-align: right;">Main Layer Legend</div>
                <div class="legends"
                    v-for="(item, index) in rightLegends"
                    :key="index"
                    style="justify-content:right;">
                    <div class="text" style="margin-right: 4px;">{{ item.text }}</div>
                    <div class="color" :style="{ backgroundColor: item.color }"></div>
                </div>
            </div>
        </div>

        <div class="view">
          <div id="main"></div>
        </div>
        <div class="bottom-legend">
            <div class="color"></div>
            <div class="text">State of Charge</div>
        </div>
    </div>
</template>

<script>
import {reactive, ref} from "@vue/reactivity";
import {printView} from "@/components/CyclesSelected/printView";
import {defineComponent, onMounted} from "vue";
import {connectionStatusStore} from "@/store/connectionStatusStore";
import {mainColor, bgColor} from "@/assets/colorUtils";
const connectionStore = connectionStatusStore()

export default defineComponent({
  computed: {
    mainColor() {
      return mainColor
    }
  },
  setup(){
    const leftLegends = reactive([
      { text: "value1 Abnormal", color: mainColor.brown },
      { text: "value2 Abnormal", color: mainColor.pink }
    ])
    const rightLegends = reactive([
      { text: "State of Health", color: bgColor.green },
      { text: "Warning Range", color: bgColor.pink }
    ])
    const selectionBtns = reactive(['Curve', 'Rose'])
    const selectedBtnId = ref(0)

    connectionStore.$subscribe(() => {
      const div = document.getElementById('main')
      const divProperty = div.getClientRects()[0]
      printView(divProperty.left, divProperty.right, selectedBtnId.value)
    })

    const clickBtn = (id) => {
      selectedBtnId.value = id
      const div = document.getElementById('main')
      const divProperty = div.getClientRects()[0]
      printView(divProperty.left, divProperty.right, selectedBtnId.value)
    }

    return{
      leftLegends,
      rightLegends,
      selectionBtns,
      selectedBtnId,
      clickBtn
    }
  }
})

</script>

<style lang="scss" scoped>
.legend {
    width: 120px;
    height: 100%;

    .title {
        font-size: 9px;
        margin-bottom: 3px;
    }

    .legends {
        width: 100%;
        height: 14px;
        display: flex;
        align-items: center;

        .color {
            width: 10px;
            height: 10px;
        }

        .text {
            font-size: 9px;
            color: #a6a6a6;
        }
    }


}

.btn-box{
  width: 50%;
  height: 40px;
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  margin: 0 auto;

  .btn{
    height: 50%;
    width: 50%;
    margin: 2px 0;
    display: flex;
    justify-content: center;
  }
}

.container {
    width: 100%;
  position: relative;

  .SC_legend {
    position: absolute;
    left: -48px;
    top: 72px;
    pointer-events: none;

    img{
      transform: scale(0.46);
    }
  }

    .top {
        height: 45px;
        width: 100%;
        display: flex;
        justify-content: space-between;
        margin-top: 10px;
    }

    .view{
      width: 100%;
      height: 320px;
    }

    .bottom-legend{
        display: flex;
        align-items: center;
        //margin-top: 320px;
        .color {
            width: 10px;
            height: 10px;
            background-color: #d9aa69;
            margin-right: 4px;
        }

        .text{
            font-size: 9px;
            color: #a6a6a6;
        }
    }
}
</style>
