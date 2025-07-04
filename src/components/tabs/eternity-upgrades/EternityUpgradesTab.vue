<script>
import EPMultiplierButton from "./EPMultiplierButton";
import EternityUpgradeButton from "./EternityUpgradeButton";

export default {
  name: "EternityUpgradesTab",
  components: {
    EternityUpgradeButton,
    EPMultiplierButton
  },
  computed: {
    grid() {
      return [
        [
          EternityUpgrade.idMultEP,
          EternityUpgrade.idMultEternities,
        ],
        [
          EternityUpgrade.idMultICRecords,
          EternityUpgrade.tdMultAchs,
        ],
        [
          EternityUpgrade.tdMultTheorems,
          EternityUpgrade.tdMultRealTime,
        ]
      ];
    },
    costIncreases: () => EternityUpgrade.epMult.costIncreaseThresholds.map(x => new Decimal(x))
  },
  methods: {
    formatPostBreak
  }
};
</script>

<template>
  <div class="l-eternity-upgrades-grid">
    <div class="l-eternity-mult-button-info">
      The cost for the {{ formatX(5) }} multiplier jumps at {{ format(costIncreases[0]) }},
      {{ formatPostBreak(costIncreases[1], 2) }}, and {{ formatPostBreak(costIncreases[2]) }} Eternity Points.
      <br>
      The cost increases super-exponentially after {{ formatPostBreak(costIncreases[3]) }} Eternity Points.
    </div>
    <EPMultiplierButton />
    <div
      v-for="(row, i) in grid"
      :key="i"
      class="l-eternity-upgrades-grid__row"
    >
      <EternityUpgradeButton
        v-for="upgrade in row"
        :key="upgrade.id"
        :upgrade="upgrade"
        class="l-eternity-upgrades-grid__cell"
      />
    </div>
  </div>
</template>

<style scoped>
.l-eternity-upgrades-grid {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 1rem;
  width: 95%;
}

.l-eternity-mult-button-info {
  font-size: 1.7rem;
  width: 50%;
}

.l-eternity-upgrades-grid > .l-spoon-btn-group {
  max-width: 50%;
  width: auto;
  margin: 2rem 0;
}

.l-eternity-upgrades-grid__row {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(0, 1fr));
  column-gap: 1rem;
  margin-bottom: 1rem;
  width: 100%;
}

.l-eternity-upgrades-grid__cell {
  font-size: 2rem;
}
</style>
