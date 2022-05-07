<template lang="pug">
  v-container
    v-row
      v-col(cols)
        DataTable(
          v-if="items.length"
          :headers="headers"
          :items="items"
          :filters="filters"
          :totalItemsCount="totalItemsCount"
          :isLoading="isLoading"
          @upload-items='uploadData()'
        )
        v-progress-circular(
          v-else
          width="2"
          color="rs__primary"
          indeterminate
        ).mx-auto
</template>

<script>
import DataTable from '~/components/DataTable.vue';
import sales from '~/api/sales.js';

export default {
  components: {
    DataTable
  },
  data() {
    return {
      sales,
      usersData: [],
      usersRows: {
        prev: 0,
        next: 50,
        step: 50,
      },
      headers: [
        {
          text: 'Name', value: 'name', align: 'start', filter: f => {
            return f.toLowerCase().includes(this.filters[0].value.toLowerCase());
          }
        },
        {
          text: 'Email', value: 'email', filter: f => {
            return f.toLowerCase().includes(this.filters[1].value.toLowerCase());
          }
        },
        {
          text: 'Gender', value: 'gender', filter: f => {
            return f.includes(this.filters[2].value);
          }
        },
        {
          text: 'Year', value: 'year', filter: f => {
            return f >= this.filters[3].value;
          }
        },
        {
          text: 'Sales', value: 'sales', filter: f => {
            return Number(f) >= this.filters[4].value;
          }
        },
        {
          text: 'Country', value: 'country', filter: f => {
            return f.toLowerCase().includes(this.filters[5].value.toLowerCase());
          }
        },
      ],
      filters: [
        { name: 'name', type: 'text', value: '', label: 'User name' },
        { name: 'email', type: 'text', value: '', label: 'User email' },
        { name: 'gender', type: 'text', value: '', label: 'Case sensitive' },
        { name: 'year', type: 'number', value: '', label: 'More/equal' },
        { name: 'sales', type: 'text', value: '', label: 'More/equal' },
        { name: 'country', type: 'text', value: '', label: 'Country filter' },
      ],
      isLoading: false,
    }
  },
  async created() {
    this.usersData = await this.fetchData(this.usersRows.prev, this.usersRows.next);
  },
  computed: {
    items() {
      return this.usersData.map((item) => {
        item.name = `${item.user.first_name} ${item.user.last_name}`;
        return item;
      });
    },
    totalItemsCount() {
      return sales.results.length;
    },
  },
  methods: {
    async fetchData(prev, next) {
      this.isLoading = true;
      await this.delay(3000);
      this.isLoading = false;
      return sales.results.slice(prev, next);
    },
    async uploadData() {
      if (this.isLoading) return false;
      this.incrementUsersRows();
      const response = await this.fetchData(this.usersRows.prev, this.usersRows.next);
      this.usersData.push(...response);
    },
    delay(ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
    },
    incrementUsersRows() {
      this.usersRows.prev += this.usersRows.step;
      this.usersRows.next += this.usersRows.step;
    },
  }
}
</script>

<style lang="sass" scoped>
.v-progress-circular
  position: absolute
  top: 50%
  left: 50%
</style>
