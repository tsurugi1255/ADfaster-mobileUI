<script>
import ArmageddonButton from "../../tabs/celestial-pelle/ArmageddonButton";
import RealityCurrencyHeader from "../../RealityCurrencyHeader";

import HeaderTickspeedInfo from "../HeaderTickspeedInfo";

import RealityButton from "./RealityButton";

// This component contains antimatter and antimatter rate at the start of the game, as well as some additional
// information depending on the UI (tickspeed for Classic, game speed for Modern). Everything but antimatter is
// removed once Reality is unlocked, to make room for the reality button
export default {
  name: "HeaderCenterContainer",
  components: {
    HeaderTickspeedInfo,
    RealityCurrencyHeader,
    RealityButton,
    ArmageddonButton,
  },
  data() {
    return {
      shouldDisplay: true,
      showEP: false,
      showNextEP: false,
      eternityPoints: new Decimal(0),
      infinityPoints: new Decimal(0),
      isModern: false,
      hasRealityButton: false,
      isDoomed: false,
      antimatter: new Decimal(0),
      antimatterPerSec: new Decimal(0),
    };
  },
  methods: {
    update() {
      this.shouldDisplay = player.break || !Player.canCrunch;
      if (!this.shouldDisplay) return;
      this.showEP = PlayerProgress.eternityUnlocked();
      this.showNextEP = Player.canEternity && player.records.thisReality.maxEP.lt(100) &&
      this.eternityPoints.copyFrom(Currency.eternityPoints.value.floor());
      this.infinityPoints.copyFrom(Currency.infinityPoints.value.floor());

      this.isModern = player.options.newUI;
      this.isDoomed = Pelle.isDoomed;
      this.antimatter.copyFrom(Currency.antimatter);
      this.hasRealityButton = PlayerProgress.realityUnlocked() || TimeStudy.reality.isBought;
      if (!this.hasRealityButton) this.antimatterPerSec.copyFrom(Currency.antimatter.productionPerSecond);
    },
  },
};
</script>

<template>
  <div
    v-if="shouldDisplay"
    class="c-prestige-button-container"
  >
  
    <div
      v-if="hasRealityButton"
      class="c-reality-container"
    >
      <ArmageddonButton
        v-if="isDoomed"
        :is-header="true"
      />
      <RealityButton v-else />
    </div>
    <RealityCurrencyHeader />
    <div
        v-if="showEP"
        class="c-eternity-points"
      >
      You have
      <span class="c-game-header__ep-amount">{{ format(eternityPoints, 2) }}</span>
      {{ pluralize("Eternity Point", eternityPoints) }}.
    </div>
    <div class="c-infinity-points">
      You have
      <span class="c-game-header__ip-amount">{{ format(infinityPoints, 2) }}</span>
      {{ pluralize("Infinity Point", infinityPoints) }}.
    </div>
    <span class="c-antimatter-amt">You have <span class="c-game-header__antimatter">{{ format(antimatter, 2, 1) }}</span> antimatter.</span>
    <div v-if="!hasRealityButton">
      You are getting {{ format(antimatterPerSec, 2) }} antimatter per second.
      <br>
      <HeaderTickspeedInfo />
    </div>
  </div>
</template>

<style scoped>
.c-reality-container {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  width: 97.5%;
  min-height: 35%;
}

.c-infinity-points,
.c-eternity-points,
.c-antimatter-amt {
  font-size: 2.7rem;
  color: rgb(109, 109, 109);
}
</style>
