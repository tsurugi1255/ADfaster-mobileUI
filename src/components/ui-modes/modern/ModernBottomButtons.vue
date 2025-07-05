<script>
export default {
    name: "ModernBottomButtons",
    data() {
        return {
            bottomButtonActive: player.bottomButtonActive
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
        }
    }
}
</script>

<template>
    <div class="c-bottom-button-container">
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
}

.o-bottom-btn-active {
    border: 1px solid white;
}

</style>