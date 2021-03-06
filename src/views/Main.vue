<template>
  <div class="main">
    <Search
      :error-message="errorMessage"
      :loading="nextLoading"
      @search-location="searchLocation"
      @set-error-message="setErrorMessage"
    />
    <Summary
      v-if="!initialLoading"
      :longForecast="longForecast"
      :shortForecast="shortForecast"
      @search-location="searchLocation"
    />
    <ExtendedMobile
      v-if="!initialLoading"
      :longForecast="longForecast"
      :shortForecast="shortForecast"
    />
    <ExtendedDesktop v-if="!initialLoading" />
  </div>
  <div v-if="initialLoading" class="loader animation">
    <div class="title">weather</div>
    <img class="image" :src="image" />
    <img class="image above" :src="image" />
    <div class="title">
      {{ errorMessage ? "There seems to be a connection problem.." : null }}
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

import { fetchForecast } from "@/api/API";
import { findTime } from "@/helpers/findTime";
import { IForecast, IForecastForecastTimestamps } from "@/models/Models";
import ExtendedDesktop from "@/components/extended-desktop/ExtendedDesktop.vue";
import ExtendedMobile from "@/components/extended-mobile/ExtendedMobile.vue";
import Search from "@/components/input/Search.vue";
import Summary from "@/components/summary/Summary.vue";

export default defineComponent({
  name: "Home",
  components: {
    ExtendedDesktop,
    ExtendedMobile,
    Search,
    Summary,
  },
  data: () => ({
    url: ["http://localhost:8080/v1/places/", "/forecasts/long-term"],
    image: require("@/assets/images/day/clear.png"),
    errorMessage: "",
    longForecast: {} as IForecast,
    shortForecast: {} as IForecastForecastTimestamps,
    initialLoading: true,
    nextLoading: false,
  }),
  methods: {
    searchLocation(input: string) {
      this.nextLoading = true;
      fetchForecast(this.url[0] + input + this.url[1]).then(
        ({ data, errorMessage }) => {
          if (data) {
            const time = findTime();
            this.longForecast = data;
            this.shortForecast = {
              ...data.forecastTimestamps.filter(
                (stamp) => stamp.forecastTimeUtc.includes(time) === true
              )[0],
              location: data.place.code,
            };
            this.initialLoading = false;
            this.errorMessage = "";
          } else {
            this.errorMessage = errorMessage || "There seems to be a problem..";
          }
          this.nextLoading = false;
        }
      );
    },
    setErrorMessage(message: string) {
      this.errorMessage = message;
    },
  },
  beforeMount() {
    this.searchLocation("klaipeda");
  },
});
</script>

<style lang="scss" scoped>
@import "@/Styles.scss";

.main {
  width: 100%;
  max-width: $breakpoint-max-width;
  background: $color-main-background 0% 0% no-repeat padding-box;

  @media screen and (min-width: $breakpoint-desktop) {
    display: flex;
    flex-direction: column;
    padding: 31px 44px;
    overflow: scroll;
  }
}

.loader {
  display: flex;
  flex-direction: column;
  align-content: center;
  justify-content: center;
  position: absolute;
  left: 0;
  top: 0;
  height: 100vh;
  width: 100vw;
  padding: 20px;
  padding-bottom: 30%;
  background: $color-primary;
  overflow: hidden;

  .title {
    padding: 17px 0;
    color: $color-white;
    font: normal normal 900 35px/47px Nunito;
    letter-spacing: 0px;
    text-align: center;
  }

  .image {
    height: 49.34px;
    width: 49.34px;
    margin: 0 auto;
    animation: spin 40s linear infinite;

    &.above {
      margin: -49.34px auto 0;
      animation: spin-backwards 40s linear infinite;
    }

    @keyframes spin {
      100% {
        transform: rotate(360deg);
      }
    }

    @keyframes spin-backwards {
      100% {
        transform: rotate(-360deg);
      }
    }
  }

  @media screen and (min-width: $breakpoint-desktop) {
    padding-bottom: 10%;
  }
}
</style>
