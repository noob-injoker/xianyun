<template>
  <div class="df f-column a-center">
    <div class="top df j-bettween">
      <div class="top-left">
        <div class="df j-bettween">
          <div class="singleTicket ccc">
            <div class="single-line"></div>
            <div class="circle-box ccc">
              <SwapRightOutlined />
            </div>&nbsp;单程
          </div>
          <div class="returnBack ccc">
            <div class="circle-box ccc">
              <SwapOutlined />
            </div>&nbsp;往返
          </div>
        </div>
        <div class="top-content">
          <div class="m-t-20 df">
            <div class="startCity m-l-20 ccc">出发城市</div>
            <div class="df autocomplete-box a-center">
              <a-auto-complete
                v-model:value="valueCity1"
                :data-source="dataSource1"
                style="width: 200px"
                placeholder="请搜索出发城市"
                @select="onSelect1"
                @change="onChange1"
              />
            </div>
          </div>

          <div class="m-t-20 df">
            <div class="toCity m-l-20 ccc">到达城市</div>
            <div class="df autocomplete-box a-center">
              <a-auto-complete
                v-model:value="valueCity2"
                :data-source="dataSource2"
                style="width: 200px;"
                placeholder="请搜索到达城市"
                @select="onSelect2"
                @change="onChange2"
              />
              <div class="change-text">
                <div @click="changeCity" class="text-box ccc">换</div>
              </div>
            </div>
          </div>

          <div class="m-t-20 df">
            <div class="startTime m-l-20 ccc">出发时间</div>
            <div class="data-picker-box">

              <a-date-picker
                style="width: 200px"
                :size="large"
                @change="onChange"
                format="YYYY-MM-DD"
                :disabled-date="disabledDate"
                placeholder="选择时间"
              />
             
              
            </div>
          </div>

          <div class="top-btn-box m-t-20">
            <a-button @click="goToTicketDetail" style="width:78%" type="primary">
              <SearchOutlined />搜索
            </a-button>
          </div>
        </div>
      </div>
      <div class="top-right">
        <img src="../assets/sale.jpg" />
      </div>
    </div>

    <div class="expend-icon df j-bettween">
      <div class="f1 h36 ccc">
        <div class="expand-icon-box icon-one">
          <VerifiedOutlined />
        </div>
        <div class="text">100%航协认证</div>
      </div>

      <div class="f1 h36 ccc">
        <div class="expand-icon-box">
          <SafetyOutlined />
        </div>
        <div class="text">出行保证</div>
      </div>

      <div class="f1 h36 ccc">
        <div class="expand-icon-box icon-three">
          <PhoneFilled />
        </div>
        <div class="text">7x24小时服务</div>
      </div>
    </div>

    <div class="specialFares">
      <div class="specialFares-text">特价机票</div>
    </div>

    <div class="specialFares-pic-box df j-bettween">
      <div v-for="(item,index) in specialFaresObj.specialFaresList" :key="index">
        <div @click="goToTicketPage(item)" class="specialFares-img">
          <img :src="specialFaresObj.specialFaresList[0].cover" alt />
          <div class="specialFares-item-box a-center df j-bettween">
            <div
              class="fs14 m-l-15"
            >{{specialFaresObj.specialFaresList[0].departCity}}--{{specialFaresObj.specialFaresList[0].destCity}}</div>
            <div class="fs18 m-r-15">￥{{specialFaresObj.specialFaresList[0].price}}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import {
  defineComponent,
  reactive,
  toRefs,
  SetupContext,
  onMounted,
} from "vue";
import api from "@/http/api";
import moment from "moment";
import { useRouter } from "vue-router";

interface Data {
  arr: [];
  msg2: string;
  msg: string;
  valueCity1: string;
  valueCity2: string;
  dataSource1: string[];
  dataSource2: string[];
  large: string;
  name: string;
  specialFaresObj: SpecialFaresObj;
  sort1: string;
  sort2: string;
  departDate: string;
}
interface SpecialFaresObj {
  specialFaresList?: SpecialFaresList[];
}
interface SpecialFaresList {
  cover?: string;
  departCity?: string;
  departCode?: string;
  departDate?: string;
  destCity?: string;
  destCode?: string;
  price?: string;
}
interface DataSource {
  name?: string;
}

export default defineComponent({
  name: "",
  props: {},
  components: {},
  setup(props, ctx: SetupContext) {
    console.log(ctx);

    const data: Data = reactive<Data>({
      name: "",
      msg: "",
      msg2: "",
      arr: [],
      valueCity1: "",
      valueCity2: "",
      dataSource1: [],
      dataSource2: [],
      large: "large",
      specialFaresObj: {
        specialFaresList: [
          {
            cover: "",
            departCity: "",
            departCode: "",
            departDate: "",
            destCity: "",
            destCode: "",
            price: "",
          },
        ],
      },
      sort1: "",
      sort2: "",
      departDate: "",
    });
    const router = useRouter();

    const disabledDate = (current: any) => {
      
      return  current && current < moment().subtract("day",1);
    };

    const onSelect1 = (value: string) => {
      console.log("onSelect1", value);
    };

    const onChange1 = (value: any) => {
      data.dataSource1 = [];
      console.log("onChange1", value);
      api
        .getRealTimeCity({ name: value })
        .then((res: any) => {
          console.log(res);
          if (res.data[0].name) {
            const name = res.data[0].name;
            data.dataSource1.push(name);
            data.sort1 = res.data[0].sort;
          }
        })
        .catch((err: Error) => {
          console.log(err);
        });
    };

    const onSearch1 = (searchText: any) => {
      data.dataSource1 = !searchText
        ? []
        : [searchText, searchText.repeat(2), searchText.repeat(3)];
    };
    const onSearch2 = (searchText: any) => {
      data.dataSource2 = !searchText
        ? []
        : [searchText, searchText.repeat(2), searchText.repeat(3)];
    };

    const onSelect2 = (value: any) => {
      console.log("onSelect2", value);
    };
    const onChange2 = (value: string) => {
      data.dataSource2 = [];
      console.log("onChange2", value);
      api
        .getRealTimeCity({ name: value })
        .then((res: any) => {
          console.log(res);
          if (res.data[0].name) {
            const name = res.data[0].name;
            data.dataSource2.push(name);
            data.sort2 = res.data[0].sort;
          }
        })
        .catch((err: Error) => {
          console.log(err);
        });
    };
    const onChange = (date: any, dateString: string) => {
      data.departDate = dateString;
    };

    const goToTicketDetail = () => {
      const item = {
        departCity: data.valueCity1,
        departCode: data.sort1,
        destCity: data.valueCity2,
        destCode: data.sort2,
        departDate: data.departDate,
      };

      const items = JSON.stringify(item);

      localStorage.setItem("ticketDetail", items);
      router.push({
        name: "ticketDetail",
      });
    };
    const goToTicketPage = (item: any) => {
      console.log(item);
      router.push({ name: "ticketDetail" });
      const items = JSON.stringify(item);
      localStorage.setItem("ticketDetail", items);
    };
    const autoGetSpecialFares = () => {
      api
        .getSpecialFares()
        .then((res: any) => {
          data.specialFaresObj.specialFaresList = res.data;
          console.log(data.specialFaresObj.specialFaresList);
        })
        .catch((err: Error) => {
          console.log(err);
        });
    };

    const changeCity = () => {
      data.arr = JSON.parse(JSON.stringify(data.dataSource2));
      data.dataSource2 = JSON.parse(JSON.stringify(data.dataSource1));
      data.dataSource1 = data.arr;
      data.msg = data.valueCity2;
      data.valueCity2 = data.valueCity1;
      data.valueCity1 = data.msg;
    };

    onMounted(() => {
      autoGetSpecialFares();
    });

    return {
      ...toRefs(data),
      moment,
      disabledDate,
      onSelect1,
      onChange1,
      onSelect2,
      onChange2,
      onChange,
      autoGetSpecialFares,
      goToTicketPage,
      onSearch1,
      onSearch2,
      changeCity,
      goToTicketDetail,
    };
  },
});
</script>

<style lang="scss" scoped>
.specialFares-img {
  position: relative;
  width: 225px;
  height: 125px;
}
.specialFares-img:hover {
  cursor: pointer;
}

.specialFares-item-box {
  width: 100%;
  position: absolute;
  bottom: 0;
  left: 0;
  height: 30px;
  background: rgba($color: black, $alpha: 0.3);
  padding: 0 15px;
  color: white;
}

.specialFares-img img {
  width: 100%;
  height: 100%;
}

.specialFares-pic-box {
  width: 1000px;
  box-shadow: 0 0 3px #dddddd;
  padding: 20px 20px;
  margin-top: 20px;
  margin-bottom: 30px;
}

.specialFares {
  color: #409eff;
  width: 1000px;
  font-size: 18px;
}

.text {
  word-spacing: 1px;
  margin-left: 8px;
}

.expend-icon {
  width: 1000px;
  padding: 10px 0px;
  margin: 20px 0;
  background: #f5f5f5;
  box-shadow: 0 0 3px #dddddd;
}

.top-btn-box {
  margin-left: 100px;
}

.autocomplete-box {
  position: relative;
}

.text-box {
  position: absolute;
  right: -10px;
  top: 50%;
  margin-top: -10px;
  width: 20px;
  height: 20px;
  font-size: 12px;
  background: gray;
  color: white;
}
.text-box:hover {
  cursor: pointer;
}

.change-text {
  position: absolute;
  right: -25px;
  top: -40px;
  height: 60px;
  width: 25px;
  border: 1px solid #dddddd;
  border-left: 1px solid white;
}

.startTime {
  width: 80px;
  height: 40px;
}

.startCity {
  width: 80px;
  height: 40px;
}

.toCity {
  width: 80px;
  height: 40px;
}

.single-line {
  position: absolute;
  z-index: 999;
  top: 0;
  left: 0;
  width: 100%;
  height: 2px;
  background: orange;
}

.circle-box {
  border: 1px solid gray;
  width: 18px;
  height: 18px;
  border-radius: 50% 50%;
  font-weight: 700;
}

.singleTicket {
  position: relative;
  width: 198px;
  height: 40px;
}

.returnBack {
  width: 198px;
  height: 40px;
  background: #eeeeee;
}

.top {
  width: 1000px;
}

.top-right {
  width: 620px;
  height: 350px;
}

.top-right img {
  width: 100%;
  height: 100%;
}

.top-left {
  width: 358px;
  border: 1px solid #dddddd;
  box-shadow: 0 0 3px solid #dddddd;
}

.top-content {
  width: 358px;
  height: 278px;
}
</style>
