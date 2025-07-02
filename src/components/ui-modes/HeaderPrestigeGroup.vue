<script>
import HeaderCenterContainer from "./prestige-header/HeaderCenterContainer";
import HeaderEternityContainer from "./prestige-header/HeaderEternityContainer";
import HeaderInfinityContainer from "./prestige-header/HeaderInfinityContainer";

export default {
  name: "HeaderPrestigeGroup",
  components: {
    HeaderCenterContainer,
    HeaderEternityContainer,
    HeaderInfinityContainer,
  },
};
</script>

<template>
  <div class="c-prestige-info-blocks">
    <div class="c-game-headers">
      <HeaderEternityContainer class="l-game-header__eternity" />
      <HeaderInfinityContainer class="l-game-header__infinity" />
    </div>
    <HeaderCenterContainer class="l-game-header__center" />
  </div>
</template>

<style scoped>
.c-prestige-info-blocks {
  display: flex;
  flex-direction: column;
  /* height: fit-content; */
  width: 100%;
  color: var(--color-text);
}

.c-game-headers {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: stretch;
  min-height: 40%;
}

.l-game-header__eternity {
  flex: 1;
}

.l-game-header__infinity {
  flex: 1;
}
</style>
