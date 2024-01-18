<template>
  <div
    v-if="dataIsFetched"
    class="landing-page"
  >
    <div class="landing-page__search">
      <input
        class="landing-page__search-input"
        v-model="cupsSearched"
        type="search"
        id="cups-search"
        placeholder="Enter your CUPS supply point identifier please"
      />
      <button
        class="landing-page__search-button"
        @click="search"
      >
        Search
      </button>
    </div>

    <template v-if="!searchResult">
      <p class="landing-page__no-result-message">{{ noResultMessage }}</p>
    </template>
    <template v-else>
      <div class="landing-page__result">
        <div class="landing-page__offer">
          <p class="landing-page__offer-text">
            <span class="landing-page__offer-common-header">
              This CUPS can acces to the rooftop revolution offers
            </span>
            <br />
            <span v-if="basicDiscount"> and to obtain a 5% discount</span>
            <span v-else-if="specialDiscount"> and to obtain a 12% discount</span>
          </p>
        </div>
        <ClientData :data="searchResult" />
      </div>
    </template>
  </div>
</template>

<script>
import ClientData from './ClientData.vue'

export default {
  name: 'LandingPage',
   components: {
    ClientData
  },
  data () {
    return {
      dataIsFetched: false,
      cupsSearched: null,
      searchResult: null,
      noResultMessage: null,
      clients: {},
      supplyPoints: {}
    }
  },
  async mounted () {
    await this.fetchData();
    this.dataIsFetched = true;
  },
  computed: {
    basicDiscount () {
      if (!this.searchResult || this.searchResult.legth) {
        return false;
      }

      const clientSupplyPoint = this.searchResult.supplyPoint;

      for (let n = 0; n < clientSupplyPoint.neighbors.length; n++) {
        const neighbourSupplyPoint = this._getNeighbourSupplyPoint(clientSupplyPoint, n);

        if (neighbourSupplyPoint[0].power.p1 >= clientSupplyPoint.power.p1 || neighbourSupplyPoint[0].power.p2 >= clientSupplyPoint.power.p2) {
          return false;
        }
      }

      return true;
    },
    specialDiscount () {
      if (!this.searchResult || this.searchResult.legth) {
        return false;
      }

      const clientSupplyPoint = this.searchResult.supplyPoint;

      for (let n = 0; n < clientSupplyPoint.neighbors.length; n++) {
        const neighbourSupplyPoint = this._getNeighbourSupplyPoint(clientSupplyPoint, n);
        const invoicedAmoundAddition = Number(neighbourSupplyPoint[0].invoiced_amount);

        if (invoicedAmoundAddition <= 100) {
          return false;
        }
      }

      return true;
    }
  },
  methods: {
    async fetchData () {
      // const response = await fetch("/endpoint")
      this.clients = this._fetchClients();
      this.supplyPoints = this._fetchSupplyPoints();
    },
    search () {
      this.searchResult = this._getResult();
    },
    _fetchClients () {
      return [{
        "full_name": "Terry Evans",
        "address": "Potato street, 4",
        "cups": "123456",
        "role": "customer",
        "building_type": "house"
      },
      {
        "full_name": "Glenda Gilbert",
        "address": "Potato street, 9",
        "cups": "234567",
        "role": "customer-basic",
        "building_type": "house"
      },
      {
        "full_name": "Phillip Lee",
        "address": "Potato street, 7",
        "cups": "345678",
        "role": "customer-premium",
        "building_type": "apartment"
      },
      {
        "full_name": "Cecil Grant",
        "address": "Sweet potato street, 2",
        "cups": "321654",
        "role": "customer-basic",
        "building_type": "house"
      },
      {
        "full_name": "Clinton Horton",
        "address": "Swede street, 5",
        "cups": "456123",
        "role": "customer",
        "building_type": "apartment"
      },
      {
        "full_name": "Steven Walters",
        "address": "Swede street, 7",
        "cups": "111222",
        "role": "customer",
        "building_type": "house"
      }];
    },
    _fetchSupplyPoints () {
      return [{
        "cups": "123456",
        "tariff": "One price",
        "invoiced_amount": "50.00",
        "power": {
          "p1": "4500",
          "p2": "4200"
        },
        "neighbors": ["234567", "345678"]
      },
      {
        "cups": "234567",
        "tariff": "Two prices",
        "invoiced_amount": "45.00",
        "power": {
          "p1": "5000",
          "p2": "4800"
        },
        "neighbors": ["123456", "345678"]
      },
      {
        "cups": "345678",
        "tariff": "One price",
        "invoiced_amount": "40.00",
        "power": {
          "p1": "5500",
          "p2": "5800"
        },
        "neighbors": ["234567", "123456"]
      },
      {
        "cups": "321654",
        "tariff": "One price",
        "invoiced_amount": "68.00",
        "power": {
          "p1": "4200",
          "p2": "4200"
        },
        "neighbors": []
      },
      {
        "cups": "456123",
        "tariff": "One price",
        "invoiced_amount": "200.00",
        "power": {
          "p1": "6200",
          "p2": "6000"
        },
        "neighbors": ["111222"]
      },
      {
        "cups": "111222",
        "tariff": "Three prices",
        "invoiced_amount": "10.00",
        "power": {
          "p1": "4700",
          "p2": "4500"
        },
        "neighbors": ["456123"]
      }];
    },
    _getResult () {
      const client = this.clients.filter((client) => {
        return client.cups === this.cupsSearched;
      });

      if (!client.length) {
        this.noResultMessage = "This CUPS is not registered in our database";
        return false;
      }

      const supplyPoint = this.supplyPoints.filter((supplyPoint) => {
        return supplyPoint.cups === this.cupsSearched;
      });

      if (client[0].building_type !== "house" || !supplyPoint[0].neighbors.length) {
        this.noResultMessage = "There are no offers available for this CUPS";
        return false;
      }

      return { client: client[0], supplyPoint: supplyPoint[0]};
    },
    _getNeighbourSupplyPoint (clientSupplyPoint, neighbourIndex) {
      return this.supplyPoints.filter((supplyPoint) => {
        return supplyPoint.cups === clientSupplyPoint.neighbors[neighbourIndex];
      });
    }
  }
}
</script>

<style>
  .landing-page {
    width: 800px;
    margin: 0 auto;
  }

  .landing-page__search {
    display: flex;
  }

  .landing-page__search-input {
    flex: 1;
    padding: 16px 16px 16px 24px;
    border-radius: 20px 0 0 20px;
    border: 1px solid #bbb;
    border-right: none;
  }

  .landing-page__search-button {
    cursor: pointer;
    padding: 16px 32px;
    background-color: #e5007e;
    border-radius: 0 20px 20px 0;
    color: white;
    border: none;
    font-size: 18px;
    font-weight: bold;
  }

  .landing-page__offer {
    font-size: 18px;
    color: #37424d;
  }

  .landing-page__offer-text {
    line-height: 150%;
  }

  .landing-page__offer-common-header {
    font-weight: bold;
    font-size: 20px;
  }

  .landing-page__offer a {
    color: #e5007e;
    text-decoration: none;
  }
</style>
