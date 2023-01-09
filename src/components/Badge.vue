

<template>
    <span class="badge rounded-pill" :style="{ backgroundColor: item.color}">{{ displayText }}</span>
</template>

<script>
export default {
    props: {
        /** Object representing tag */
        item: {
            type: Object,
            required: true,
        },
        /** Function to remove tag */
        remove: {
            type: Function,
            default: null,
        },
        /**
         * Size of tag
         * @values normal, small
         */
        size: {
            type: String,
            default: "normal",
        }
    },
    computed: {
        displayText() {
            if (this.item.value === "") {
                return this.item.name;
            } else {
                return `${this.item.name}: ${this.item.value}`;
            }
        }
    }
};
</script>

<style lang="scss" scoped>
.tag-wrapper {
    color: white;
    opacity: 0.85;

    .dark & {
        opacity: 1;
    }
}

.tag-text {
    padding-bottom: 1px !important;
    text-overflow: ellipsis;
    overflow: hidden;
    white-space: nowrap;
}

.btn-remove {
    font-size: 0.9em;
    line-height: 24px;
    opacity: 0.3;
}

.btn-remove:hover {
    opacity: 1;
}
</style>
