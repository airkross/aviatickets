<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-12 d-flex justify-content-center">
          <div class="block-logo">
            <router-link to="/" class="logo">
              <img src="@/assets/Logo.png" alt="Логотип" />
            </router-link>
          </div>
        </div>
        <div class="col-4">
          <app-sidebar @checked="checkedArr = $event"></app-sidebar>
        </div>
        <div class="col-8">
          <div class="container">
            <div class="row">
              <div class="col-12">
                <div class="border-radio">
                  <label for="cheap" class="radio-label">
                    <input
                      type="radio"
                      id="cheap"
                      value="cheap"
                      v-model="radio"
                      @click="radioFilter($event.target.id)"
                    />
                    <span class="radio cheap" :class="{'active': radio === 'cheap'}">Самый дешевый</span>
                  </label>
                  <label for="quick" class="radio-label">
                    <input
                      type="radio"
                      id="quick"
                      value="quick"
                      v-model="radio"
                      @click="radioFilter($event.target.id)"
                    />
                    <span class="radio quick" :class="{'active': radio === 'quick'}">Самый быстрый</span>
                  </label>
                </div>
              </div>
            </div>
          </div>
          <p class="d-flex justify-content-center" v-if="!filteredTickets.length">Рейсов нет.</p>
          <app-ticket
            v-else
            v-for="(ticket,index) in filteredTickets"
            :key="index"
            :ticket="ticket"
          ></app-ticket>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Sidebar from "@/components/Sidebar.vue";
import Ticket from "@/components/Ticket.vue";
export default {
  name: "home",
  data() {
    return {
      tickets: [],
      tempArrayTickets: [],
      checkedArr: [],
      radio: ""
    };
  },
  computed: {
    filteredTickets() {
      if (!this.checkedArr.length) {
        return this.tickets;
      }
      if (this.checkedArr.indexOf("all") !== -1) {
        return this.tickets;
      }
      let tickets = this.checkedArr.map(key => {
        return this.tickets.filter(key2 => {
          return key2.segments[0].stops.length == key;
        });
      });
      tickets = [].concat.apply([], tickets);
      return tickets;
    }
  },
  methods: {
    radioFilter(id) {
      let tickets = this.filteredTickets;
      if (id === "cheap") {
        let tmp;
        for (let i = 0; i < tickets.length; i++) {
          for (let j = 0; j < tickets.length; j++) {
            if (tickets[i].price < tickets[j].price) {
              tmp = tickets[i];
              tickets[i] = tickets[j];
              tickets[j] = tmp;
            }
          }
        }
      } else {
        let tmp;
        for (let i = 0; i < tickets.length; i++) {
          for (let j = 0; j < tickets.length; j++) {
            if (
              tickets[i].segments[0].duration < tickets[j].segments[0].duration
            ) {
              tmp = tickets[i];
              tickets[i] = tickets[j];
              tickets[j] = tmp;
            }
          }
        }
      }
    },
    checked(checkedArr) {
      this.checkedArr.push(checkedArr);
    }
  },
  async mounted() {
    try {
      const searchId = await this.$store.dispatch("searchId");
      const tickets = await this.$store.dispatch("fetchTickets", searchId);
      tickets.length = 5;
      tickets.forEach(key => {
        this.tickets.push(key);
      });
    } catch (e) {
      throw e;
    }
  },
  components: {
    appSidebar: Sidebar,
    appTicket: Ticket
  }
};
</script>

<style scoped>
input[type="radio"] {
  display: none;
}

label {
  width: 50%;
  /* max-width: 252px; */
}

.radio {
  display: block;
  height: 50px;
  text-align: center;
  line-height: 50px;
  font-family: Open Sans;
  font-style: normal;
  font-weight: 600;
  font-size: 12px;

  letter-spacing: 0.5px;
  text-transform: uppercase;

}

.cheap {
  border-radius: 3px 0 0 3px;
}
.quick {
  border-radius: 0 3px 3px 0;
  border-left: 1px solid #dfe5ec;
}
.radio-label {
  margin-bottom: 0;
}
.border-radio {
  border: 1px solid #dfe5ec;
  border-radius: 3px;
  margin-bottom: 20px;
}

.active {
  background: #2196f3;
  color: #FFFFFF;

}
</style>
