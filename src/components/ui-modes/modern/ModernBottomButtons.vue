<script>

export default {
    name: "ModernBottomButtons",
    data() {
        return {
            infinityReached: false,
            replicantiReached: false,
            eternityReached: false,
            currentSticky: null,
            buttonTimeout: null,
            buttonActiveState: [false, false, false, false]
        };
    },
    computed: {
        buttonFunctions() {
            return {
                0: maxAll,              // Buy Max
                1: bigCrunchReset,      // Big Crunch
                2: replicantiGalaxy,    // Buy RG
                3: animateAndEternity   // Eternity
            }
        }
    },
    methods: {
        update() {
            this.infinityReached = PlayerProgress.infinityUnlocked();
            this.replicantiReached = PlayerProgress.replicantiUnlocked();
            this.eternityReached = PlayerProgress.eternityUnlocked();

            for(let i = 0; i < 4; i++) {
                if(this.buttonActiveState[i] || i === this.currentSticky) {
                    this.buttonFunctions[i]();
                }
            }
        },
        buttonClick(buttonId) {
            this.buttonFunctions[buttonId]();
            if(buttonId === this.currentSticky) {
                this.currentSticky = null;
            }
        },
        buttonPress(buttonId) {
            this.$set(this.buttonActiveState, buttonId, true)
            this.buttonTimeout = setTimeout(() => {
                this.currentSticky = buttonId;
            }, 500);
        },
        buttonRelease(buttonId) {
            this.$set(this.buttonActiveState, buttonId, false)
            clearTimeout(this.buttonTimeout);
            this.buttonTimeout = null;
        },
        buttonClassObject(buttonId) {
            return {
                "o-bottom-btn-active": buttonId === this.currentSticky,
                "o-bottom-btn-pressed": this.buttonActiveState[buttonId]
            }
        }
    }
}
</script>

<template>
    <div class="c-bottom-button-container">
        <div 
            v-if="eternityReached" 
            class="l-bottom-button-wrapper"
        >
            <button
                class="o-bottom-btn"
                :class="buttonClassObject(3)"
                @click="buttonClick(3)"
                @mousedown="buttonPress(3)"
                @touchstart="buttonPress(3)"
                @mouseup="buttonRelease(3)"
                @touchend="buttonRelease(3)"
                @mouseleave="buttonRelease(3)"
            >
                E
            </button>
        </div>
        <div 
            class="l-bottom-button-wrapper"
            v-if="replicantiReached"
        >
            <button
                class="o-bottom-btn"
                :class="buttonClassObject(2)"
                @click="buttonClick(2)"
                @mousedown="buttonPress(2)"
                @touchstart="buttonPress(2)"
                @mouseup="buttonRelease(2)"
                @touchend="buttonRelease(2)"
                @mouseleave="buttonRelease(2)"
            >
                R
            </button>
        </div>
        <div 
            class="l-bottom-button-wrapper"
            v-if="infinityReached"
        >
            <button
                class="o-bottom-btn"
                :class="buttonClassObject(1)"
                @click="buttonClick(1)"
                @mousedown="buttonPress(1)"
                @touchstart="buttonPress(1)"
                @mouseup="buttonRelease(1)"
                @touchend="buttonRelease(1)"
                @mouseleave="buttonRelease(1)"
            >
                C
            </button>
        </div>
        <div class="l-bottom-button-wrapper">
            <button
                class="o-bottom-btn"
                :class="buttonClassObject(0)"
                @click="buttonClick(0)"
                @mousedown="buttonPress(0)"
                @touchstart="buttonPress(0)"
                @mouseup="buttonRelease(0)"
                @touchend="buttonRelease(0)"
                @mouseleave="buttonRelease(0)"
            >
                M
            </button>
        </div>
    </div>
</template>

<style scoped>

.c-bottom-button-container {
    position: absolute;
    bottom: 10vh;
    right: 0;
    z-index: 5;
    width: 100vw;
    height: 10vh;
    padding: 2rem 4rem;
    display: flex;
    flex-direction: row;
    justify-content: flex-end;
    align-items: center;
}

.l-bottom-button-wrapper {
    width: 13%;
    aspect-ratio: 1/1;
    margin: 0 1rem;
}

.o-bottom-btn {
    background-color: black;
    font-size: 5rem;
    border: 0.4rem solid black;
    border-radius: 50%;
    color: white;
    width: 100%;
    height: 100%;
    pointer-events: all;
    cursor: pointer;
    transition: .2s;
    -webkit-user-select: none;    
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
}

.o-bottom-btn-active {
    border: 0.4rem solid white !important;
}

.o-bottom-btn-pressed {
    background-color: gray;
    border: 0.4rem solid gray;
}

</style>