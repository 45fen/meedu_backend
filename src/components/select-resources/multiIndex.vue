<template>
  <div class="meedu-dialog-mask" v-if="show">
    <div class="meedu-dialog-box">
      <div class="meedu-dialog-header">选择</div>
      <div class="meedu-dialog-body">
        <div class="float-left">
          <el-tabs v-model="activeResource">
            <el-tab-pane
              :label="item.name"
              :name="item.key"
              v-for="(item, index) in avaliableResources"
              :key="index"
            ></el-tab-pane>
          </el-tabs>
        </div>
        <div class="float-left">
          <vod-comp
            v-if="activeResource === 'vod'"
            :selected="selectedVod"
            @change="change"
          ></vod-comp>
          <live-comp
            v-else-if="activeResource === 'live'"
            :selected="selectedLive"
            @change="change"
          ></live-comp>
          <paper-comp
            v-else-if="activeResource === 'paper'"
            :selected="selectedPaper"
            @change="change"
          ></paper-comp>
        </div>
      </div>
      <div class="meedu-dialog-footer">
        <el-button type="primary" @click="confirm"> 确定 </el-button>
        <el-button @click="close" class="ml-30">取消</el-button>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from "vuex";
import VodComp from "./components/multiVod.vue";
import LiveComp from "./components/multiLive.vue";
import PaperComp from "./components/multiPaper.vue";

export default {
  components: {
    VodComp,
    LiveComp,
    PaperComp,
  },
  props: [
    "show",
    "selectedIds",
    "enabledResource",
    "selectedVod",
    "selectedLive",
    "selectedPaper",
  ],
  data() {
    return {
      selectedResult: null,
      activeResource: null,
    };
  },
  computed: {
    ...mapState(["enabledAddons"]),
    avaliableResources() {
      let resources = [];

      if (this.enabledResourceMap["vod"]) {
        resources.push({
          name: "录播",
          key: "vod",
        });
      }

      if (this.enabledResourceMap["live"] && this.enabledAddons["Zhibo"]) {
        resources.push({
          name: "直播",
          key: "live",
        });
      }

      if (this.enabledAddons["Paper"]) {
        if (this.enabledResourceMap["paper"]) {
          resources.push({
            name: "试卷",
            key: "paper",
          });
        }
      }
      return resources;
    },
    enabledResourceMap() {
      if (!this.enabledResource) {
        return {};
      }
      let items = this.enabledResource.split(",");
      let r = {};
      items.forEach((item) => {
        r[item] = 1;
      });

      return r;
    },
    resourceActive() {
      let r = null;
      if (this.enabledResourceMap["paper"]) {
        r = "paper";
      } else {
        r = "vod";
      }
      return r;
    },
  },
  mounted() {
    this.activeResource = this.resourceActive;
  },
  methods: {
    close() {
      this.$emit("close");
    },
    confirm() {
      if (this.selectedResult === null || this.selectedResult.length === 0) {
        this.$message.warning("请先选择内容");
        return;
      }
      this.$emit("change", this.selectedResult);
    },
    change(result) {
      this.selectedResult = result;
    },
  },
};
</script>

<style lang="less" scoped>
.meedu-dialog-box {
  width: 900px !important;
  margin-left: -450px !important;
}
</style>
