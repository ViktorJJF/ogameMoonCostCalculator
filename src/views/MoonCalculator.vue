<template>
  <v-container fluid class="container-width" v-if="ships.length > 0">
    <v-select
      :items="[{name:'30%',value:0.3},{name:'50%',value:0.5},{name:'80%',value:0.8},]"
      v-model="debris"
      item-text="name"
      item-value="value"
      background-color="white"
      class="input-width"
    ></v-select>
    <v-row>
      <v-col cols="12" sm="4">
        <v-card>
          <v-container fluid>
            <h2>Naves</h2>
            <v-row dense justify="space-between" v-for="(ship, idx) in ships" :key="ship.id">
              <v-col cols="12" sm="9">
                <p>{{ ship.name }}</p>
              </v-col>
              <v-col cols="12" sm="3">
                <v-text-field
                  ref="qtyShip"
                  @keyup.enter="addAttempt()"
                  type="number"
                  v-model="qtyShips[idx].qty"
                  hide-details
                  dense
                  outlined
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="info" @click="addAttempt()">Agregar intento</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
      <v-col cols="12" sm="8">
        <v-card>
          <v-container fluid>
            <h2>Intentos</h2>
            <div v-if="attempts.length>0">
              <v-simple-table class="table-header">
                <template v-slot:default>
                  <thead>
                    <tr>
                      <th class="text-left">
                        <span>Naves</span>
                      </th>
                      <th class="text-left" v-for="i in attempts.length" :key="i">
                        <span>
                          Intento {{ i }}
                          <v-btn color="error" fab x-small @click="deleteAttempt(i-1)">
                            <v-icon>mdi-close</v-icon>
                          </v-btn>
                        </span>
                      </th>
                      <!-- <th class="text-left">Planeta Origen</th> -->
                      <!-- <th class="text-left">Planeta Destino</th> -->
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="i in ships.length" :key="i">
                      <td class="text-left">
                        <b>{{ shipNames[i - 1] }}</b>
                      </td>
                      <td
                        v-for="(attempt, attemptIdx) in attempts"
                        :key="attemptIdx"
                        class="text-left"
                      >
                        <span
                          :class="{'bold-text':parseInt(attempt[i - 1].qty || 0)>0}"
                        >{{ parseInt(attempt[i - 1].qty || 0) }}</span>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <b>SubTotal de intento</b>
                      </td>
                      <td v-for="(attempt, attemptIdx) in attempts" :key="attemptIdx">
                        <p>
                          <b>Metal:</b>
                          {{ subTotal(attempt)[0].toLocaleString() }}
                        </p>
                        <p>
                          <b>Cristal:</b>
                          {{ subTotal(attempt)[1].toLocaleString() }}
                        </p>
                        <p>
                          <b>Deuterio:</b>
                          {{ subTotal(attempt)[2].toLocaleString() }}
                        </p>
                      </td>
                    </tr>
                  </tbody>
                </template>
              </v-simple-table>
            </div>
            <p v-else>
              <v-alert type="error">Aún no agregaste intentos</v-alert>
            </p>
          </v-container>
        </v-card>
        <v-card class="mt-5 results-font" outlined>
          <v-container fluid>
            <h3>Total de coste:</h3>
            <div class="green-square resourceIcon metal mr-3"></div>
            <p>
              <b>Metal:</b>
              {{total.metal.toLocaleString()}}
            </p>
            <div class="resourceIcon crystal mr-3"></div>
            <p>
              <b>Cristal:</b>
              {{total.crystal.toLocaleString()}}
            </p>
            <div class="resourceIcon deuterium mr-3"></div>
            <p>
              <b>Deuterio:</b>
              {{total.deuterium.toLocaleString()}}
            </p>
            <h3>Escombros:</h3>
            <div class="resourceIcon metal mr-3"></div>
            <p>
              <b>Metal:</b>
              {{(total.metal*debris).toLocaleString()}}
            </p>
            <div class="resourceIcon crystal mr-3"></div>
            <p>
              <b>Cristal:</b>
              {{(total.crystal*debris).toLocaleString()}}
            </p>

            <h3>Diferencia (recursos perdidos):</h3>
            <div class="resourceIcon metal mr-3"></div>
            <p>
              <b>Metal:</b>
              {{(total.metal*(1-debris)).toLocaleString()}}
            </p>
            <div class="resourceIcon crystal mr-3"></div>
            <p>
              <b>Cristal:</b>
              {{(total.crystal*(1-debris)).toLocaleString()}}
            </p>
          </v-container>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      debris: 0.8,
      total: { metal: 0, crystal: 0, deuterium: 0 },
      ships: [],
      qtyShips: [
        { id: 1, qty: null },
        { id: 2, qty: null },
        { id: 3, qty: null },
        { id: 4, qty: null },
        { id: 5, qty: null },
        { id: 6, qty: null },
        { id: 7, qty: null },
        { id: 8, qty: null },
        { id: 9, qty: null },
        { id: 10, qty: null },
        { id: 11, qty: null },
        { id: 12, qty: null },
        { id: 13, qty: null },
        { id: 14, qty: null },
        { id: 15, qty: null }
      ],
      attempts: [],
      shipNames: [
        "Nave pequeña de carga",
        "Nave Grande de carga",
        "Cazador Ligero",
        "Cazador Pesado",
        "Crucero",
        "Nave de batalla",
        "Colonizador",
        "Reciclador",
        "Sonda de espionaje",
        "Bombardero",
        "Destructor",
        "Estrella de la muerte",
        "Acorazado",
        "Segador",
        "Explorador"
      ]
    };
  },
  mounted() {
    this.initialData();
  },
  methods: {
    initialData() {
      axios
        .get("/ships.json")
        .then(res => {
          let preShips = res.data;
          for (let idx = 0; idx < preShips.length; idx++) {
            preShips[idx].qty = 0;
          }
          this.ships = preShips;
          //local storage
          // if (localStorage.attempts) {
          //   this.attempts = JSON.parse(
          //     localStorage.getItem("attempts") || "[]"
          //   );
          //   this.getTotal();
          // }
        })
        .catch(err => {
          console.error(err);
        });
    },
    saveToLocalStorage() {
      //optional
      localStorage.setItem("attempts", JSON.stringify(this.attempts));
    },
    addAttempt() {
      this.attempts.push(JSON.parse(JSON.stringify(this.qtyShips)));
      this.getTotal();
      this.clear();
      // this.saveToLocalStorage();
    },
    clear() {
      for (let idx = 0; idx < this.qtyShips.length; idx++) {
        this.qtyShips[idx].qty = null;
      }
    },
    subTotal(attempt) {
      let resources = [0, 0, 0]; //metal - crystal -deuterium
      attempt.reduce((prev, curr) => {
        resources[0] +=
          this.getShipDetails(curr.id).costs.metal * parseInt(curr.qty || 0);
        resources[1] +=
          this.getShipDetails(curr.id).costs.crystal * parseInt(curr.qty || 0);
        resources[2] +=
          this.getShipDetails(curr.id).costs.deuterium *
          parseInt(curr.qty || 0);
        return prev;
      }, 0);
      return resources;
    },
    getShipDetails(id) {
      return this.ships.find(ship => ship.id == id);
    },
    getTotal() {
      let total = { metal: 0, crystal: 0, deuterium: 0 };
      this.attempts.forEach((attempt, i) => {
        attempt.forEach(ship => {
          total.metal +=
            this.getShipDetails(ship.id).costs.metal * parseInt(ship.qty || 0);
          total.crystal +=
            this.getShipDetails(ship.id).costs.crystal *
            parseInt(ship.qty || 0);
          total.deuterium +=
            this.getShipDetails(ship.id).costs.deuterium *
            parseInt(ship.qty || 0);
        });
        this.total = total;
      });
    },
    deleteAttempt(index) {
      console.log("eliminando intento: ", index);
      this.attempts.splice(index, 1);
      this.getTotal();
      this.saveToLocalStorage();
    }
  }
};
</script>

<style lang="scss" scoped>
$base-color: #122d4a;
.inline-element {
  display: inline-block;
}
.container-width {
  //   width: 900px;
}
.input-width {
  max-width: 200px;
}
.results-font {
  font-size: 20px;
}

.resourceIcon {
  background: transparent
    url("https://gf3.geo.gfsrv.net/cdnbb/a9fe14ed992de9b7f40d22213a475e.png") 0
    0 no-repeat;
  width: 48px;
  height: 32px;
  float: left;
}
.resourceIcon.metal {
  background-position: 0 -160px;
}
.resourceIcon.crystal {
  background-position: -48px -160px;
}
.resourceIcon.deuterium {
  background-position: -96px -160px;
}
.table-header {
  th {
    font-size: 21px;
    background-color: $base-color;
    span {
      color: #ffffff;
    }
  }
}
.bold-text {
  font-weight: bold;
  font-size: 1.3em;
  color: $base-color;
}
</style>
