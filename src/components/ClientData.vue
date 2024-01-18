<template>
  <div v-if="data" class="client-data">
    <h3>CLIENT</h3>
    <table class="client-data__table" >
      <tr>
        <th :key="`client-name-${index}`" v-for="(value, name, index) in data.client">
          {{ getFormatedText(name) }}
        </th>
      </tr>
      <tr>
        <td :key="`client-value-${index}`" v-for="(value, name, index) in data.client">
          {{ getFormatedText(value) }}
        </td>
      </tr>
    </table>
    <h3>SUPPLY POINT</h3>
    <table class="client-data__table" >
      <tr>
        <th :key="`supply-point-name-${index}`" v-for="(value, name, index) in data.supplyPoint">
          {{ getFormatedText(name) }}
        </th>
      </tr>
      <tr>
        <td :key="`supply-point-value-${index}`" v-for="(value, name, index) in data.supplyPoint">
          {{ value }}
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  name: 'ClientData',
  props: {
    data: []
  },
  methods: {
    getFormatedText (text) {
      text = text.replace("_", " ");
      return text.charAt(0).toUpperCase() + text.slice(1)
    }
  }
}
</script>

<style>
  .client-data {
    margin-top: 40px;
  }

  .client-data__table {
    width: 100%;
    border-spacing: 0;
    border: 1px solid #ccc;
    margin-bottom: 8px;
  }

  .client-data__table th,
  .client-data__table td {
    padding: 16px;
  }

  .client-data__table th {
    background: #37424d;
    color: white;
  }

  .client-data__table td {
    background: white;
  }

  .client-data__table td + td {
    border-left: 1px solid #ccc;
  }
</style>
