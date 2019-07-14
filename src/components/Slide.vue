<template>
    <div class="w-full h-full flex flex-col">
        <header class="px-8 py-4 flex justify-between border-b border-gray-200">
            <h1>presentation</h1>

            <div>
                <div v-text="pageCount"></div>
                <button type="button" @click="prev()">prev</button>
                <button type="button" @click="next()">next</button>
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
            this.localCurrent = this.localCurrent-1;
        },

        next() {
            this.localCurrent = this.localCurrent+1;
        },
    },

    mounted() {
        this.localCurrent = this.current;
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