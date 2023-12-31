<script>
import {TreadmillsApiService} from "@/analytics/services/treadmills-api.service";
import {FilterMatchMode} from "primevue/api";

export default {
  name: 'HealthChecksComponent',
  data() {
    return {
      healthChecks: [],
      treadmills: [],
      centers: [],
      filters: {},
      treadmillService: null,
      //You can create a service per entity
    };
  },
  created() {
    this.treadmillService = new TreadmillsApiService();
    this.initFilters();
    this.loadHealthChecks();
  },
  methods: {
    initFilters() {
      this.filters = {
        global: { value: null, matchMode: FilterMatchMode.CONTAINS },
      };
    },
    loadHealthChecks() {
      this.treadmillService.getAllHealthChecks().then(response => {
        this.healthChecks = response.data;
      }).catch(error => {
        console.log(error);
      });
      this.treadmillService.getAllTreadmills().then(response => {
        this.treadmills = response.data;
      }).catch(error => {
        console.log(error);
      });

      this.treadmillService.getAllCenters().then(response => {
        this.centers = response.data;
      }).catch(error => {
        console.log(error);
      });
    },
    getSerialNumber(record) {
      const treadmill = this.treadmills.find(treadmill => treadmill.id === record.treadmillId);
      return treadmill ? treadmill.serialNumber : '-';
    },
    getCenterName(record) {
      // Get treadmill object
      const treadmill = this.treadmills.find(treadmill => treadmill.id === record.treadmillId);
      // Get center object
      const center = treadmill ? this.centers.find(center => center.id === treadmill.centerId) : null;
      return center ? center.name : '-';
    },
    getFormattedDate(record) {
      return record.year + '-' + record.month + '-' + record.day;
    },
    getFormattedTime(record) {
      return record.hour + ':' + record.minutes + ':' + record.seconds;
    },
  },
}
</script>

<template>
  <div>
    <pv-data-table
        ref="dt"
        :value="healthChecks"
        data-key="id"
        :paginator="true"
        :rows="10"
        :filters="filters"
        paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
        :rowsPerPageOptions="[5, 10, 25]"
        currentPageReportTemplate="Showing {first} to {last} of {totalRecords} healthChecks"
        responsiveLayout="scroll"
    >
      <template #header>
        <div class="flex flex-column md:flex-row md:justify-content-between">
          <h5 class="mb-2 md:m-0 p-as-md-center text-xl">Treadmills Health Check Information</h5>
          <span class="p-input-icon-left">
            <i class="pi pi-search" />
            <pv-input-text v-model="filters['global'].value" placeholder="Search" class="small-text"/>
          </span>
        </div>
      </template>
      <pv-column field="id" header="Record ID" :sortable="true" style="min-width: 8rem"></pv-column>
      <pv-column field="treadmillId" header="Treadmill ID" :sortable="true" style="min-width: 8rem"></pv-column>
      <pv-column :field="getSerialNumber" header="Serial Number" :sortable="true" style="min-width: 8rem"></pv-column>
      <pv-column :field="getCenterName" header="Center Name" :sortable="true" style="min-width: 10rem"></pv-column>
      <pv-column :field="getFormattedDate" header="Date" :sortable="true" style="min-width: 8rem"></pv-column>
      <pv-column :field="getFormattedTime" header="Time" :sortable="true" style="min-width: 8rem"></pv-column>
      <pv-column field="volts" header="Volts" :sortable="true" style="min-width: 8rem"></pv-column>
      <pv-column field="watts" header="Watts" :sortable="true" style="min-width: 8rem"></pv-column>
      <pv-column field="hp" header="HP" :sortable="true" style="min-width: 8rem"></pv-column>
      <template #footer>
        <div class="flex justify-content-center">
          <p>In total there are {{ healthChecks ? healthChecks.length : 0 }} health checks.</p>
        </div>
      </template>
    </pv-data-table>
  </div>
</template>

<style scoped>

.small-text {
  width: 15rem;
}

@media screen and (max-width: 960px) {
  .small-text {
    width: 10rem;
  }
}
</style>