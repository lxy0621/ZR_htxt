<template>
  <div>
    <div class="search-btn-group">
      <div class="search-btn-group-total" @click="handleSearchTotal" v-bind:class="{'onActiv':totalActive}">
        <p class="search-btn-group-total-num">{{ total }}</p>
        <span class="search-btn-group-total-label">查询结果</span>
      </div>
      <div v-for="(searchBtnItem, index) in searchBtnData" :key="index" class="search-btn-group-con">
        <div class="search-btn-group-con-title" @click="toggleContainer(index)" style=";transition: all 0.8s ease;">
          <em v-show="index == onActive[0]"></em>
          <span v-html="searchBtnItem.name" style="font-size: 12px;"></span>
          <span v-if="index == active ? false : true">
            &nbsp;<i style="padding-top:2px;" class="el-icon-arrow-right"></i>
          </span>
        </div>
        <div class="search-btn-group-container" :class="index == active ? 'group-active' : ''">
          <div class="search-btn-group-type">
            <div v-for="(searchTypeItem, key) in searchBtnItem.children" :key="key" class="search-btn-group-type-block"
                 @click="handleSearch(searchTypeItem,index,key)"
                 v-bind:class="{'onActiv':index==onActive[0]&&onActive[1]==key}">
              <p class="search-btn-group-type-num">{{ searchTypeItem.cnt }}</p>
              <span class="search-btn-group-type-label">{{ searchTypeItem.name }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {CouponAdminService} from "@/service/marketing/tools/CouponAdminService";

export default {
  name: "CouponAdminSearchBtnGroups",
  data() {
    return {
      searchBtnShow: true,
      active: 0,
      queryParams: {
        page: 1,
        limit: 30,
      },
      service: null,
      total: 0,
      searchBtnData: [
        {
          name: "优惠券<br>状态",
          children: [
            {
              name: "待启用",
              cnt: 0,
              state: "1"
            },
            {
              name: "启用中",
              cnt: 0,
              state: "2"
            },
            {
              name: "已关闭",
              cnt: 0,
              state: "4"
            },
            {
              name: "已结束",
              cnt: 0,
              state: "3"
            },
          ]
        },
      ],
      onActive: [null, null],
      totalActive: true,
    };
  },
  mounted() {
    this.service = new CouponAdminService();
    this.findCouponGroups(this.queryParams);
  },
  methods: {
    toggleContainer(index) {
      this.active = index;
    },
    findCouponGroups(queryParams) {
      this.queryParams = queryParams;
      this.service.groupByBatchState(queryParams).then(response => {
        //查询结果
        this.total = response.data.total;
        // 优惠券状态
        this.searchBtnData[0].children[0].cnt = response.data.groupByBatchState[0].cnt;
        this.searchBtnData[0].children[1].cnt = response.data.groupByBatchState[1].cnt;
        this.searchBtnData[0].children[2].cnt = response.data.groupByBatchState[3].cnt;
        this.searchBtnData[0].children[3].cnt = response.data.groupByBatchState[2].cnt;
      });
    },
    handleSearchTotal() {
      this.totalActive = true;
      this.onActive = [null, null];
      delete this.queryParams.groupCouponBatchState;
      this.$emit("search", this.queryParams);
    },
    handleSearch(obj, pindex, cindex) {
      this.totalActive = false;
      this.onActive = [pindex, cindex];

      this.queryParams.groupCouponBatchState = obj.state;
      this.$emit("search", this.queryParams);
    }
  }
};
</script>
<style lang="scss" scoped>
.search-btn-group-container {
  transition: all 0.8s ease;
  opacity: 0;
  width: 0;
}

.group-active {
  transition: all 1s ease;
  opacity: 1;
  width: initial;
}

.search-btn-group {
  display: -webkit-box;
  display: -webkit-flex;
  display: -moz-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-flex-wrap: wrap;
  -ms-flex-wrap: wrap;
  flex-wrap: wrap;
  text-align: center;
  margin: 10px 0 5px;
  height: 54px;
}

.search-btn-group-total {
  padding: 9px 16px;
  background-color: #fff;
  display: -webkit-box;
  display: -webkit-flex;
  display: -moz-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-orient: vertical;
  -webkit-box-direction: normal;
  -webkit-flex-direction: column;
  -moz-box-orient: vertical;
  -moz-box-direction: normal;
  -ms-flex-direction: column;
  flex-direction: column;
  -webkit-box-pack: center;
  -webkit-justify-content: center;
  -moz-box-pack: center;
  -ms-flex-pack: center;
  justify-content: center;
  cursor: pointer;

  .search-btn-group-total-num {
    color: #ffffff;
    margin: 1px;
    font-weight: 700;
    font-size: 12px;
  }

  .search-btn-group-total-label {
    color: #ffffff;
    margin: 1px;
    font-size: 12px;
  }
}

.search-btn-group-con {
  display: -webkit-box;
  display: -webkit-flex;
  display: -moz-box;
  display: -ms-flexbox;
  display: flex;
  margin-left: 16px;
  -webkit-box-shadow: 0 0 4px 0 rgba(0, 0, 0, 0.15);
  -moz-box-shadow: 0 0 4px 0 rgba(0, 0, 0, 0.15);
  box-shadow: 0 0 4px 0 rgba(0, 0, 0, 0.15);

  .search-btn-group-con-title {
    padding: 9px 16px;
    color: #161616;
    background-color: #eef1f6;
    display: -webkit-box;
    display: -webkit-flex;
    display: -moz-box;
    display: -ms-flexbox;
    display: flex;
    align-items: middle;
    -webkit-box-align: center;
    -webkit-align-items: center;
    -moz-box-align: center;
    -ms-flex-align: center;
    align-items: center;
    -webkit-box-pack: center;
    -webkit-justify-content: center;
    -moz-box-pack: center;
    -ms-flex-pack: center;
    justify-content: center;
    cursor: pointer;
    max-width: 100px;
    position: relative;
  }

  .search-btn-group-type {
    display: -webkit-box;
    display: -webkit-flex;
    display: -moz-box;
    display: -ms-flexbox;
    display: flex;
    -webkit-flex-wrap: nowrap;
    -ms-flex-wrap: nowrap;
    flex-wrap: nowrap;
    overflow: hidden;
    max-width: 800px;

    .search-btn-group-type-block {
      display: -webkit-box;
      display: -webkit-flex;
      display: -moz-box;
      display: -ms-flexbox;
      display: flex;
      -webkit-box-orient: vertical;
      -webkit-box-direction: normal;
      -webkit-flex-direction: column;
      -moz-box-orient: vertical;
      -moz-box-direction: normal;
      -ms-flex-direction: column;
      flex-direction: column;
      -webkit-box-pack: center;
      -webkit-justify-content: center;
      -moz-box-pack: center;
      -ms-flex-pack: center;
      justify-content: center;
      padding: 9px 16px;
      background-color: #ffffff;
      border: 1px solid #eef1f6;
      cursor: pointer;
      -webkit-box-flex: 0;
      -webkit-flex: 0 0 auto;
      -moz-box-flex: 0;
      -ms-flex: 0 0 auto;
      flex: 0 0 auto;

      .search-btn-group-type-num {
        color: #409eff;
        margin: 1px;
        font-weight: 700;
        font-size: 12px;
      }

      .search-btn-group-type-label {
        margin: 1px;
        font-size: 12px;
        color: #161616;
      }
    }
  }
}

@import "~@/assets/style/searchBtnGroup.scss";
</style>
