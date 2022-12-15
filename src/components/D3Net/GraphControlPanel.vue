<template>
  <div id="menu">
    <div class="row">
      <div class="col-lg-6 col-md-12">
        <card>
          <table class="table">
            <thead>
              <tr>
                <th scope="col" colspan="3">节点与链接</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <th scope="row">
                  节点数量
                </th>
                <td>
                  <input
                    type="range"
                    v-model.number="setts.currentNodesLimit"
                    @input="changeSettings"
                    min="1"
                    :max="setts.maxNodes"
                    step="10"
                  />
                </td>
                <td>
                  <span>{{ setts.currentNodesLimit }}</span>
                </td>
              </tr>
              <tr>
                <th scope="row">
                  节点大小
                </th>
                <td>
                  <input
                    type="range"
                    v-model.number="opts.sizeRatio"
                    @input="change"
                    min="0.1"
                    max="1"
                    step="0.1"
                  />
                </td>
                <td>
                  <span>{{ opts.sizeRatio }}</span>
                </td>
              </tr>
              <tr>
                <th scope="row">
                  链接大小
                </th>
                <td>
                  <input
                    type="range"
                    v-model.number="opts.linkWidth"
                    @input="change"
                    min="1"
                    max="15"
                    step="1"
                  />
                </td>
                <td>
                  <span>{{ opts.linkWidth }}</span>
                </td>
              </tr>
              <tr>
                <th scope="row">字体大小</th>
                <td>
                  <input
                    type="range"
                    v-model.number="opts.fontSize"
                    @input="change"
                    min="1"
                    max="30"
                    step="1"
                  />
                </td>
                <td>
                  <span>{{ opts.fontSize }}</span>
                </td>
              </tr>
              <tr>
                <th scope="row">显示节点名称</th>
                <td>
                  <input
                    type="checkbox"
                    v-model="opts.nodeLabels"
                    @change="change"
                  />
                </td>
                <td></td>
              </tr>
              <tr>
                <th scope="row">显示链接名称</th>
                <td>
                  <input
                    type="checkbox"
                    v-model="opts.linkLabels"
                    @change="change"
                  />
                </td>
                <td></td>
              </tr>
            </tbody>
          </table>
        </card>
      </div>

      <div class="col-lg-6 col-md-12">
        <card>
          <table class="table">
            <thead>
              <tr>
                <th scope="col" colspan="3">视图</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <th scope="row">
                  密度
                </th>
                <td>
                  <input
                    type="range"
                    v-model.number="opts.force"
                    @input="change"
                    min="1"
                    max="5000"
                    step="1"
                  />
                </td>
                <td>
                  <span>{{ opts.force }}</span>
                </td>
              </tr>
              <tr>
                <th scope="row">
                  X方向密度
                </th>
                <td>
                  <input
                    type="range"
                    v-model.number="opts.forces.X"
                    @input="change"
                    min="0.1"
                    max="1"
                    step="0.1"
                  />
                </td>
                <td>
                  <span>{{ opts.forces.X }}</span>
                </td>
              </tr>
              <tr>
                <th scope="row">
                  Y方向密度
                </th>
                <td>
                  <input
                    type="range"
                    v-model.number="opts.forces.Y"
                    @input="change"
                    min="0.1"
                    max="1"
                    step="0.1"
                  />
                </td>
                <td>
                  <span>{{ opts.forces.Y }}</span>
                </td>
              </tr>
              <tr>
                <th scope="row">
                  X偏移
                </th>
                <td>
                  <input
                    type="range"
                    v-model.number="opts.offset.x"
                    @input="change"
                    min="-1000"
                    max="1000"
                    step="1"
                  />
                </td>
                <td>
                  <span>{{ opts.offset.x }}</span>
                </td>
              </tr>
              <tr>
                <th scope="row">
                  Y偏移
                </th>
                <td>
                  <input
                    type="range"
                    v-model.number="opts.offset.y"
                    @input="change"
                    min="-1000"
                    max="1000"
                    step="1"
                  />
                </td>
                <td>
                  <span>{{ opts.offset.y }}</span>
                </td>
              </tr>
            </tbody>
          </table>
        </card>
      </div>
    </div>
  </div>
</template>
<script>
import { Card } from "@/components/index";

export default {
  name: "graph-control-panel",
  components: {
    Card
  },
  props: {
    settings: {
      type: Object
    },
    options: {
      type: Object
    }
  },
  data() {
    return {
      opts: {}
    };
  },
  created() {
    this.opts = this.options;
    this.setts = this.settings;
  },
  methods: {
    change() {
      this.$emit("options", this.opts);
    },
    changeSettings() {
      this.$emit("settings", this.setts);
    },
    reset() {
      // this.opts = Object.assign({}, defaultData.options);
      // this.options.width = this.$el.clientWidth;
      // this.options.height = this.$el.clientHeight;
      // this.$emit("reset", this.options);
    },
    emit(e) {
      this.$emit(e);
    }
  }
};
</script>
