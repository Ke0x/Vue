<template>
  <div>
    <table style="width:100%">
        <tr>
            <th>Id</th>
            <th>Prénom</th>
            <th>Nom</th>
            <th>Age</th>
            <th>Genre</th>
            <th>Email</th>
        </tr>
        <tr v-for="item in visibleUsers" :key="item.id">
            <td>{{item.id}}</td>
            <td>{{item.firstname}}</td>
            <td>{{item.lastname}}</td>
            <td>{{item.age}}</td>
            <td>{{item.gender}}</td>
            <td>{{item.email}}</td>
        </tr>
    </table>
    <Pagination
    @page:update="this.updatePage"
    v-bind:item="this.item"
    v-bind:pageNumber="this.pageNumber"
    v-bind:Size="this.size">
    </Pagination>
  </div>
    <div class="chartContainer">
    <apexchart
      width="500"
      type="bar"
      :options="chartOptions"
      :series="series"
    ></apexchart>
  </div>
</template>

<script lang="ts">
import Pagination from './Pagination.vue';
import _ from 'lodash';
import VueApexCharts from "vue3-apexcharts";


export default {
  name: 'List',
  data() {
      return {
          item: [],
          pageNumber: 0,
          size: 15,
          visibleUsers: [],
          countGender: [],
          chartOptions: {
            chart: {
              id: "vuechart-h-f",
            },
            xaxis: {
              categories: ['homme', 'femme'],
            },
          },
          series: [
            {
              name: "homme-femme",
              data: [52, 48],
            },
          ],
      }
  },
  beforeMount(){
    this.getUsers();
  },
  methods: {
    async getUsers(){
      const res = await fetch('https://run.mocky.io/v3/89d7da8a-d3c7-4bee-8a62-e62db3239aaa');
      const data = await res.json();
      this.item = data;
      const count = _.countBy(this.item, obj => obj.gender == 'men');
      console.log(count)
      this.countGender[0] = count.true
      this.countGender[1] = count.false
      this.series = [{
          data: this.countGender
        }]
      console.log(this.series)
      this.updateVisibleUsers();
    },
    updatePage(pageNumber) {
      this.pageNumber = pageNumber;
      this.updateVisibleUsers();
    },
    updateVisibleUsers() {
      this.visibleUsers = this.item.slice(this.pageNumber * this.size, (this.pageNumber * this.size) + this.size);

      if(this.visibleUsers.lenght == 0 && this.currentPage > 0) {
        this.updatePage(this.pageNumber - 1);
      }
    }
  },
  components: {
    Pagination,
    apexchart: VueApexCharts,
  }
}

</script>

<style>
table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

td, th {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #dddddd;
}
.chartContainer {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  padding: 20px
}
</style>