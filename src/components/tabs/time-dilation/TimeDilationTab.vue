<script>
import { buyDilationUpgrade } from "../../../core/dilation";
import DilationButton from "./DilationButton";
import DilationUpgradeButton from "./DilationUpgradeButton";
import HoldableButton from "@/components/HoldableButton.vue";

export default {
  name: "TimeDilationTab",
  components: {
    DilationButton,
    DilationUpgradeButton,
    HoldableButton
  },
  data() {
    return {
      tachyons: new Decimal(),
      dilatedTime: new Decimal(),
      dilatedTimeIncome: new Decimal(),
      galaxyThreshold: new Decimal(),
      baseGalaxies: 0,
      totalGalaxies: 0,
      tachyonGalaxyGain: 1,
      hasPelleDilationUpgrades: false,
      galaxyTimeEstimate: "",
      maxDT: new Decimal(),
      toMaxTooltip: "",
      isHovering: false,
    };
  },
  computed: {
    rebuyables() {
      return [
        DilationUpgrade.dtGain,
        DilationUpgrade.galaxyThreshold,
        DilationUpgrade.tachyonGain
      ];
    },
    upgrades() {
      return [
        [
          DilationUpgrade.doubleGalaxies,
          DilationUpgrade.ndMultDT,
        ],
        [
          DilationUpgrade.tdMultReplicanti,
          DilationUpgrade.timeStudySplit,
        ],
        [
          DilationUpgrade.dilationPenalty,
          DilationUpgrade.ipMultDT,
        ],
      ];
    },
    // This might be negative due to rift drain, so we need to add "+" iff the value is positive. The actual
    // addition of a negative sign (or not) is assumed to be handled in a notation-specific way
    dilatedTimeGainText() {
      const sign = this.dilatedTimeIncome.gte(0) ? "+" : "";
      return `${sign}${format(this.dilatedTimeIncome, 2, 1)}`;
    },
    pelleRebuyables() {
      return [
        DilationUpgrade.dtGainPelle,
        DilationUpgrade.galaxyMultiplier,
        DilationUpgrade.tickspeedPower
      ];
    },
    pelleUpgrades() {
      return [
        DilationUpgrade.galaxyThresholdPelle,
        DilationUpgrade.flatDilationMult
      ];
    },
    ttGenerator() {
      return DilationUpgrade.ttGenerator;
    },
    baseGalaxyText() {
      return `${formatInt(this.baseGalaxies)} Base`;
    },
    hasMaxText: () => PlayerProgress.realityUnlocked() && !Pelle.isDoomed,
    allRebuyables() {
      const upgradeRows = [];
      upgradeRows.push(this.rebuyables);
      if (this.hasPelleDilationUpgrades) upgradeRows.push(this.pelleRebuyables);
      return upgradeRows;
    },
    allRebuyablesUpgradeOrder() {
      if(this.hasPelleDilationUpgrades) {
        return [
          DilationUpgrade.dtGainPelle.id,
          DilationUpgrade.dtGain.id,
          DilationUpgrade.galaxyMultiplier.id,
          DilationUpgrade.galaxyThreshold.id,
          DilationUpgrade.tickspeedPower.id,
        ];
      } else {
        return [
          DilationUpgrade.tachyonGain.id,
          DilationUpgrade.dtGain.id,
          DilationUpgrade.galaxyThreshold.id
        ];
      }
    },
    allSingleUpgrades() {
      const upgradeRows = [];
      upgradeRows.push(...this.upgrades);
      if (this.hasPelleDilationUpgrades) upgradeRows.push(this.pelleUpgrades);
      upgradeRows.push([this.ttGenerator]);
      return upgradeRows;
    },
  },
  methods: {
    update() {
      this.tachyons.copyFrom(Currency.tachyonParticles);
      this.dilatedTime.copyFrom(Currency.dilatedTime);
      const rawDTGain = getDilationGainPerSecond().times(getGameSpeedupForDisplay());
      this.galaxyTimeEstimate = getDilationTimeEstimate(this.galaxyThreshold);
      if (PelleRifts.paradox.isActive) {
        // The number can be small and either positive or negative with the rift active, which means that extra care
        // needs to be taken to get the calculation as close to correct as possible. This relies on some details
        // related to tick microstructure to make things accurate, and it seems to be to roughly 1 part in 5e6
        const tickProp = player.options.updateRate / 1000;
        const drainFactorPerTick = 1 - (1 - Pelle.riftDrainPercent) ** tickProp;
        const drainPerSecond = this.dilatedTime.add(rawDTGain.times(tickProp)).times(drainFactorPerTick / tickProp);
        this.dilatedTimeIncome = rawDTGain.minus(drainPerSecond);
      } else {
        this.dilatedTimeIncome = rawDTGain;
      }
      this.galaxyThreshold.copyFrom(player.dilation.nextThreshold);
      this.baseGalaxies = player.dilation.baseTachyonGalaxies;
      this.totalGalaxies = player.dilation.totalTachyonGalaxies;
      this.hasPelleDilationUpgrades = PelleRifts.paradox.milestones[0].canBeApplied;
      if (this.baseGalaxies < 500 && DilationUpgrade.doubleGalaxies.isBought) {
        this.tachyonGalaxyGain = DilationUpgrade.doubleGalaxies.effectValue;
      } else {
        this.tachyonGalaxyGain = 1;
      }
      this.tachyonGalaxyGain *= DilationUpgrade.galaxyMultiplier.effectValue;
      this.maxDT.copyFrom(player.records.thisReality.maxDT);

      const estimateText = getDilationTimeEstimate(this.maxDT);
      if (this.dilatedTimeIncome.lte(0)) this.toMaxTooltip = "No DT gain";
      else this.toMaxTooltip = estimateText.startsWith("<") ? "Currently Increasing" : estimateText;
    },
    buyAllRebuyables() {
      for(const id of this.allRebuyablesUpgradeOrder) {
        buyDilationUpgrade(id, 1000);
      }
    }
  }
};
</script>

<template>
  <div class="l-dilation-tab">
    <span>
      You have
      <span class="c-dilation-tab__tachyons">{{ format(tachyons, 2, 1) }}</span>
      {{ pluralize("Tachyon Particle", tachyons) }}.
    </span>
    <div
      @mouseover="isHovering = true"
      @mouseleave="isHovering = false"
      class="l-dilation-btn-container"
    >
      <DilationButton />
    </div>
    <span>
      You have
      <span class="c-dilation-tab__dilated-time">{{ format(dilatedTime, 2, 1) }}</span>
      Dilated Time.
      <span class="c-dilation-tab__dilated-time-income">{{ dilatedTimeGainText }}/s</span>
    </span>
    <span>
      Next
      <span v-if="tachyonGalaxyGain > 1">{{ formatInt(tachyonGalaxyGain) }}</span>
      {{ pluralize("Tachyon Galaxy", tachyonGalaxyGain) }} at
      <span
        class="c-dilation-tab__galaxy-threshold"
        :ach-tooltip="galaxyTimeEstimate"
      >{{ format(galaxyThreshold, 2, 1) }}</span>
      Dilated Time, gained total of
      <span
        class="c-dilation-tab__galaxies"
        :ach-tooltip="baseGalaxyText"
      >{{ formatInt(totalGalaxies) }}</span>
      {{ pluralize("Tachyon Galaxy", totalGalaxies) }}
    </span>
    <span v-if="hasMaxText">
      Your maximum Dilated Time reached this Reality is
      <span
        v-tooltip="toMaxTooltip"
        class="max-accent"
      >{{ format(maxDT, 2, 1) }}</span>.
    </span>
    <HoldableButton 
      className="maxButton" 
      onHoldClass="maxButtonPressed"
      :onHoldFunction="buyAllRebuyables
    ">
      Max All Rebuyables
    </HoldableButton>
    <div class="l-dilation-upgrades-grid">
      <div
        v-for="(upgradeRow, row) in allRebuyables"
        :key="'rebuyable' + row"
        class="l-dilation-rebuyables-grid__row"
      >
        <DilationUpgradeButton
          v-for="upgrade in upgradeRow"
          :key="upgrade.id"
          :upgrade="upgrade"
          :is-rebuyable="true"
          class="l-dilation-upgrades-grid__cell"
          :show-tooltip="isHovering"
        />
      </div>
      <div
        v-for="(upgradeRow, row) in allSingleUpgrades"
        :key="'single' + row"
        class="l-dilation-upgrades-grid__row"
      >
        <DilationUpgradeButton
          v-for="upgrade in upgradeRow"
          :key="upgrade.id"
          :upgrade="upgrade"
          :is-rebuyable="false"
          class="l-dilation-upgrades-grid__cell"
          :show-tooltip="isHovering"
        />
      </div>
    </div>
  </div>
</template>

<style scoped>
.max-accent {
  color: var(--color-dilation);
  font-size: 1.5rem;
  text-shadow: 0 0 0.2rem var(--color-reality-dark);
  cursor: default;
}

.maxButton {
  font-size: 2rem;
  color: var(--color-text);
  background-color: var(--color-base);
  width: calc(50% - 1rem);
  padding: 1rem;
  font-family: Typewriter, serif;
  border: 0.1rem solid var(--color-good-dark);
  border-radius: var(--var-border-radius, 0.4rem);
  margin-top: 2rem;
  pointer-events: all;
  cursor: pointer;
  transition: .2s;
}

.maxButtonPressed {
  color: var(--color-text-inverted);
  background-color: var(--color-good);
}

.l-dilation-upgrades-grid {
  display: flex;
  flex-direction: column;
  margin-top: 1rem;
  width: 100%;
}

.l-dilation-rebuyables-grid__row {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
  margin-bottom: 3rem;
}

.l-dilation-rebuyables-grid__row > div:first-child,
.l-dilation-upgrades-grid__row:last-child > div {
  grid-column: 1/-1;
  justify-self: center;
  width: calc(50% - 0.5rem);
}

.l-dilation-upgrades-grid__row {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
  margin-bottom: 1rem;
}
</style>
