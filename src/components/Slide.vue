<template>
    <div class="w-full h-full flex flex-col">
        <header class="px-8 py-4 flex justify-between border-b border-gray-200">
            <h1 v-text="title"></h1>

            <div>
                <button type="button" @click="prev()">
                    <svg class="fill-current w-4 h-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M7.05 9.293L6.343 10 12 15.657l1.414-1.414L9.172 10l4.242-4.243L12 4.343z"/></svg>
                </button>

                <span v-text="pageCount"></span>

                <button type="button" @click="next()">
                    <svg class="fill-current w-4 h-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M12.95 10.707l.707-.707L8 4.343 6.586 5.757 10.828 10l-4.242 4.243L8 15.657l4.95-4.95z"/></svg>
                </button>
            </div>
        </header>

        <div class="flex-grow overflow-hidden">
            <ul
                class="flex min-h-full transition-transform"
                :style="translationStyle"
            >
                <li
                    class="min-w-full inset-0 p-8"
                    v-for="(slide, key) in slides"
                    :key="key"
                    v-html="getHtml(slide)"
                ></li>
            </ul>
        </div>

        <div class="h-1 overflow-hidden">
            <div class="h-full bg-green-400 transition-width" :style="progressWidth"></div>
        </div>
    </div>
</template>

<script>
import marked from 'marked';

export default {
    props: {
        title: {
            type: String,
            required: true,
        },

        slides: {
            type: Array,
            required: true,
        },

        current: {
            type: Number,
            default: 0,
        }
    },

    data: () => ({
        localCurrent: null,
    }),

    computed: {
        pageCount() {
            const current = this.localCurrent + 1;

            return `${current}/${this.slides.length}`;
        },

        translationStyle() {
            let style = {};
            const coordinate = this.localCurrent * 100;

            if (style) {
                style.transform = `translateX(-${coordinate}%)`;
            }

            return style;
        },

        progressWidth() {
            const width = (this.localCurrent * 100) / (this.slides.length-1);

            return {
                width: `${width}%`,
            };
        },
    },

    methods: {
        getHtml(slide) {
            return marked(slide, { sanitize: true });
        },

        prev() {
            if (this.localCurrent > 0) {
                this.localCurrent = this.localCurrent - 1;
            }
        },

        next() {
            const limit = this.slides.length - 1;

            if (this.localCurrent < limit) {
                this.localCurrent = this.localCurrent + 1;
            }
        },

        onKeyUp(event) {
            switch (event.keyCode) {
                case 37: // left
                    this.prev();
                    break;

                case 39: // right
                    this.next();
                    break;

                case 32: // space
                    this.next();
                    break;
            }
        },
    },

    mounted() {
        this.localCurrent = this.current;

        document.addEventListener('keyup', this.onKeyUp);
    },

    destroyed() {
        document.removeEventListener('keyup', this.onKeyUp);
    },
}
</script>

<style>
.transition-transform {
    transition: transform .5s ease;
}

.transition-width {
    transition: width .5s ease;
}
</style>
