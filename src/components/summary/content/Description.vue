<template>
  <div class="description">
    Now it's {{ shortForecast?.conditionCode }} in
    <span class="location">{{ shortForecast?.location }}</span>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

import { IForecastForecastTimestamps } from "@/models/Models";

export default defineComponent({
  name: "Description",
  props: {
    shortForecast: {
      type: Object as () => IForecastForecastTimestamps,
    },
  },
});
</script>

<style lang="scss" scoped>
@import "@/Styles.scss";

.description,
.location {
  color: $color-white;
  font: normal normal bold 25px/34px Nunito;
  margin: 0 0 40px;
  padding: 0 3px;

  .location {
    text-transform: capitalize;
  }

  @media screen and (min-width: $breakpoint-desktop) {
    padding: 0 21px;
  }
}
</style>
