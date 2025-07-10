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
            isPressed: false
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
        }
        
    }
}
</script>

<template>
    <button
        :class="StyleObject"
        @click="onHoldFunction"
        @mousedown="isPressed = true"
        @touchstart="isPressed = true"
        @mouseup="isPressed = false"
        @touchend="isPressed = false"
        @mouseleave="isPressed = false"
    >
        <slot />
    </button>
</template>