<template>
  <div class="content">
    <div v-if="profileValid">
      <div class="row">
        <div class="col-4">
          <card id="info">
            <template slot="header">
              <h5 class="card-category">Info</h5>
              <h3 class="card-title">
                <i class="tim-icons icon-single-02 text-success "></i>
                {{ name }}
              </h3>
            </template>
            <table class="table">
              <tbody>
                <tr>
                  <th scope="row" class="text-left">
                    <b>部门: </b>
                  </th>
                  <td>
                    {{ info.institution }}
                  </td>
                </tr>
                <tr>
                  <th scope="row" class="text-left">
                    <b>职位: </b>
                  </th>
                  <td>
                    {{ info.occupation }}
                  </td>
                </tr>
                <tr>
                  <th scope="row" class="text-left">
                    <b>研究领域: </b>
                  </th>
                  <td>
                    {{ info.interests }}
                  </td>
                </tr>
              </tbody>
            </table>
            <p class="mb-1">
              <b>{{ info.summary }}</b>
            </p>
          </card>
        </div>
        <div class="col-8">
          <card id="papers">
            <template slot="header">
              <h5 class="card-category">论文</h5>
              <h3 class="card-title">
                <i class="tim-icons icon-paper text-warning "></i>
                {{ name }}的论文
              </h3>
            </template>
            <div id="papers-list" class="list-group">
              <a
                v-for="paper in papers"
                :key="paper.id"
                :href="paper.link"
                class="list-group-item list-group-item-action flex-column align-items-start text-left"
              >
                <div class="d-flex w-100 justify-content-between ">
                  <h5 class="mb-1">
                    <b>{{ paper.title }}</b>
                  </h5>
                  <small>{{ paper.date }}</small>
                </div>
                <p class="mb-1">
                  {{ paper.summary }}
                </p>
                <small>{{ paper.influence }}</small>
              </a>
            </div>
          </card>
        </div>
      </div>
      <div class="row">
        <div class="col-lg-12 col-md-12">
          <card class="knowledgegraph">
            <template slot="header">
              <h5 class="card-category">关系</h5>
              <h3 class="card-title">
                <i class="tim-icons icon-square-pin text-primary "></i>
                关系图谱
              </h3>
            </template>
            <D3Net
              :netdata="JSON.parse(JSON.stringify(communityGraph))"
              :propOptions="JSON.parse(JSON.stringify(communityGraph.options))"
            />
          </card>
        </div>
      </div>
    </div>
    <div v-else>
      <p id="no-profile">{{ name }}的个人主页还未上传</p>
    </div>
  </div>
</template>

<script>
import { Card } from "@/components/index";
import { getPersonalDetails, postCount } from "@/api/api";
import { communityOptions, personalOptions } from "./options.js";
import LineChart from "@/components/Charts/LineChart";
import * as chartConfigs from "@/components/Charts/config";
import D3Net from "@/components/D3Net/D3Net";
import config from "@/config";

export default {
  name: "PersonalDetails",
  components: {
    Card,
    LineChart,
    D3Net
  },
  data() {
    return {
      profileValid: true,
      name: "",
      info: {},
      papers: {},
      paperTrend: {
        bigLineChart: {
          activeIndex: 0,
          chartData: { datasets: [{}] },
          extraOptions: chartConfigs.purpleChartOptions,
          gradientColors: config.colors.primaryGradient,
          gradientStops: [1, 0.4, 0],
          categories: []
        }
      },
      communityGraph: {
        nodes: [],
        links: [],
        options: communityOptions()
      },
      personalGraph: {
        nodes: [],
        links: [],
        options: personalOptions()
      }
    };
  },
  created() {
    this.getPersonalDetails(this.$route.params.name);
  },
  methods: {
    handleInfo(info) {
      this.info = info;
      if (this.info.interests.length > 3) {
        this.info.interests =
          this.info.interests[0].split("_").join(" ") +
          ", " +
          this.info.interests[1].split("_").join(" ") +
          ", " +
          this.info.interests[2].split("_").join(" ") +
          "...";
      } else {
        this.info.interests = this.info.interests.join(", ");
      }
    },
    handlePapers(papers) {
      this.papers = papers;
    },
    handlePaperTrend(paperTrend) {
      this.paperTrend["tabs"] = Object.keys(paperTrend);
      this.paperTrend.bigLineChart["allData"] = [];
      this.paperTrend["labels"] = [];
      for (let tab of this.paperTrend.tabs) {
        this.paperTrend.bigLineChart["allData"].push(
          paperTrend[tab]["amounts"]
        );
        this.paperTrend["labels"].push(paperTrend[tab]["date"]);
      }
    },
    initBigChart(index) {
      let chartData = {
        datasets: [
          {
            fill: true,
            borderColor: config.colors.primary,
            borderWidth: 2,
            borderDash: [],
            borderDashOffset: 0.0,
            pointBackgroundColor: config.colors.primary,
            pointBorderColor: "rgba(255,255,255,0)",
            pointHoverBackgroundColor: config.colors.primary,
            pointBorderWidth: 20,
            pointHoverRadius: 4,
            pointHoverBorderWidth: 15,
            pointRadius: 4,
            data: this.paperTrend.bigLineChart.allData[index]
          }
        ],
        labels: this.paperTrend.labels[index]
      };
      this.$refs.bigChart.updateGradients(chartData);
      this.paperTrend.bigLineChart.chartData = chartData;
      this.paperTrend.bigLineChart.activeIndex = index;
    },
    handleCommunityGraph(communityGraph) {
      this.communityGraph.largestNodeSize = 1;
      this.communityGraph.nodes = communityGraph.nodes;
      for (let node of this.communityGraph.nodes) {
        node["_size"] = 1;
        node["_color"] = 1;
      }
      this.communityGraph.links = communityGraph.links;
      this.communityGraph["isFieldDetails"] = false;
      this.communityGraph["isPersonalDetails"] = true;
    },
    handlePersonalGraph(personalGraph) {
      this.personalGraph.largestNodeSize = 1;
      this.personalGraph.nodes = personalGraph.nodes;
      for (let node of this.personalGraph.nodes) {
        node["_size"] = 1;
        node["_color"] = 2;
      }
      this.personalGraph.links = personalGraph.links;
      this.personalGraph["isFieldDetails"] = false;
      this.personalGraph["isPersonalDetails"] = true;
    },
    handleResponse(response) {
      this.handleInfo(response.info);
      this.handlePapers(response.papers);
      this.handlePaperTrend(response.paperTrend);
      this.handleCommunityGraph(response.communityGraph);
      this.handlePersonalGraph(response.personalGraph);
    },
    handleEmptyResponse() {
      this.profileValid = false;
    },
    getPersonalDetails(name) {
      this.name = name.split("_").join(" ");
      getPersonalDetails({ name: name })
        .then(response => {
          if (response.length == 0) {
            this.handleEmptyResponse();
          } else {
            this.handleResponse(response[0]);
          }
        })
        .finally(() => {
          if (this.profileValid) {
            this.initBigChart(0);
          }
        });
      postCount()
        .then(() => {})
        .finally(() => {});
    }
  }
};
</script>

<style scoped>
#info {
  height: 350px;
  overflow: scroll;
}

.list-group {
  max-height: 270px;
  overflow: scroll;
}

.knowledgegraph {
  max-height: 350px;
}

#papers {
  height: 350px;
}

#no-profile {
  font-size: 30px;
  color: black;
}
</style>
