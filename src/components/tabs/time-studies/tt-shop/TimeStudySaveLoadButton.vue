<script>
import HoverMenu from "./HoverMenu";

export default {
  name: "TimeStudySaveLoadButton",
  components: {
    HoverMenu,
  },
  props: {
    saveslot: {
      type: Number,
      required: true
    }
  },
  data() {
    return {
      name: "",
      displayName: "",
      canEternity: false
    };
  },
  computed: {
    preset() {
      return player.timestudy.presets[this.saveslot - 1];
    },
  },
  methods: {
    update() {
      this.name = player.timestudy.presets[this.saveslot - 1].name;
      this.displayName = this.name === "" ? this.saveslot : this.name;
      this.canEternity = Player.canEternity;
    },
    nicknameBlur(event) {
      const newName = event.target.value.slice(0, 4).trim();
      if (!this.isASCII(newName)) return;

      const existingNames = player.timestudy.presets.map(p => p.name);
      if (existingNames.includes(newName)) return;

      this.preset.name = newName;
      this.name = this.preset.name;
    },
    hideContextMenu() {
      this.$viewModel.currentContextMenu = null;
    },
    // This is largely done because of UI reasons - there is no Unicode specification for character width, which means
    // that arbitrary Unicode inputs can allow for massive text overflow
    isASCII(input) {
      // eslint-disable-next-line no-control-regex
      return !/[^\u0000-\u00ff]/u.test(input);
    },
    save() {
      this.hideContextMenu();
      this.preset.studies = GameCache.currentStudyTree.value.exportString;
      const presetName = this.name ? `Study preset "${this.name}"` : "Study preset";
      GameUI.notify.eternity(`${presetName} saved in slot ${this.saveslot}`);
    },
    load() {
      this.hideContextMenu();
      if (this.preset.studies) {
        // We need to use a combined tree for committing to the game state, or else it won't buy studies in the imported
        // tree are only reachable if the current tree is already bought
        const combinedTree = new TimeStudyTree();
        combinedTree.attemptBuyArray(TimeStudyTree.currentStudies, false);
        combinedTree.attemptBuyArray(combinedTree.parseStudyImport(this.preset.studies), true);
        TimeStudyTree.commitToGameState(combinedTree.purchasedStudies, false, combinedTree.startEC);

        const presetName = this.name ? `Study preset "${this.name}"` : "Study preset";
        GameUI.notify.eternity(`${presetName} loaded from slot ${this.saveslot}`);
      } else {
        Modal.message.show("This Time Study list currently contains no Time Studies.");
      }
    },
  },
};
</script>

<template>
      <button
        class="l-tt-save-load-btn c-tt-buy-button c-tt-buy-button--unlocked"
        @click.shift.exact="save"
        @click.exact="load"
      >
        {{ displayName }}
      </button>
</template>

<style scoped>
.l-tt-save-load-btn__wrapper {
  position: relative;
  width: 100%;
}

.l-tt-save-load-btn {
  width: 100%;
  height: 3.5rem;
}
</style>
