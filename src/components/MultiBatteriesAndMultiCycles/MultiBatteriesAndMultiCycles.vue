<template>
  <div class="main-title">
    <div class="number-box">F</div>
    <div class="title-box" style="width: 70%">
      Multi-Batteries & Multi-Cycles Matrix
    </div>
  </div>
    <div class="container">
      <div class="performance-line">
        <p>SOH</p>
        <img src="../../assets/MM_Axis.png" alt="">
        <div class="performance">
          <div class="color" ></div>
        </div>
      </div>
      <div class="view-container">
        <div id="heatView"></div>
      </div>
      <div id="tip"></div>
    </div>
</template>

<script setup>
  import {reactive} from "@vue/reactivity";
  import {onMounted} from "vue";
  import {heatView} from "@/components/MultiBatteriesAndMultiCycles/printVeiw";
  import {connectionStatusStore} from "@/store/connectionStatusStore";
  const connectionStore = connectionStatusStore()

  const colorList = reactive(["#4f9a95", "#62a5a0", "#91c0bc", "#daeae9", "#f3f8f8"])

  connectionStore.$subscribe(() => {
    heatView()
  })
</script>

<style scoped lang="scss">
::-webkit-scrollbar{
  display: none;
}
  .container{
    width: 387px;
    height: 330px;

    .performance-line{
      display: flex;
      justify-content: flex-end;
      height: 15px;
      align-items: center;
      position: relative;

      img{
        position: absolute;
        left: -108px;
        top: -45px;
        transform: scale(0.5);
      }

      p{
        font-size: 7px;
        height: 15px;
        line-height: 15px;
        margin-right: 5px;
      }

      .performance{
        width: 58px;
        height: 10px;
        display: flex;
        justify-content: space-between;

        .color{
          width: 100%;
          height: 100%;
          background: linear-gradient(to right, #8cbebb, white);
        }
      }
    }

    .view-container{
      width: 386px;
      height: 315px;
      margin: 10px 0 0 15px;
      overflow: auto;
      position: relative;

      #heatView {
        width: 380px;
        height: 2790px;
      }
    }
  }

#tip{
  position: absolute;
  margin-left: 10px;
  margin-top: 10px;
  line-height: 22px;
  background-color: rgba(0, 0, 0, .6);
  padding: 4px 9px;
  font-size: 13px;
  color: #fff;
  border-radius: 3px;
  pointer-events: none;
  display: none;
}
</style>
