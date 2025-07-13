<script>
export default {
    name: "HoldableButton",
    props: {
        className: {
            type: [String, Object],
            default: ""
        },
        onHoldFunction: {
            type: Function,
            required: true
        },
        onHoldClass: {
            type: String,
            default: ""
        }
    },
    data() {
        return {
            isPressed: false,
            onPressTimeout: null
        };
    },
    computed: {
        StyleObject() {
            return {
                [this.onHoldClass]: this.isPressed,
                [this.className]: true
            }
        }
    },
    methods: {
        update() {
            if(this.isPressed) {
                this.onHoldFunction();
            }
        },
        onPress() {
            this.onPressTimeout = setTimeout(() => {
                this.isPressed = true;
            }, 100);
        },
        releasePress() {
            clearTimeout(this.onPressTimeout);
            this.onPressTimeout = null;
            this.isPressed = false;
        }
        
    }
}
</script>

<template>
    <button
        class="o-holdable-button"
        :class="StyleObject"
        @click="onHoldFunction"
        @mousedown="onPress"
        @touchstart="onPress"
        @mouseup="releasePress"
        @touchend="releasePress"
        @mouseleave="releasePress"
    >
        <slot />
    </button>
</template>

<style scoped>
/* On mobile when holding the button there is a tendency for button text to be selected. This mitigates that. */
.o-holdable-button {
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
</style>