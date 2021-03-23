<template>
    <div class="v-scroller" @scroll="scrollHandler">
        <div class="v-scroller__viewport" :style="{ height: viewportHeight + 'px' }">
            <div class="v-scroller__spacer" ref="spacer" :style="spacerStyle">
                <div class="v-scroller__item" v-for="(item, idx) in visibleItems" :key="idx">
                    <slot name="item-template" :item="item">{{ item }}</slot>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "VScroller",
    props: {
        items: { required: true }
    },
    data: () => ({
        rootHeight: 100,
        rowHeight: 10,
        rowWidth: 100,
        spacerWidth: 100,
        scrollTop: 0,
    }),
    computed: {
        itemsPerRow() {
            return Math.ceil(this.spacerWidth / this.rowWidth);
        },
        viewportHeight() {
            return Math.ceil(this.items.length / this.itemsPerRow) * this.rowHeight;
        },
        startIndex() {
            let count = Math.floor(this.scrollTop / this.rowHeight);
            count = Math.floor(count * this.itemsPerRow);
            return Math.max(0, count);
        },
        countVisibleItems() {
            const count = (Math.ceil(this.rootHeight / this.rowHeight) + 2) * this.itemsPerRow;
            const min = this.items.length - this.startIndex;
            return Math.min(min, count);
        },
        visibleItems() {
            return this.items.slice(
                this.startIndex,
                this.startIndex + this.countVisibleItems
            )
        },
        spacerStyle() {
            return { transform: 'translateY('+ (this.startIndex / this.itemsPerRow) * this.rowHeight +'px)' }
        }
    },
    methods: {
        scrollHandler() {
            this.scrollTop = this.$el.scrollTop;
        }
    },
    mounted() {
        const rootEl = this.$el;
        const spacerEl = this.$refs['spacer'];

        const rootBounds = rootEl.getBoundingClientRect();
        this.rootHeight = rootBounds.height;

        const spacerBounds = spacerEl.getBoundingClientRect();
        this.spacerWidth = spacerBounds.width;

        const children = spacerEl.children;
        for (let i = 0; i < children.length; i++) {
            const child = children[i];
            this.rowHeight = Math.max(child.offsetHeight, this.rowHeight);
            this.rowWidth = Math.max(child.offsetWidth, this.rowWidth);
        }
    }
}
</script>

<style lang="scss">
.v-scroller {
    overflow: auto;
    height: 300px;
}
</style>
