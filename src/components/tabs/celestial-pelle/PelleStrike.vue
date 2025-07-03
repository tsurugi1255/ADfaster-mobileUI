<script>
import ExpandingControlBox from "@/components/ExpandingControlBox";

export default {
  name: "PelleStrike",
  components: {
    ExpandingControlBox
  },
  props: {
    strike: {
      type: Object,
      required: true
    },
  },
  data() {
    return {
      strikeReward: ""
    };
  },
  methods: {
    update() {
      this.strikeReward = this.strike.reward();
    }
  }
};
</script>

<template>
  <!-- <div class="c-pelle-strike-container">
    <ExpandingControlBox container-class="c-pelle-strike">
      <template #header>
        <div class="c-pelle-strike-header">
          ▼ {{ strike.requirement }} ▼
        </div>
      </template>
      <template #dropdown>
        <div class="c-pelle-strike-dropdown">
          <span>Penalty: {{ strike.penalty }}</span>
          <br>
          <span>Reward: {{ strikeReward }}</span>
        </div>
      </template>
    </ExpandingControlBox>
  </div> -->
  <div class="c-pelle-strike-container">
    <div class="c-pelle-strike-header">
      ▼ {{ strike.requirement }} ▼
    </div>
    <div class="c-pelle-strike-dropdown">
      <span><b>Penalty:</b> {{ strike.penalty }}</span>
      <span><b>Reward:</b> {{ strikeReward }}</span>
    </div>
  </div>
</template>

<style>
.c-pelle-strike {
  min-height: 3.8rem;
  z-index: 3;
}

.c-pelle-strike-container {
  width: 100%;
  height: 100%;
  padding: 0.5rem 0.5rem 2rem;
  background: var(--color-pelle--base);
  border: var(--var-border-width, 0.2rem) solid black;
  border-radius: var(--var-border-radius, 1.5rem);
  font-size: 1.8rem;
  font-weight: bold;
  color: black;
}

.c-pelle-strike-header {
  padding: 0.7rem;
  cursor: pointer;
}

.c-pelle-strike-dropdown {
  padding: 0.7rem;
}
</style>
