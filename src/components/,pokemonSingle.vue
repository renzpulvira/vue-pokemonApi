<template>
  <div class="pokemon-modal" v-if="pokemonId">
    <!-- <img class="pokemon-modal__pokeball" src="@/assets/pokeball.png" alt />
    <img class="pokemon-modal__close" v-on:click="toggleClose()" src="@/assets/close.png" />-->
    <div class="pokemon-modal__base-info">
      <div class="pokemon-modal__base-info__wrapper-01">
        <div class="base-info__ident">
          <!-- <img
            v-bind:src="'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/' + (pokemonId) + '.png'"
          />-->
          <div class="base-info__name">
            <p>{{ pokeName }}</p>
            <span>{{ pokeType }}</span>
          </div>
          <div class="base-info__sprites">
            <a v-bind:href="'http://pokemondb.net/pokedex/' + pokeName" target="_blank">
              <img
                v-bind:src="'https://img.pokemondb.net/sprites/black-white/anim/normal/' + pokeName + '.gif'"
                alt
              />
              <img
                v-bind:src="'https://img.pokemondb.net/sprites/black-white/anim/back-normal/' + pokeName + '.gif'"
                alt
              />
            </a>
          </div>
        </div>
        <div class="pokemon-modal__base-info__intro">
          <h4>Intro</h4>
          <p>{{ pokeBio }}</p>
        </div>
      </div>
      <div class="base-info__wrapper-02">
        <div v-if="dataCollection" class="base-info__stats">
          <div class="base-info__stats-container">
            <RadarChart :chart-data="dataCollection" :options="chartOptions" />
          </div>
        </div>
      </div>
    </div>
    <!-- <ul>
      <li
        v-for="pokeStats in pokemonInfo.info[pokemonId-1].pokemon_stats"
        :key="pokeStats.id"
      >{{ pokeStats.base_stat }}</li>
    </ul>-->
    <!-- <div class v-if="dataCollection">
      <RadarChart :chart-data="dataCollection" :options="chartOptions" />
    </div>-->
  </div>
</template>

<script>
import RadarChart from "@/components/RadarChart.js";

export default {
  name: "PokemonSingle",
  components: {
    RadarChart
  },
  props: {
    isOpen: Boolean,
    pokemonId: Number,
    pokemonInfo: Object
  },
  data() {
    return {
      dataCollection: null,
      chartOptions: null
    };
  },
  computed: {
    dataLabels: function() {
      return this.pokemonInfo.stats_label.map(label => label);
    },
    dataStats: function() {
      return this.pokemonInfo.info.pokemon_stats;
    },
    filterIntro: function() {
      return this.pokemonInfo.info[this.pokemonId - 1].intro.filter(bylang => {
        return bylang.language.name.match("en");
      });
    },
    arrayify: function() {
      return this.pokemonInfo.info[this.pokemonId - 1].pokemon_stats.map(
        x => x.base_stat
      );
    },
    pokeName: function() {
      return this.pokemonInfo.info[this.pokemonId - 1].pokemon_name;
    },
    pokeType: function() {
      return this.pokemonInfo.info[this.pokemonId - 1].species;
    },
    pokeBio: function() {
      return this.pokemonInfo.info[this.pokemonId - 1].intro.flavor_text;
    },
    pokeColor: function() {
      return this.pokemonInfo.info[this.pokemonId - 1].color;
    }
  },
  methods: {
    toggleClose: function() {
      this.$emit("toggleClose", false);
    },
    fillData: function() {
      this.dataCollection = {
        labels: this.dataLabels,
        datasets: [
          {
            label: "",
            borderColor: "rgba(0, 0, 0, 0.5)",
            borderWidth: 2,
            data: this.arrayify
          }
        ]
      };
      this.chartOptions = {
        scale: {
          pointLabels: {
            fontSize: 10
          }
        },
        legend: {
          display: false
        },
        title: {
          display: true,
          text: ""
        },
        responsive: true,
        maintainAspectRatio: false,
        aspectRatio: 5,
        pointDot: false,
        showTooltips: false,
        scaleOverride: false,
        scaleSteps: 1,
        scaleStepWidth: 0,
        scaleStartValue: 0
      };
    }
  },
  mounted: function() {
    this.fillData();
  }
};
</script>

<style lang="scss" scoped>
.pokemon-modal {
  position: fixed;
  top: 48%;
  left: 50%;
  transform: translate(-50%, -50%);
  height: 570px;
  width: 676px;
  background: white;
  z-index: 9;
  display: flex;
  transform: translate(-50%, -50.1%);

  /* &__pokeball {
    position: absolute;
    top: 30px;
    left: 80px;
    transform: rotate(30deg);
    z-index: 4;
    width: 100%;
    height: auto;
    width: 80px;
    opacity: 0.5;
  }
 */
  /* &__close {
    position: absolute;
    top: 10px;
    right: 10px;
    width: 16px;
    cursor: pointer;
  }
 */
  &__base-info {
    position: relative;
    z-index: 9;
    display: flex;
    flex-wrap: wrap;
    flex: 0 100%;
    justify-content: space-between;
    align-items: flex-start;
    align-content: flex-start;

    .base-info {
      &__ident {
        display: flex;
        flex: 0 100%;
        align-content: flex-start;
        justify-content: center;
        padding: 10px 0 25px;
        background: white;
        box-shadow: 0px -1px 3px #6f3333;

        span {
          font-size: 12px;
          font-weight: 700;
          flex: 0 100%;
          opacity: 0.6;
        }
      }

      &__name {
        flex: 0 50%;
        justify-content: flex-start;

        p {
          margin: 0;
          font-family: "Roboto";
          font-weight: 600;
          text-transform: capitalize;
          flex: 0 100%;
          margin-bottom: 3px;
        }
      }

      &__sprites {
        flex: 0 50%;
        align-items: flex-end;
        img {
          // flex: 0 100%;
          margin-bottom: 10px;
          height: auto;
          width: auto;
          // height: 96px;
          // width: 100%;
          // max-width: 96px;
        }
      }

      &__stats {
        background: #fff;
        flex: 0 100%;
        justify-content: center;
        align-items: center;

        &-container {
          position: relative;
          width: 230px;
          margin: 0 auto;

          canvas {
            height: 290px !important;
          }
        }
      }

      &__intro {
        display: flex;
        flex: 0 100%;
        padding: 10px 20px 25px;
        flex-wrap: wrap;
        background: white;
        align-content: flex-start;
        align-items: flex-start;

        h4 {
          margin: 0;
          padding: 0;
        }
      }
    }
  }
}

@media screen and (max-width: 425px) {
  .pokemon-modal {
    width: 100%;
    height: 100%;
    padding-top: 0;

    &__close {
      top: 25px;
      right: 10px;
      z-index: 999;
    }

    &__base-info {
      height: 110%;
      overflow-y: scroll;
    }
  }
}
</style>
