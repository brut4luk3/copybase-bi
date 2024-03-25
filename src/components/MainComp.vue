<template>
  <div class="main-comp">
    <header>
      <h1>Copybase BI</h1>
      <h2>Encontre suas métricas de MRR, Churn Rate e muito mais!</h2>
      <img src="@/assets/data_science.png" alt="Data Science">
    </header>
    <div class="content">
      <div class="left-container">
        <canvas id="main-chart"></canvas>
      </div>
      <div class="right-container" ref="rightContainer">
        <!-- Gráficos adicionais serão inseridos aqui -->
      </div>
    </div>
    <!-- Modal para exibição do gráfico selecionado -->
    <div v-if="isModalVisible" class="popup-overlay" @click="closeModal">
      <div class="popup-content" @click.stop>
        <canvas id="modal-chart"></canvas>
      </div>
    </div>
    <div class="button-container">
      <input type="file" id="file" ref="file" style="display: none;" @change="handleFileUpload"
        accept=".csv, application/vnd.openxmlformats-officedocument.spreadsheetml.sheet" />
      <button @click="checkBeforeUpload">Carregue sua planilha</button>
    </div>
    <FooterComp />
  </div>
  <RobotChat />
</template>

<script>
import axios from 'axios';
import Chart from 'chart.js/auto';
import FooterComp from './FooterComp.vue';
import RobotChat  from './RobotChat.vue'

export default {
  name: 'MainComp',
  components: {
    FooterComp,
    RobotChat
  },
  data() {
    return {
      mainChartData: null,
      mainChartLabel: '',
      mainChartType: '',
      additionalCharts: [],
      mainChartInstance: null,
      selectedChart: null,
      isModalVisible: false,
    };
  },
  methods: {
    checkBeforeUpload() {
      if (this.mainChartData) {
        if (confirm("Essa ação irá excluir sua planilha, deseja prosseguir?")) {
          location.reload();
        } else {
          return;
        }
      } else {
        this.$refs.file.click();
      }
    },
    handleFileUpload(event) {
      const file = event.target.files[0];
      const formData = new FormData();
      formData.append('file', file);

      axios.post('http://127.0.0.1:5000/upload', formData, {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      })
        .then(response => {
          this.mainChartData = response.data.MRR;
          this.mainChartLabel = 'Receita Recorrente Mensal (MRR)';
          this.mainChartType = 'line';
          this.renderChart(this.mainChartData, this.mainChartLabel, 'main-chart', this.mainChartType);
          this.createAdditionalCharts(response.data['Churn Rate'], 'Taxa de Churn', 0, 'bar');
          this.createAdditionalCharts(response.data['Trial Cancelled'], 'Cancelamentos de Trial', 1, 'bar');
          this.createAdditionalCharts(response.data['Active Subscribers'], 'Assinantes Ativos', 2, 'pie');
        })
        .catch(error => console.error(error));
    },
    showModal(chartData, chartLabel, chartType) {
      this.selectedChart = { data: chartData, label: chartLabel, type: chartType };
      this.isModalVisible = true;
      this.$nextTick(() => {
        this.renderChart(this.selectedChart.data, this.selectedChart.label, 'modal-chart', this.selectedChart.type);
      });
    },
    closeModal() {
      this.isModalVisible = false;
      this.selectedChart = null;
    },
    renderChart(data, label, elementId, type) {
      const colors = ['#41167F', '#5A4E8F', '#736C9F', '#8C8AAF', '#A5A8BF'];
      const dataValues = Object.values(data);

      const backgroundColors = type === 'line' ? colors[0] : dataValues.map((_, index) => colors[index % colors.length]);

      const ctx = document.getElementById(elementId).getContext('2d');
      const newChartInstance = new Chart(ctx, {
        type: type,
        data: {
          labels: Object.keys(data),
          datasets: [{
            label: label,
            data: dataValues,
            backgroundColor: backgroundColors,
            borderColor: type === 'pie' ? 'white' : colors[0],
            borderWidth: type === 'pie' ? 0 : 1,
          }]
        },
        options: {
          onClick: () => {
            if (elementId !== 'main-chart') {
              this.showModal(data, label, type);
            }
          },
          plugins: {
            title: {
              display: true,
              text: label
            }
          },
          scales: type !== 'pie' ? {
            y: {
              beginAtZero: true
            }
          } : {}
        },
      });

      if (elementId === 'main-chart') {
        this.mainChartInstance = newChartInstance;
      }
    },
    createAdditionalCharts(data, label, index, type) {
      const chartContainer = document.createElement('canvas');
      chartContainer.id = `chart-${index}`;
      this.$refs.rightContainer.appendChild(chartContainer);
      this.additionalCharts.push({ id: chartContainer.id, data, label, type });
      this.renderChart(data, label, chartContainer.id, type);
    },
  },
}
</script>

<style>
@import "../styles/MainComp.css";
</style>