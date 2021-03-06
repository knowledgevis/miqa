<script>
import { mapState } from "vuex";

export default {
  name: "WindowControl",
  data: () => ({
    windowWidth: 0,
    windowLevel: 0
  }),
  computed: {
    ...mapState(["proxyManager", "currentDataset"]),
    representation() {
      return this.currentDataset && this.proxyManager.getRepresentations()[0];
    },
    windowWidthDomain() {
      return this.representation.getPropertyDomainByName("windowWidth");
    },
    windowLevelDomain() {
      return this.representation.getPropertyDomainByName("windowLevel");
    }
  },
  watch: {
    windowWidth(value) {
      this.representation.setWindowWidth(value);
    },
    windowLevel(value) {
      this.representation.setWindowLevel(value);
    },
    proxyManager() {
      this.modifiedSubscription.unsubscribe();
      this.bindWindow();
    }
  },
  created() {
    this.bindWindow();
  },
  beforeDestroy() {
    this.modifiedSubscription.unsubscribe();
  },
  methods: {
    bindWindow() {
      this.windowWidth = this.representation.getWindowWidth();
      this.windowLevel = this.representation.getWindowLevel();
      this.modifiedSubscription = this.representation.onModified(() => {
        this.windowWidth = this.representation.getWindowWidth();
        this.windowLevel = this.representation.getWindowLevel();
      });
    },
    increaseWindowWidth() {
      var windowWidth = Math.min(
        (this.windowWidth +=
          (this.windowWidthDomain.max - this.windowWidthDomain.min) / 30),
        this.windowWidthDomain.max
      );
      this.windowWidth = windowWidth;
    },
    decreaseWindowWidth() {
      var windowWidth = Math.max(
        (this.windowWidth -=
          (this.windowWidthDomain.max - this.windowWidthDomain.min) / 30),
        this.windowWidthDomain.min
      );
      this.windowWidth = windowWidth;
    },
    increaseWindowLevel() {
      var windowLevel = Math.min(
        (this.windowLevel +=
          (this.windowLevelDomain.max - this.windowLevelDomain.min) / 30),
        this.windowLevelDomain.max
      );
      this.windowLevel = windowLevel;
    },
    decreaseWindowLevel() {
      var windowLevel = Math.max(
        (this.windowLevel -=
          (this.windowLevelDomain.max - this.windowLevelDomain.min) / 30),
        this.windowLevelDomain.min
      );
      this.windowLevel = windowLevel;
    }
  }
};
</script>

<template>
  <div>
    <v-layout>
      <v-flex>
        <v-slider
          class="mr-4 verticalOffset"
          hide-details
          label="Window"
          :min="windowWidthDomain.min"
          :max="windowWidthDomain.max"
          :step="windowWidthDomain.step"
          v-model="windowWidth"
          v-mousetrap="[
            { bind: '=', handler: increaseWindowWidth },
            { bind: '-', handler: decreaseWindowWidth }
          ]"
        ></v-slider>
      </v-flex>
      <v-flex shrink style="width: 80px">
        <v-text-field
          class="mt-0"
          hide-details
          single-line
          type="number"
          v-model="windowWidth"
        ></v-text-field>
      </v-flex>
    </v-layout>
    <v-layout>
      <v-flex>
        <v-slider
          class="mr-4 verticalOffset"
          hide-details
          label="Level"
          :min="windowLevelDomain.min"
          :max="windowLevelDomain.max"
          :step="windowLevelDomain.step"
          v-model="windowLevel"
          v-mousetrap="[
            { bind: ']', handler: increaseWindowLevel },
            { bind: '[', handler: decreaseWindowLevel }
          ]"
        ></v-slider>
      </v-flex>
      <v-flex shrink style="width: 80px">
        <v-text-field
          class="mt-0"
          hide-details
          single-line
          type="number"
          v-model="windowLevel"
        ></v-text-field>
      </v-flex>
    </v-layout>
  </div>
</template>

<style lang="scss">
.verticalOffset {
  position: relative;
  top: 12px;
}
</style>
