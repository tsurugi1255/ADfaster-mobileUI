<script>
import ExpandingControlBox from "@/components/ExpandingControlBox";
import OpenModalHotkeysButton from "@/components/OpenModalHotkeysButton";
import OptionsButton from "@/components/OptionsButton";
import PrimaryToggleButton from "@/components/PrimaryToggleButton";
import SelectNotationDropdown from "@/components/tabs/options-visual/SelectNotationDropdown";
import SelectThemeDropdown from "@/components/tabs/options-visual/SelectThemeDropdown";
import SelectSidebarDropdown from "@/components/tabs/options-visual/SelectSidebarDropdown";
import UpdateRateSlider from "./UpdateRateSlider";

export default {
  name: "OptionsVisualTab",
  components: {
    UpdateRateSlider,
    PrimaryToggleButton,
    ExpandingControlBox,
    OptionsButton,
    OpenModalHotkeysButton,
    SelectThemeDropdown,
    SelectNotationDropdown,
    SelectSidebarDropdown
  },
  data() {
    return {
      theme: "",
      notation: "",
      sidebarResource: "",
      headerTextColored: true,
    };
  },
  computed: {
    sidebarDB: () => GameDatabase.sidebarResources,
    themeLabel() {
      return `Theme: ${Themes.find(this.theme).displayName()}`;
    },
    notationLabel() {
      return `Notation: ${this.notation}`;
    },
    sidebarLabel() {
      return `Sidebar (Modern UI): ${this.sidebarResource}`;
    },
    UILabel() {
      return `UI: ${this.$viewModel.newUI ? "Modern" : "Classic"}`;
    }
  },
  watch: {
    headerTextColored(newValue) {
      player.options.headerTextColored = newValue;
    },
  },
  methods: {
    update() {
      const options = player.options;
      this.theme = Theme.currentName();
      this.notation = options.notation;
      this.sidebarResource = player.options.sidebarResourceID === 0
        ? "Latest Resource"
        : this.sidebarDB.find(e => e.id === player.options.sidebarResourceID).optionName;
      this.headerTextColored = options.headerTextColored;
    },
  }
};
</script>

<template>
  <div class="l-options-tab">
    <div class="l-options-grid">
      <div class="l-options-grid__row">
        <OptionsButton
          class="o-primary-btn--option_font-large"
          onclick="GameOptions.toggleUI()"
        >
          {{ UILabel }}
        </OptionsButton>
        <UpdateRateSlider />
        <OptionsButton
          class="o-primary-btn--option"
          onclick="Modal.newsOptions.show();"
        >
          Open News Options
        </OptionsButton>
      </div>
      <div class="l-options-grid__row">
        <OptionsButton
          class="o-primary-btn--option"
          onclick="Modal.animationOptions.show();"
        >
          Open Animation Options
        </OptionsButton>
        <OptionsButton
          class="o-primary-btn--option"
          onclick="Modal.infoDisplayOptions.show()"
        >
          Open Info Display Options
        </OptionsButton>
        <OptionsButton
          class="o-primary-btn--option"
          onclick="Modal.awayProgressOptions.show()"
        >
          Open Away Progress Options
        </OptionsButton>
      </div>
      <div class="l-options-grid__row">
        <OptionsButton
          class="o-primary-btn--option"
          onclick="Modal.hiddenTabs.show()"
        >
          Modify Visible Tabs
        </OptionsButton>
        <PrimaryToggleButton
          v-model="headerTextColored"
          class="o-primary-btn--option l-options-grid__button"
          label="Relative prestige gain text coloring:"
        />
        <OptionsButton
          class="o-primary-btn--option"
          onclick="Modal.notation.show();"
        >
          Open Exponent Notation Options
        </OptionsButton>
      </div>
      <div class="l-options-grid__row">
        <ExpandingControlBox
          class="l-options-grid__button c-options-grid__notations"
          button-class="o-primary-btn o-primary-btn--option l-options-grid__notations-header"
          :label="themeLabel"
        >
          <template #dropdown>
            <SelectThemeDropdown />
          </template>
        </ExpandingControlBox>
        <ExpandingControlBox
          class="l-options-grid__button c-options-grid__notations"
          button-class="o-primary-btn o-primary-btn--option l-options-grid__notations-header"
          :label="notationLabel"
        >
          <template #dropdown>
            <SelectNotationDropdown />
          </template>
        </ExpandingControlBox>
      </div>
      <OpenModalHotkeysButton />
    </div>
  </div>
</template>
