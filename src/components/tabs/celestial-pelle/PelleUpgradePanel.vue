<script>
import ArmageddonButton from "./ArmageddonButton";
import PelleUpgradeVue from "./PelleUpgrade";
import RemnantGainFactor from "./RemnantGainFactor";

export default {
  name: "PelleUpgradePanel",
  components: {
    ArmageddonButton,
    PelleUpgradeVue,
    RemnantGainFactor,
  },
  data() {
    return {
      showBought: false,
      isCollapsed: false,
      isHovering: false,
      remnants: 0,
      realityShards: new Decimal(0),
      shardRate: new Decimal(0),
      upgrades: [],
      boughtUpgrades: []
    };
  },
  computed: {
    collapseIcon() {
      return this.isCollapsed
        ? "fas fa-expand-arrows-alt"
        : "fas fa-compress-arrows-alt";
    },
    rebuyables: () => PelleUpgrade.rebuyables,
    visibleUpgrades() { return this.upgrades.slice(0, 5); },
    fadedUpgrades() { return this.upgrades.slice(5, 10); },
    allUpgrades() {
      let upgrades = [];
      if (this.showBought) upgrades = this.boughtUpgrades;
      upgrades = upgrades.concat(this.visibleUpgrades);
      return upgrades;
    },
    showImprovedEstimate() {
      return this.isHovering && !this.shardRate.eq(0);
    }
  },
  methods: {
    update() {
      this.showBought = Pelle.cel.showBought;
      this.isCollapsed = player.celestials.pelle.collapsed.upgrades;
      this.remnants = Pelle.cel.remnants;
      this.realityShards.copyFrom(Pelle.cel.realityShards);
      this.shardRate.copyFrom(Pelle.realityShardGainPerSecond);
      this.upgrades = PelleUpgrade.singles.filter(u => !u.isBought);
      this.boughtUpgrades = PelleUpgrade.singles.filter(u => u.isBought);
    },
    toggleBought() {
      Pelle.cel.showBought = !Pelle.cel.showBought;
      this.$recompute("upgrades");
    },
    toggleCollapse() {
      player.celestials.pelle.collapsed.upgrades = !this.isCollapsed;
    }
  }
};
</script>

<template>
  <div class="l-pelle-panel-container">
    <div class="c-pelle-panel-title">
      
      Pelle Upgrades
      
      <RemnantGainFactor :hide="showImprovedEstimate" />
    </div>
    <div
      v-if="!isCollapsed"
      class="l-pelle-content-container"
    >
      <div class="c-armageddon-container">
        <div class="c-armageddon-container-inner">
          <div class="c-armageddon-resources-container">
            <div>
              You have <span class="c-remnants-amount">{{ format(remnants, 2) }}</span> Remnants.
            </div>
            <div>
              You have <span class="c-remnants-amount">{{ format(realityShards, 2) }}</span> Reality Shards.
              <span class="c-remnants-amount">+{{ format(shardRate, 2, 2) }}/s</span>
            </div>
          </div>
          <div
            class="c-armageddon-button-container"
            @mouseover="isHovering = true"
            @mouseleave="isHovering = false"
          >
            <ArmageddonButton />
          </div>
        </div>
      </div>
      <div class="c-pelle-rebuyable-upgrade-container">
        <PelleUpgradeVue
          v-for="upgrade in rebuyables"
          :key="upgrade.config.id"
          :upgrade="upgrade"
          :show-improved-estimate="showImprovedEstimate"
        />
      </div>
      <button
        class="o-pelle-button"
        @click="toggleBought"
      >
        {{ showBought ? "Showing bought upgrades" : "Bought upgrades hidden" }}
      </button>
      <div
        v-if="allUpgrades.length"
        class="c-pelle-upgrade-container c-pelle-single-upgrade-container"
      >
        <PelleUpgradeVue
          v-for="upgrade in allUpgrades"
          :key="upgrade.config.id"
          :upgrade="upgrade"
          :show-improved-estimate="showImprovedEstimate"
        />
        <PelleUpgradeVue
          v-for="upgrade in fadedUpgrades"
          :key="upgrade.config.id"
          :upgrade="upgrade"
          faded
        />
      </div>
      <div v-else>
        No upgrades to show!
      </div>
    </div>
  </div>
</template>

<style scoped>
.c-collapse-icon-clickable {
  position: absolute;
  top: 50%;
  left: 1.5rem;
  width: 3rem;
  align-content: center;
  transform: translateY(-50%);
  cursor: pointer;
}

.o-pelle-button {
  font-family: Typewriter;
  font-size: 2rem;
  color: var(--color-text);
  background: var(--color-text-inverted);
  border: 0.1rem solid var(--color-pelle--base);
  border-radius: var(--var-border-radius, 0.5rem);
  margin: 1rem 0 0.5rem;
  padding: 1rem;
  transition-duration: 0.12s;
  cursor: pointer;
}

.o-pelle-button:hover {
  box-shadow: 0.1rem 0.1rem 0.3rem var(--color-pelle--base);
}

.c-pelle-rebuyable-upgrade-container {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
  max-width: 95%;
  margin: 2rem 0;
}

.c-pelle-upgrade-container {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
  max-width: 95%;
  margin: 2rem 0;
}

.c-pelle-upgrade-container > .c-pelle-upgrade {
  width: calc(50% - 0.5rem);
  margin-top: 1rem;
}

.c-pelle-upgrade-container > .c-pelle-upgrade:nth-child(odd) {
  margin-right: 0.5rem;
}

.c-pelle-upgrade-container > .c-pelle-upgrade:nth-child(even) {
  margin-left: 0.5rem;
}

.c-pelle-rebuyable-upgrade-container > .c-pelle-upgrade:first-child {
  grid-column: 1 / -1;
  width: calc(50% - 0.5rem);
  justify-self: center;
}

.c-armageddon-container {
  width: 70%;
  border: var(--var-border-width, 0.2rem) solid var(--color-pelle--base);
  border-radius: var(--var-border-radius, 0.5rem);
  padding: 1rem;
}

.c-armageddon-container-inner {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.c-armageddon-button-container {
  width: 80%;
  height: fit-content;
  margin: 2rem 0;
}

.c-armageddon-resources-container {
  width: 100%;
  font-size: 2rem;
}

.c-remnants-amount {
  font-size: 2.5rem;
  font-weight: bold;
  color: var(--color-pelle--base);
}
</style>
