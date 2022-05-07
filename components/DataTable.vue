<template lang="pug">
  v-data-table(
    :headers="headers"
    :items="items"
    item-key="email"
    dense
  ).elevation-1.mt-10
    template(v-slot:body.prepend v-if="filters")
      tr.d-none-xs
        td(v-for="filter in filters")
          v-text-field(v-model="filter.value", :type="filter.type" :label="filter.label")
    template(v-slot:footer.prepend)
      div.full-width-xs.mt-xs-15
        | Total {{ items.length }} records
        v-btn.mx-1(color="primary" :disabled="isLoading || items.length >= totalItemsCount" @click="uploadItems()")
          v-icon(dark='') mdi-cached
          | More


</template>

<script>
export default {
  props: ['items', 'headers', 'filters', 'totalItemsCount', 'isLoading'],
  data() {
    return {
      search: '',
    }
  },
  methods: {
    uploadItems() {
      this.$emit('upload-items');
    },
  },
}
</script>

<style lang="sass" scoped>
  .v-data-table
    max-width: 100%
  @media (max-width: 600px)
    .d-none-xs
      display: none
    .full-width-xs
      width: 100%
      text-align: center
    .mt-xs-15
      margin-top: 15px
</style>
