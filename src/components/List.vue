<template>
  <div>
    <table style="width:100%">
        <tr>
            <th>
              ID
            </th>
            <th>
              <div class="tabHeader">
                Prénom
                <input v-model="searchName" placeholder="Rechercher" />              
              </div>
            </th>
            <th>
              <div class="tabHeader">
                Nom
                <input v-model="searchLastname" placeholder="Rechercher" />              
              </div>
            </th>
            <th>
              <div class="tabHeader">
                Adresse
                <input v-model="searchAdresse" placeholder="Rechercher" />              
              </div>
            </th>
            <th>
              <div class="tabHeader">
                Pays
                <input v-model="searchCountry" placeholder="Rechercher" />              
              </div>
            </th>
            <th>
              <div class="tabHeader">
                Ville
                <input v-model="searchCity" placeholder="Rechercher" />              
              </div>
            </th>
            <th>
              <div class="tabHeader">
                Email
                <input v-model="searchEmail" placeholder="Rechercher" />              
              </div>
            </th>
            <th>
              <div class="tabHeader">
                Télephone
                <input v-model="searchPhone" placeholder="Rechercher" />              
              </div>
            </th>
        </tr>
        <tr v-for="item in setvisibleUsers" :key="item.id">
            <td>{{item.id}}</td>
            <td>{{item.firstname}}</td>
            <td>{{item.lastname}}</td>
            <td>{{item.contact.address}}</td>
            <td>{{item.contact.country}}</td>
            <td>{{item.contact.city}}</td>
            <td>{{item.contact.email}}</td>
            <td>{{item.contact.phone}}</td>
        </tr>
    </table>
    <input type="hidden" id="testing-code" :value="JSON.stringify(setvisibleUsers)">
    <Pagination
    @page:update="this.updatePage"
    v-bind:item="this.item"
    v-bind:pageNumber="this.pageNumber"
    v-bind:Size="this.size">
    </Pagination>
    <button v-on:click="downloadData">Télécharger les données</button>
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
          searchId: null,
          searchName: null,
          searchLastname: null,
          searchAdresse: null,
          searchCountry: null,
          searchCity: null,
          searchEmail: null,
          searchPhone: null,
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
    async getUsers() {
      const res = await fetch('https://run.mocky.io/v3/70e5b0ad-7112-41c5-853e-b382a39e65b7');
      const data = await res.json();
      this.item = data.people;
      console.log(this.item)
      const count = _.countBy(this.item, obj => obj.gender == 'Male');
      this.countGender[0] = count.true
      this.countGender[1] = count.false
      this.series = [{
          data: this.countGender
        }]
      this.updateVisibleUsers();
    },
    updatePage(pageNumber) {
      this.pageNumber = pageNumber;
      this.updateVisibleUsers();
    },
    updateVisibleUsers() {
      this.visibleUsers = this.resultQuery.slice(this.pageNumber * this.size, (this.pageNumber * this.size) + this.size);

      if(this.visibleUsers.lenght == 0 && this.currentPage > 0) {
        this.updatePage(this.pageNumber - 1);
      }
    },
    downloadData() {
          let testingCodeToCopy = document.getElementById("testing-code");
          testingCodeToCopy.setAttribute('type', 'text');
          testingCodeToCopy.select();

          try {
            var successful = document.execCommand('copy');
            var msg = successful ? 'Succés' : 'Erreur';
            alert('Les données ont bien été copié dans le presse papier ' + msg);
          } catch (err) {
            alert('Oops, erreur');
          }

          /* unselect the range */
          testingCodeToCopy.setAttribute('type', 'hidden')
          window.getSelection().removeAllRanges();
    }
  },
  computed: {
    resultQuery: function () {
      console.log('ok')
      if(this.searchName){
      return this.item.filter((item)=>{
        return this.searchName.toLowerCase().split(' ').every(v => item.firstname.toLowerCase().includes(v))
      })
      } else if (this.searchLastname) {
      return this.item.filter((item)=>{
        return this.searchLastname.toLowerCase().split(' ').every(v => item.lastname.toLowerCase().includes(v))
      })
      } else if (this.searchAdresse) {
      return this.item.filter((item)=>{
        return this.searchAdresse.toLowerCase().split(' ').every(v => item.contact.address.toLowerCase().includes(v))
      })
      } else if (this.searchCountry) {
      return this.item.filter((item)=>{
        return this.searchCountry.toLowerCase().split(' ').every(v => item.contact.country.toLowerCase().includes(v))
      })
      } else if (this.searchCity) {
      return this.item.filter((item)=>{
        return this.searchCity.toLowerCase().split(' ').every(v => item.contact.city.toLowerCase().includes(v))
      })
      } else if (this.searchEmail) {
      return this.item.filter((item)=>{
        return this.searchEmail.toLowerCase().split(' ').every(v => item.contact.email.toLowerCase().includes(v))
      })
      } else if (this.searchPhone) {
      return this.item.filter((item)=>{
        return this.searchPhone.toLowerCase().split(' ').every(v => item.contact.phone.toLowerCase().includes(v))
      })
      }
      else{
        return this.item;
      }
    },
    setvisibleUsers: function () {
      return this.resultQuery.slice(this.pageNumber * this.size, (this.pageNumber * this.size) + this.size);
    }
  },
  components: {
    Pagination,
    'apexchart': VueApexCharts,
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
.tabHeader {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>