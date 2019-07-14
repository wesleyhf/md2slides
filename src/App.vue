<template>
    <div id="app" class="font-sans">
        <div class="slide-wrapper">
            <slide :slides="slides"></slide>
        </div>

        <button type="button" @click="toggleSideBar()">toggleSideBar</button>

        <div
            class="fixed inset-y-0 right-0 w-1/4 bg-blue-400 sidebar"
            :class="sideBarClasses"
        >
            <textarea v-model="markdown" rows="10"></textarea>
        </div>
    </div>
</template>

<script>
import Slide from './components/Slide.vue'

const storageKey = 'markdown';

export default {
    name: 'app',

    components: {
        Slide,
    },

    data: () => ({
        markdown: '',
        showSideBar: false,
    }),

    computed: {
        slides() {
            return this.markdown
                .trim()
                .split('@end')
                .map(slide => slide.trim());
        },

        sideBarClasses() {
            return {
                'active': this.showSideBar,
            };
        },
    },

    methods: {
        setLocalStorage() {
            localStorage.setItem(
                storageKey,
                JSON.stringify(this.markdown)
            );
        },

        getLocalStorage() {
            const encodedMarkdown = localStorage.getItem(storageKey);

            if (encodedMarkdown) {
                this.markdown = JSON.parse(encodedMarkdown);
            }
        },

        toggleSideBar() {
            this.showSideBar = !this.showSideBar;
        },
    },

    mounted() {
        this.getLocalStorage();
    },
}
</script>

<style lang="scss">
@tailwind base;
@tailwind components;
@tailwind utilities;

.slide-wrapper {
    width: 960px;
    height: 540px;
    margin: 0 auto;
}

.sidebar {
    transform: translateX(100%);
    transition: transform .5s ease;

    &.active {
        transform: translateX(0);
    }
}
</style>
