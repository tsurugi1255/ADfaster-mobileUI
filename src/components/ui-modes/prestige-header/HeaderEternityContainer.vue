<script>
import EternityButton from "./EternityButton";
import UnlockInfinityDimButton from "./UnlockInfinityDimButton";

export default {
  name: "HeaderEternityContainer",
  components: {
    EternityButton,
    UnlockInfinityDimButton,
  },
  data() {
    return {
      showContainer: false,
      showEP: false,
      showNextEP: false,
      eternityPoints: new Decimal(0),
      nextEP: new Decimal(0),
    };
  },
  methods: {
    update() {
      this.showContainer = player.break || PlayerProgress.eternityUnlocked();
      this.showEP = PlayerProgress.eternityUnlocked();
      this.eternityPoints.copyFrom(Currency.eternityPoints.value.floor());
      this.showNextEP = Player.canEternity && player.records.thisReality.maxEP.lt(100) &&
        gainedEternityPoints().lt(100);
      if (this.showNextEP) this.nextEP.copyFrom(requiredIPForEP(gainedEternityPoints().floor().toNumber() + 1));
    },
  },
};
</script>

<template>
  <div
    v-if="showContainer"
    class="c-prestige-button-container"
  >
    <UnlockInfinityDimButton />
    <EternityButton />
  </div>
</template>

<style scoped>
.c-eternity-points {
  font-size: 1.2rem;
  padding-bottom: 0.5rem;
}
</style>
