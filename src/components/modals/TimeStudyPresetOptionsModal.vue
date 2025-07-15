<script>
import ModalWrapper from "@/components/modals/ModalWrapper";

export default {
  name: "TimeStudyPresetOptionsModal",
  components: {
    ModalWrapper,
  },
  data() {
    return {
      presets: JSON.parse(JSON.stringify(player.timestudy.presets))
    };
  },
  methods: {
    edit(saveslot) {
      this.emitClose();
      Modal.studyString.show({ id: saveslot });
    },
    handleExport(preset, saveslot) {
      copyToClipboard(preset.studies);
      const presetName = preset.name ? `Study preset "${preset.name}"` : "Study preset";
      GameUI.notify.eternity(`${presetName} exported from slot ${saveslot} to your clipboard`);
      this.emitClose();
    },
    save(preset, saveslot) {
      preset.studies = GameCache.currentStudyTree.value.exportString;
      const presetName = preset.name ? `Study preset "${preset.name}"` : "Study preset";
      GameUI.notify.eternity(`${presetName} saved in slot ${saveslot+1}`);
      this.emitClose();
    },
    load(preset, saveslot) {
      if (preset.studies) {
        // We need to use a combined tree for committing to the game state, or else it won't buy studies in the imported
        // tree are only reachable if the current tree is already bought
        const combinedTree = new TimeStudyTree();
        combinedTree.attemptBuyArray(TimeStudyTree.currentStudies, false);
        combinedTree.attemptBuyArray(combinedTree.parseStudyImport(preset.studies), true);
        TimeStudyTree.commitToGameState(combinedTree.purchasedStudies, false, combinedTree.startEC);

        const presetName = preset.name ? `Study preset "${preset.name}"` : "Study preset";
        GameUI.notify.eternity(`${presetName} loaded from slot ${saveslot+1}`);
      } else {
        Modal.message.show("This Time Study list currently contains no Time Studies.");
      }
      this.emitClose();
    },
    respecAndLoad(preset) {
      if (Player.canEternity) {
        player.respec = true;
        const newTree = new TimeStudyTree();
        newTree.attemptBuyArray(newTree.parseStudyImport(preset.studies));
        animateAndEternity(() => TimeStudyTree.commitToGameState(newTree.purchasedStudies, false, newTree.startEC));
      }
      this.emitClose();
    },
    deletePreset(preset, saveslot) {
      if (preset.studies) Modal.studyString.show({ id: saveslot, deleting: true });
      else Modal.message.show("This Time Study list currently contains no Time Studies.");
      this.emitClose();
    },
  },
};
</script>

<template>
  <ModalWrapper>
    <template #header>
      Time Study Presets
    </template>
    <div>
      Modify your time study presets below.
    </div>
    <div class="time-study-presets">
      <div 
        class="time-study-preset-option"
        v-for="(preset, index) in presets"
      >
        <div class="preset-header">Slot {{ index+1 }}</div>
        <input 
          type="text"
          size="4"
          maxlength="4"
          class="l-tt-save-load-btn__menu-rename c-tt-save-load-btn__menu-rename"
          :value="preset.name" 
        />
        <div
          class="l-tt-save-load-btn__menu-item c-tt-save-load-btn__menu-item"
          @click="edit(index)"
        >
          Edit
        </div>
        <div
          class="l-tt-save-load-btn__menu-item c-tt-save-load-btn__menu-item"
          @click="handleExport(preset, index)"
        >
          Export
        </div>
        <div
          class="l-tt-save-load-btn__menu-item c-tt-save-load-btn__menu-item"
          @click="save(preset, index)"
        >
          Save
        </div>
        <div class="l-tt-save-load-btn__menu-item">
          <div
            class="c-tt-save-load-btn__menu-item"
            @click="load(preset, index)"
          >
            Load
          </div>
          <div class="c-tt-save-load-btn__menu-item__hover-options">
            <div
              :class="{
                'c-tt-save-load-btn__menu-item__hover-option': true,
                'c-tt-save-load-btn__menu-item__hover-option--disabled': !canEternity,
              }"
              @click="respecAndLoad(preset)"
            >
              Respec and Load
            </div>
          </div>
        </div>
        <div
          class="l-tt-save-load-btn__menu-item c-tt-save-load-btn__menu-item"
          @click="deletePreset(preset, index)"
        >
          Delete
        </div>
      </div>
    </div>
  </ModalWrapper>
</template>

<style scoped>
.time-study-presets {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
  width: 100%;
  padding: 1rem;
}

.time-study-preset-option {
  border: 1px solid gray;
  padding: 1rem;
  border-radius: 1rem;
}

.preset-header {
  text-align: left;
  margin: 1rem 0;
}

.c-tt-save-load-btn__menu-item,
.c-tt-save-load-btn__menu-item__hover-option {
  font-size: 2.3rem;
  padding: 0.5rem 0;
  border-bottom: 1px solid rgb(94, 94, 94);
}
</style>
