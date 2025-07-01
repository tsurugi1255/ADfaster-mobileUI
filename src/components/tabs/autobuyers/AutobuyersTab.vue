<script>
import AutobuyerToggles from "./AutobuyerToggles";
import BigCrunchAutobuyerBox from "./BigCrunchAutobuyerBox";
import DimensionAutobuyerBox from "./DimensionAutobuyerBox";
import DimensionBoostAutobuyerBox from "./DimensionBoostAutobuyerBox";
import EternityAutobuyerBox from "./EternityAutobuyerBox";
import GalaxyAutobuyerBox from "./GalaxyAutobuyerBox";
import OpenModalHotkeysButton from "@/components/OpenModalHotkeysButton";
import RealityAutobuyerBox from "./RealityAutobuyerBox";
import SimpleAutobuyersMultiBox from "./SimpleAutobuyersMultiBox";
import TickspeedAutobuyerBox from "./TickspeedAutobuyerBox";
import MultipleSingleAutobuyersGroup from "./MultipleSingleAutobuyersGroup";

export default {
  name: "AutobuyersTab",
  components: {
    AutobuyerToggles,
    OpenModalHotkeysButton,
    RealityAutobuyerBox,
    EternityAutobuyerBox,
    BigCrunchAutobuyerBox,
    GalaxyAutobuyerBox,
    DimensionBoostAutobuyerBox,
    TickspeedAutobuyerBox,
    DimensionAutobuyerBox,
    SimpleAutobuyersMultiBox,
    MultipleSingleAutobuyersGroup
  },
  data() {
    return {
      hasInfinity: false,
      hasContinuum: false,
      displayADAutobuyersIndividually: false,
      hasInstant: false,
    };
  },
  computed: {
    // It only makes sense to show this if the player has seen gamespeed-altering effects, but we should keep it there
    // permanently as soon as they have
    hasSeenGamespeedAlteringEffects() {
      return PlayerProgress.seenAlteredSpeed();
    },
    gameTickLength() {
      return `${formatInt(player.options.updateRate)} ms`;
    }
  },
  methods: {
    update() {
      this.hasInfinity = PlayerProgress.infinityUnlocked();
      this.hasContinuum = Laitela.continuumActive;
      this.checkADAutoStatus();
    },
    checkADAutoStatus() {
      const ad = Autobuyer.antimatterDimension;
      // Since you don't need to buy autobuyers in Doomed and unbought ones are hidden, we can check if only the
      // autobuyers you can see (ie, have unlocked) have been maxed.
      if (Pelle.isDoomed) {
        this.displayADAutobuyersIndividually = !ad.zeroIndexed.filter(x => x.isUnlocked)
          .every(x => x.hasUnlimitedBulk && x.hasMaxedInterval);
        return;
      }
      this.hasInstant = ad.hasInstant;
      this.displayADAutobuyersIndividually = !ad.collapseDisplay;
    },
  }
};
</script>

<template>
  <div class="l-autobuyers-tab">
    <div class="autobuyer-header">
      <AutobuyerToggles />
      <OpenModalHotkeysButton />
      <div v-if="hasSeenGamespeedAlteringEffects" class="autobuyer-info">
        Autobuyer intervals and time-based settings are always <b>real time</b> and therefore
        <br>
        unaffected by anything which may alter how fast the game itself is running.
        <br>
        <br>
      </div>
      <div v-if="!hasInfinity" class="autobuyer-info">
        Challenges for upgrading autobuyers are unlocked by reaching Infinity.
      </div>
      <b class="autobuyer-info">Autobuyers with no displayed bulk have unlimited bulk by default.</b>
      <b class="autobuyer-info">
        Antimatter Dimension Autobuyers can have their bulk upgraded once interval is below {{ formatInt(100) }} ms.
      </b>
      <b v-if="hasInstant" class="autobuyer-info">Autobuyers with "Instant" interval will trigger every game tick ({{ gameTickLength }}).</b>
    </div>
    <RealityAutobuyerBox class="c-reality-pos" />
    <EternityAutobuyerBox class="c-eternity-pos" />
    <BigCrunchAutobuyerBox class="c-infinity-pos" />
    <GalaxyAutobuyerBox />
    <DimensionBoostAutobuyerBox />
    <TickspeedAutobuyerBox v-if="!hasContinuum" class="c-tickspeed-pos" />
    <MultipleSingleAutobuyersGroup v-if="!hasContinuum" />
    <MultipleSingleAutobuyersGroup v-if="hasContinuum" class="l-autobuyer-singlet-group__continuum" />
    <template v-if="displayADAutobuyersIndividually">
      <DimensionAutobuyerBox
        v-for="tier in 8"
        :key="tier"
        :tier="tier"
      />
    </template>
    
    <SimpleAutobuyersMultiBox class="c-additional-autobuyers" />
  </div>
</template>

<style scoped>
/* This is necessary for the ExpandingControlBox within these components to render in the right stacking order
when they're open. It looks slightly hacky but actually can't be done any other way; each AutobuyerBox creates
its own stacking context, which means that all z-indices specified within are essentially scoped and the
AutobuyerBox components will always render in page order regardless of internal z-indices without these. */
.c-reality-pos {
  z-index: 3;
  grid-column: 1/-1;
}

.c-eternity-pos {
  z-index: 2;
}

.c-infinity-pos {
  z-index: 1;
}

/* .c-tickspeed-pos {
  grid-column: 1/-1;
} */
</style>
