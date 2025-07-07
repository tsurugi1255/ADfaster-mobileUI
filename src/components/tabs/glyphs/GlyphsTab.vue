<script>
import CurrentGlyphEffects from "./CurrentGlyphEffects";
import EquippedGlyphs from "./EquippedGlyphs";
import ExpandingControlBox from "@/components/ExpandingControlBox";
import GlyphInventory from "./GlyphInventory";
import GlyphLevelsAndWeights from "./GlyphLevelsAndWeights";
import GlyphPeek from "./GlyphPeek";
import GlyphTabSidebar from "./sidebar/GlyphTabSidebar";
import RealityAmplifyButton from "./RealityAmplifyButton";
import RealityReminder from "./RealityReminder";
import ResetRealityButton from "./ResetRealityButton";
import SacrificedGlyphs from "./SacrificedGlyphs";
import SingleGlyphCustomzationPanel from "./SingleGlyphCustomzationPanel";

export default {
  name: "GlyphsTab",
  components: {
    ExpandingControlBox,
    GlyphTabSidebar,
    GlyphPeek,
    RealityAmplifyButton,
    GlyphInventory,
    SacrificedGlyphs,
    CurrentGlyphEffects,
    EquippedGlyphs,
    GlyphLevelsAndWeights,
    ResetRealityButton,
    RealityReminder,
    SingleGlyphCustomzationPanel
  },
  data() {
    return {
      enslavedHint: "",
      showInstability: false,
      instabilityThreshold: 0,
      hyperInstabilityThreshold: 0,
      isInCelestialReality: false,
      canAmplify: false,
      glyphTextColors: true,
      autoRestartCelestialRuns: false,
      sacrificeUnlocked: false,
      sacrificeDisplayed: false,
      resetRealityDisplayed: false,
      respec: player.reality.respec,
      undoVisible: false,
      undoAvailable: false,
      respecIntoProtected: player.options.respecIntoProtected,
      undoSlotsAvailable: 0,
    };
  },
  computed: {
    showEnslavedHint() {
      return this.enslavedHint !== "";
    },
    glyphColorState() {
      return {
        "o-glyph-color-checkbox": true,
        "o-glyph-color-checkbox--active": this.glyphTextColors,
        "o-glyph-color-checkbox--inactive": !this.glyphTextColors,
      };
    },
    isDoomed() {
      return Pelle.isDoomed;
    },
    // "Armageddon" causes the button to have text overflow, so we conditionally make the button taller; this doesn't
    // cause container overflow due to another button being removed entirely when doomed
    unequipClass() {
      return {
        "l-glyph-equip-button": this.isDoomed,
        "l-glyph-equip-button-short": !this.isDoomed,
      };
    },
    glyphRespecStyle() {
      if (this.respec) {
        return {
          color: "var(--color-reality-light)",
          "background-color": "var(--color-reality)",
          "border-color": "#094e0b",
          cursor: "pointer",
        };
      }
      return {
        cursor: "pointer",
      };
    },
    unequipText() {
      if (Pelle.isDoomed) return "Unequip Glyphs on Armageddon";
      return "Unequip Glyphs on Reality";
    }
  },
  methods: {
    update() {
      this.respec = player.reality.respec;
      this.undoVisible = TeresaUnlocks.undo.canBeApplied;
      this.resetRealityDisplayed = PlayerProgress.realityUnlocked();
      this.showInstability = player.records.bestReality.glyphLevel > 800;
      this.instabilityThreshold = Glyphs.instabilityThreshold;
      this.hyperInstabilityThreshold = Glyphs.hyperInstabilityThreshold;
      this.isInCelestialReality = isInCelestialReality();
      this.canAmplify = Enslaved.isUnlocked && !this.isInCelestialReality;
      this.autoRestartCelestialRuns = player.options.retryCelestial;
      this.glyphTextColors = player.options.glyphTextColors;
      this.enslavedHint = "";
      this.sacrificeUnlocked = GlyphSacrificeHandler.canSacrifice;
      this.sacrificeDisplayed = player.reality.showGlyphSacrifice;
      this.undoAvailable = this.undoVisible && this.undoSlotsAvailable && player.reality.glyphs.undo.length > 0;
      this.undoSlotsAvailable = this.respecIntoProtected
      this.respecIntoProtected = player.options.respecIntoProtected;
      if (!Enslaved.isRunning) return;
      const haveBoost = Glyphs.activeWithoutCompanion.find(e => e.level < Enslaved.glyphLevelMin) !== undefined;
      if (haveBoost) {
        this.enslavedHint = "done... what little... I can... with Glyphs...";
      }
    },
    toggleAutoRestartCelestial() {
      player.options.retryCelestial = !player.options.retryCelestial;
    },
    toggleGlyphTextColors() {
      player.options.glyphTextColors = !player.options.glyphTextColors;
    },
    glyphInfoClass(isSacrificeOption) {
      return {
        "l-glyph-info-button": true,
        "c-glyph-info-button": true,
        "c-glyph-info-button--active": isSacrificeOption,
        "c-glyph-info-button--inactive": !isSacrificeOption
      };
    },
    setInfoState(state) {
      player.reality.showGlyphSacrifice = state;
    },
    glyphColorPosition() {
      return this.sacrificeUnlocked ? "l-glyph-color-position__low" : "l-glyph-color-position__top";
    },
    glyphInfoBorderClass() {
      return {
        "c-current-glyph-effects-with-top-border": !this.sacrificeUnlocked
      };
    },
    buttonGroupClass() {
      return {
        "l-half-width": this.canAmplify
      };
    },
    toggleRespec() {
      player.reality.respec = !player.reality.respec;
    },
    toggleRespecIntoProtected() {
      player.options.respecIntoProtected = !player.options.respecIntoProtected;
    },
    undo() {
      if (!this.undoAvailable || Pelle.isDoomed) return;
      if (player.options.confirmations.glyphUndo) Modal.glyphUndo.show();
      else Glyphs.undo();
    }
  }
};
</script>

<template>
  <div>
    <div class="l-glyphs-tab">
      <div class="l-glyph-info-wrapper">
        <span
          class="l-glyph-color-box"
          @click="toggleGlyphTextColors"
        >
          <div :class="glyphColorPosition()">
            <label
              :class="glyphColorState"
            >
              <span class="fas fa-palette" />
            </label>
          </div>
        </span>
        <div
          v-if="sacrificeUnlocked"
          class="c-glyph-info-options"
        >
          <button
            :class="glyphInfoClass(!sacrificeDisplayed)"
            @click="setInfoState(false)"
          >
            Current Glyph effects
          </button>
          <button
            :class="glyphInfoClass(sacrificeDisplayed)"
            @click="setInfoState(true)"
          >
            Glyph Sacrifice totals
          </button>
        </div>
        <SacrificedGlyphs v-if="sacrificeUnlocked && sacrificeDisplayed" />
        <CurrentGlyphEffects
          v-else
          :class="glyphInfoBorderClass()"
        />
      </div>
      
      <ExpandingControlBox
        width-source="content"
        label="Glyph Level Factors"
        container-class="c-glyph-level-factors-dropdown-header"
        class="l-glyph-level-factors"
      >
        <template #dropdown>
          <GlyphLevelsAndWeights />
        </template>
      </ExpandingControlBox>
      <div class="l-reality-button-column">
        <div class="l-equipped-glyphs-and-effects-container">
          <GlyphPeek />
          <EquippedGlyphs />
        </div>
        <div class="l-reality-control-buttons">
          <div
            v-if="resetRealityDisplayed"
            class="l-reality-button-group"
          >
            <RealityAmplifyButton
              v-if="!isInCelestialReality"
              :class="buttonGroupClass()"
            />
            <ResetRealityButton :class="buttonGroupClass()" />
          </div>
          <div class="l-equipped-glyphs__buttons">
            <button
              class="c-reality-upgrade-btn"
              :class="unequipClass"
              :style="glyphRespecStyle"
              @click="toggleRespec"
            >
              {{ unequipText }}
            </button>
            <button
              v-if="undoVisible"
              class="l-glyph-equip-button c-reality-upgrade-btn"
              :class="{'c-reality-upgrade-btn--unavailable': !undoAvailable}"
              @click="undo"
            >
              <span>Undo</span>
            </button>
            <button
              class="l-glyph-equip-button c-reality-upgrade-btn"
              @click="toggleRespecIntoProtected"
            >
              Unequip Glyphs to:
              <br>
              <span v-if="respecIntoProtected">Protected slots</span>
              <span v-else>Main inventory</span>
            </button>
            <!-- <button
              class="l-glyph-equip-button-short c-reality-upgrade-btn"
              :class="{'tutorial--glow': cosmeticGlow}"
              @click="showOptionModal"
            >
              Open Glyph Visual Options
            </button> -->
          </div>

          <div
            v-if="isInCelestialReality"
            class="l-celestial-auto-restart-checkbox"
          >
            <input
              id="autoRestart"
              v-model="autoRestartCelestialRuns"
              type="checkbox"
              :value="autoRestartCelestialRuns"
              class="o-clickable"
              @input="toggleAutoRestartCelestial()"
            >
            <label
              for="autoRestart"
              class="o-clickable"
            >
              Repeat this Celestial's Reality
            </label>
          </div>
          <!-- <RealityReminder /> -->
          <!-- <SingleGlyphCustomzationPanel /> -->
        </div>
      </div>
      <div class="l-player-glyphs-column">
        <div
          v-if="showEnslavedHint"
          class="o-teresa-quotes"
          v-html="enslavedHint"
        />
        <GlyphInventory />
      </div>
      <GlyphTabSidebar />
    </div>
  </div>
</template>

<style scoped>
.l-glyph-level-factors {
  width: 80% !important;
  height: auto !important;
  font-size: 2rem;
  margin: 1rem 0;
}

.l-glyph-level-factors .l-expanding-control-box__button {
  margin-bottom: 2rem;
}
.o-clickable {
  cursor: pointer;
}

.l-glyph-color-position__low {
  top: 6rem !important;
  right: 46.5rem !important;
}

.l-celestial-auto-restart-checkbox {
  display: flex;
  flex-direction: row;
  align-items: center;
  user-select: none;
}
</style>
