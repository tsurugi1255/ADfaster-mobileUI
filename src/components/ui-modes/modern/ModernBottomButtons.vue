<script>
import { bigCrunchReset } from '../../../core/big-crunch';
import { animateAndEternity } from '../../../core/eternity';
import { Player } from '../../../core/player';
import { PlayerProgress } from '../../../core/player-progress';
import { replicantiGalaxy } from '../../../core/replicanti';
import { galaxies } from '../../../core/secret-formula/multiplier-tab/galaxies';

export default {
    name: "ModernBottomButtons",
    data() {
        return {
            bottomButtonActive: false,
            crunchButtonActive: false,
            infinityReached: false,
            replicantiButtonActive: false,
            replicantiReached: false,
            eternityButtonActive: false,
            eternityReached: false
        };
    },
    computed: {
        buttonClassObject() {
            return {
                "o-bottom-btn-active": this.bottomButtonActive
            }
        }
    },
    watch: {
        bottomButtonActive(newValue) {
            player.bottomButtonActive = newValue;
        }
    },
    methods: {
        update() {
            this.bottomButtonActive = player.bottomButtonActive;
            this.infinityReached = PlayerProgress.infinityUnlocked();
            this.replicantiReached = PlayerProgress.replicantiUnlocked();
            this.eternityReached = PlayerProgress.eternityUnlocked();

            if(this.eternityButtonActive) {
                animateAndEternity();
            }

            if(this.crunchButtonActive) {
                bigCrunchReset();
            }

            if(this.replicantiButtonActive) {
                replicantiGalaxy();
            }
        },
        performEternity() {
            animateAndEternity();
        },
        performBigCrunch() {
            bigCrunchReset();
        },
        buyReplicantiGalaxy() {
            replicantiGalaxy();
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
                @click="performEternity"
                @mousedown="eternityButtonActive = true"
                @touchstart="eternityButtonActive = true"
                @mouseup="eternityButtonActive = false"
                @touchend="eternityButtonActive = false"
                @mouseleave="eternityButtonActive = false"
            >
                E
            </button>
        </div>
        <div 
            v-if="infinityReached"
            class="l-bottom-button-wrapper"
        >
            <button 
                class="o-bottom-btn" 
                @click="performBigCrunch"
                @mousedown="crunchButtonActive = true"
                @touchstart="crunchButtonActive = true"
                @mouseup="crunchButtonActive = false"
                @touchend="crunchButtonActive = false"
                @mouseleave="crunchButtonActive = false"
            >
                C
            </button>
        </div>
        <div 
            v-if="replicantiReached"
            class="l-bottom-button-wrapper"
        >
            <button 
                class="o-bottom-btn" 
                @click="buyReplicantiGalaxy"
                @mousedown="replicantiButtonActive = true"
                @touchstart="replicantiButtonActive = true"
                @mouseup="replicantiButtonActive = false"
                @touchend="replicantiButtonActive = false"
                @mouseleave="replicantiButtonActive = false"
            >
                R
            </button>
        </div>
        <div class="l-bottom-button-wrapper">
            <button 
                class="o-bottom-btn" 
                :class="buttonClassObject"
                @click="bottomButtonActive = !bottomButtonActive"
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
    border: 1px solid black;
    border-radius: 50%;
    color: white;
    width: 100%;
    height: 100%;
    pointer-events: all;
    cursor: pointer;
    transition: .2s;
}

.o-bottom-btn:hover {
    background-color: white;
    color: black;
}

.o-bottom-btn-active {
    border: 1px solid white;
}

</style>