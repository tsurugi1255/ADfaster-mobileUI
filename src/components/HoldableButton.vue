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
            }, 700);
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