<template>
  <div class="container">
    <div class="main-title" style="width: 40% ;margin-bottom: -10px" >
      <div class="number-box">B</div>
      <div class="title-box">
       Primary Interactive View
      </div>
    </div>
    <div class="view">
      <div class="sub-title">
        <div class="warn">
          <div style="transform: rotate(-90deg)">WARN</div>
        </div>
        <div class="SOH">
          <div style="transform: rotate(-90deg); ">SOH Prediction & Feature Value</div>
        </div>
      </div>
      <div id="mainView">

      </div>
      <div class="select-button-container">
        <div class="title">Curve Selection</div>
        <div class="btn-box">
          <div class="btn" v-for="(btn, id) in selectionBtns" :key="id">
            <el-button style="width: 80%; height: 100%; border-radius: 3px" @click="clickBtn(id)"
                       :style="{backgroundColor: id === selectedBtnId ? mainColor.green : 'white'}">
              <p :style="{color: id === selectedBtnId ? 'white' : mainColor.green}">{{btn}}</p>
            </el-button>
          </div>
        </div>
      </div>
    </div>
  </div>

</template>

<script setup>
  import {printView} from "@/components/PrimaryInteractive/printView";
  import {output7} from '@/plugins/axiosInstance'
  import {selectedCyclesStore} from "@/store/selectedCyclesStore";
  import {batteryListStore} from "@/store/batteryListStore";
  import * as d3 from "d3";
  import Tools from "@/tools";
  const store = selectedCyclesStore()
  const storeForBattery = batteryListStore()
  import {connectionStatusStore} from "@/store/connectionStatusStore";
  import {onMounted} from "vue";
  import {reactive, ref} from "@vue/reactivity";
  import {mainColor} from "@/assets/colorUtils";
  const connectionStore = connectionStatusStore()

  const selectionBtns = reactive(["Prediction", "Raw"])
  const selectedBtnId = ref(0)
  let dataCache = null
  const clickBtn = (id) => {
    // id = 0是预测值，id = 1是原始数据
    printView(dataCache || output7, id)
    selectedBtnId.value = id
  }

  // 本来是打算每次打开程序都读取一遍数据，但是pinia存取大数据比较慢，所以直接写死了弄成了json
    const readCsv = () => {
    const tempList = []
    const voltList = []
      const speedList = []
      const mileageList = []
    //这里边放的是一个三维数组
    // d3.csv('src/csv/competition(original).csv').then((data) => {
    //
    //   for(let i = 0; i < 27900; i+=100) {
    //     const tempCurrList = []
    //     const voltCurrList = []
    //     const speedCurrList = []
    //     const mileageCurrList = []
    //     for(let j = 0; j < 100; j++){
    //       const batteryTemp = data[i + j].cell_temp_list.split('_').map(v => +v)
    //       const batteryVolt = data[i + j].cell_volt_list.split(':')[1].split('_').map(v => +v)
    //       // 这里就是一个二维数组，横坐标是电池，纵坐标是数据记录周期
    //       tempCurrList.push(batteryTemp)
    //       voltCurrList.push(batteryVolt)
    //       // 速度和行驶里程是单列数组
    //       speedCurrList.push(+data[i + j].speed)
    //       mileageCurrList.push(+data[i + j].mileage)
    //     }
    //     // 是一个三维数据，注意调用
    //     tempList.push(tempCurrList)
    //     voltList.push({"col": voltCurrList})
    //     // 速度和行驶里程是二维数组
    //     speedList.push(speedCurrList)
    //     mileageList.push(mileageCurrList)
    //   }
    //   storeForBattery.updateBatteryTempList(tempList)
    //   storeForBattery.updateBatteryVoltList(voltList)
    //   // Tools.exportJson('tempList.json', JSON.stringify(tempList))
    //   Tools.exportJson('voltList.json', JSON.stringify(voltList))
    //   // Tools.exportJson('speedList.json', JSON.stringify(speedList))
    //   // Tools.exportJson('mileageList.json', JSON.stringify(mileageList))
    //   // Tools.exportJson('competition.json', JSON.stringify(data))
    // })

    d3.csv('src/csv/output7.csv').then((data) => {
      Tools.exportJson('output7.json', JSON.stringify(data))
    })
  }
  const dealFakeData = (tempList, voltList) => {
    const newTempList = tempList.map((v, i) => {
      return v.map(va => {
        return va.map(val => {
          return i < 220 ? val - 40 : i < 244 ? val - 40 + Math.floor(10 * (270 - i) / 50) :
              val - 35 + Math.floor(10 * (270 - i) / 50)
        })
      })
    })
    const newVoltList = voltList.map(v => {
      return v.map(va => {
        return va.map(val => {
          return val - 1000
        })
      })
    })

    Tools.exportJson('tempList.json', JSON.stringify(newTempList))
    // Tools.exportJson('voltList.json', JSON.stringify(newVoltList))
  }
  store.$subscribe((arg, data) => {
    dataCache = data.selectedCycles
    printView(data.selectedCycles, selectedBtnId.value)
  })
  connectionStore.$subscribe(() => {
    printView(output7, selectedBtnId.value)
  })
  onMounted(() => {
    // readCsv()
    // dealFakeData(tempList, voltList)
  })
</script>

<style scoped lang="scss">
#mainView{
  margin-top: 26px;
  width: 1750px;
  height: 430px;
}
  .container{
    .view{
      height: 100%;
      display: flex;
      .sub-title{
        width: 27px;
        height: 100%;
        margin-top: 30px;
        .warn{
          height: 64px;
          width: 27px;
          margin-bottom: 20px;
          background-color: #cbe1ec;
          display: flex;
          justify-content: center;
          align-items: center;
          color: #397699
        }

        .SOH{
          margin-top: 10px;
          height: 370px;
          width: 27px;
          white-space: nowrap;
          background-color: #cbe1ec;
          display: flex;
          justify-content: center;
          align-items: center;
          color: #397699
        }
      }
    }
  }

  .select-button-container{
    height: 60px;
    width:200px;
    position: absolute;
    border: 2px solid #e5e5e5;
    border-radius: 4px;
    top: 420px;
    left: 100px;
    background-color: #fdfdfd;

    .title{
      width: 80%;
      margin: 0 auto;
      display: flex;
      justify-content: center;
      margin-bottom: 10px;
    }

    .btn-box{
      width: 100%;
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
  }
</style>
