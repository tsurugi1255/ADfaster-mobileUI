<script>
import PrimaryButton from "@/components/PrimaryButton";
import PrimaryToggleButton from "@/components/PrimaryToggleButton";
import TimeStudySaveLoadButton from "./TimeStudySaveLoadButton";
import TimeTheoremBuyButton from "./TimeTheoremBuyButton";

export default {
  name: "TimeTheoremShop",
  components: {
    PrimaryButton,
    PrimaryToggleButton,
    TimeTheoremBuyButton,
    TimeStudySaveLoadButton
  },
  data() {
    return {
      theoremAmount: new Decimal(0),
      theoremGeneration: new Decimal(0),
      totalTimeTheorems: new Decimal(0),
      shopMinimized: false,
      minimizeAvailable: false,
      hasTTAutobuyer: false,
      isAutobuyerOn: false,
      budget: {
        am: new Decimal(0),
        ip: new Decimal(0),
        ep: new Decimal(0)
      },
      costs: {
        am: new Decimal(0),
        ip: new Decimal(0),
        ep: new Decimal(0)
      },
      showST: false,
      STamount: 0,
      hasTTGen: false,
      showTTGen: false,
      invertTTgenDisplay: false,
      respec: player.respec,
    };
  },
  computed: {
    minimized() {
      return this.minimizeAvailable && this.shopMinimized;
    },
    formatTimeTheoremType() {
      if (this.theoremAmount.gte(1e6)) {
        return format;
      }
      if (!(Teresa.isRunning || Enslaved.isRunning) &&
        getAdjustedGlyphEffect("dilationTTgen") > 0 && !DilationUpgrade.ttGenerator.isBought) {
        return formatFloat;
      }
      return formatInt;
    },
    TTgenRateText() {
      if (this.theoremGeneration.lt(1 / 3600)) {
        return `one TT every ${TimeSpan.fromSeconds(
          this.theoremGeneration.reciprocal().toNumber()).toStringShort(false)}`;
      }
      if (this.theoremGeneration.lt(0.1)) {
        return `${format(this.theoremGeneration.times(3600), 2, 2)} TT/hour`;
      }
      return `${format(this.theoremGeneration, 2, 2)} TT/sec`;
    },
    totalTimeTheoremText() {
      return `${quantify("total Time Theorem", this.totalTimeTheorems, 2, 2, this.formatTimeTheoremType)}`;
    },
    minimizeArrowStyle() {
      return {
        transform: this.minimized ? "rotate(-180deg)" : "",
        transition: "all 0.25s ease-out"
      };
    },
    saveLoadText() {
      return this.$viewModel.shiftDown ? "Save:" : "Load:";
    },
    shopBottomRowHeightStyle() {
      return {
        height: this.hasTTAutobuyer ? "6.7rem" : "4.4rem",
      };
    },
    respecClassObject() {
      return {
        // "o-primary-btn--subtab-option": true,
        "o-primary-btn--respec-active": this.respec
      };
    }
  },
  watch: {
    isAutobuyerOn(newValue) {
      Autobuyer.timeTheorem.isActive = newValue;
    },
    invertTTgenDisplay(newValue) {
      player.options.invertTTgenDisplay = newValue;
    },
    respec(newValue) {
      player.respec = newValue;
    }
  },
  methods: {
    minimize() {
      player.timestudy.shopMinimized = !player.timestudy.shopMinimized;
    },
    formatAM(am) {
      return `${format(am)} AM`;
    },
    buyWithAM() {
      TimeTheorems.buyOne(false, "am");
    },
    formatIP(ip) {
      return `${format(ip)} IP`;
    },
    buyWithIP() {
      TimeTheorems.buyOne(false, "ip");
    },
    formatEP(ep) {
      return `${format(ep, 2, 0)} EP`;
    },
    buyWithEP() {
      TimeTheorems.buyOne(false, "ep");
    },
    buyMaxTheorems() {
      TimeTheorems.buyMax(false);
    },
    update() {
      this.respec = player.respec;
      this.theoremAmount.copyFrom(Currency.timeTheorems);
      this.theoremGeneration.copyFrom(getTTPerSecond().times(getGameSpeedupForDisplay()));
      this.totalTimeTheorems.copyFrom(Currency.timeTheorems.max);
      this.shopMinimized = player.timestudy.shopMinimized;
      this.hasTTAutobuyer = Autobuyer.timeTheorem.isUnlocked;
      this.isAutobuyerOn = Autobuyer.timeTheorem.isActive;
      this.minimizeAvailable = DilationUpgrade.ttGenerator.isBought || this.hasTTAutobuyer;
      const budget = this.budget;
      budget.am.copyFrom(TimeTheoremPurchaseType.am.currency);
      budget.ip.copyFrom(TimeTheoremPurchaseType.ip.currency);
      budget.ep.copyFrom(TimeTheoremPurchaseType.ep.currency);
      const costs = this.costs;
      costs.am.copyFrom(TimeTheoremPurchaseType.am.cost);
      costs.ip.copyFrom(TimeTheoremPurchaseType.ip.cost);
      costs.ep.copyFrom(TimeTheoremPurchaseType.ep.cost);
      this.showST = V.spaceTheorems > 0 && !Pelle.isDoomed;
      this.STamount = V.availableST;
      this.hasTTGen = this.theoremGeneration.gt(0);
      this.showTTGen = this.hasTTGen && (ui.view.shiftDown === this.invertTTgenDisplay);
      this.invertTTgenDisplay = player.options.invertTTgenDisplay;
    },
    toggleTTgen() {
      this.invertTTgenDisplay = !this.invertTTgenDisplay;
    },
    exportStudyTree() {
      if (player.timestudy.studies.length === 0) {
        GameUI.notify.error("You cannot export an empty Time Study Tree!");
      } else {
        copyToClipboard(GameCache.currentStudyTree.value.exportString);
        GameUI.notify.info("Exported current Time Studies to your clipboard");
      }
    }
  },
};
</script>

<template>
  <div class="time-theorem-buttons">
    <div class="ttshop-container ttshop-background">
      <div
        data-role="page"
        class="ttbuttons-row ttbuttons-top-row"
      >
        <p class="timetheorems">
          <span class="c-tt-amount">
            You have {{ quantify("Time Theorem", theoremAmount, 2, 0, formatTimeTheoremType) }}
          </span>
          <span v-if="showST">
            <br>
            You have {{ quantifyInt("Space Theorem", STamount) }}
          </span>
        </p>
        <div class="tt-gen-container">
          <span
            v-if="hasTTGen"
            class="checkbox-margin"
            ach-tooltip="This shows TT generation by default and total TT if you hold shift.
              Check this box to swap this behavior."
          >
          </span>
          <span v-if="showTTGen">
            You are gaining {{ TTgenRateText }}.
          </span>
          <span v-else>
            You have {{ totalTimeTheoremText }}.
          </span>
        </div>

        <div
          class="ttbuttons-row"
          :style="shopBottomRowHeightStyle"
        >
          <div class="tt-max-auto-btn-grp">
            <button
              class="o-tt-top-row-button c-tt-buy-button c-tt-buy-button--unlocked"
              @click="buyMaxTheorems"
            >
              Buy max
            </button>
            <PrimaryToggleButton
              v-if="hasTTAutobuyer"
              v-model="isAutobuyerOn"
              class="o-tt-autobuyer-button c-tt-buy-button c-tt-buy-button--unlocked"
              label="Auto:"
            />
          </div>
          
          <div class="tt-buy-buttons">
            <TimeTheoremBuyButton
              :budget="budget.am"
              :cost="costs.am"
              :format-cost="formatAM"
              :action="buyWithAM"
            />
            <TimeTheoremBuyButton
              :budget="budget.ip"
              :cost="costs.ip"
              :format-cost="formatIP"
              :action="buyWithIP"
            />
            <TimeTheoremBuyButton
              :budget="budget.ep"
              :cost="costs.ep"
              :format-cost="formatEP"
              :action="buyWithEP"
            />
          </div>
        </div>
        <div class="l-tt-buy-max-vbox">
          <button
            class="o-tt-top-row-button c-tt-buy-button c-tt-buy-button--unlocked"
            @click="exportStudyTree"
          >
            Export Tree
          </button>
          <button
            class="o-tt-top-row-button  c-tt-buy-button c-tt-buy-button--unlocked"
            onClick="Modal.preferredTree.show()"
          >
            Select Preferred Path
          </button>
          <button
            class="o-tt-top-row-button c-tt-buy-button c-tt-buy-button--unlocked"
            onclick="Modal.studyString.show({ id: -1 })"
          >
            Import Tree
          </button>
        </div>
        <div class="l-load-tree-area">
          <div class="l-tt-buy-max-vbox">
            <button
              class="o-tt-top-row-button  c-tt-buy-button c-tt-buy-button--unlocked"
              :class="respecClassObject"
              @click="respec = !respec"
            >
              Respec on next Eternity
            </button>
          </div>
          <div class="l-tree-load-button-wrapper">
            <TimeStudySaveLoadButton
              v-for="saveslot in 6"
              :key="saveslot"
              :saveslot="saveslot"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>

.tt-max-auto-btn-grp {
  display: flex;
  flex-direction: row;
  justify-content: center;
}

.tt-max-auto-btn-grp * {
  margin: 3rem 0.5rem 1rem;
  min-width: 15rem;
}
.l-load-tree-area {
  display: flex;
  flex-direction: column;
  width: 100%;
  align-items: center;
  margin-bottom: 5rem;
}

.l-tree-load-button-wrapper {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(0, 1fr));
  width: 100%;
  column-gap: 1rem;
}

.ttbuttons-bottom-row-hide {
  height: 0;
}

.tt-gen-container {
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  align-items: center;
  font-size: 2rem;
}

.checkbox-margin {
  margin: 0 0.4rem;
}

.tt-buy-buttons {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(0, 1fr));
  width: 100%;
  column-gap: 1rem;
  margin-bottom: 1rem;
}
</style>
