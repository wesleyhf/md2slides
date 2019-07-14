<template>
    <div id="app" class="font-sans">
        <div class="w-screen h-screen">
            <slide
                :title="title"
                :slides="slides"
            ></slide>
        </div>

        <aside
            class="fixed inset-y-0 right-0 w-1/4 p-4 bg-gray-100 border-l border-gray-200 sidebar"
            :class="{ active: showSettings }"
        >
            <div>
                <h1 class="text-3xl mb-4">Settings</h1>

                <div class="mb-4">
                    <label class="block mb-2" for="title">Title</label>
                    <input
                        id="title"
                        type="text"
                        placeholder="Title"
                        class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                        v-model="title"
                    >
                </div>

                <div class="mb-4">
                    <label class="block mb-2" for="markdown">Markdown</label>
                    <textarea
                        id="markdown"
                        type="text"
                        placeholder="Markdown"
                        class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                        v-model="markdown"
                        rows="10"
                    ></textarea>
                </div>
            </div>

            <button
                type="button"
                class="bg-gray-100 text-gray-800 font-bold py-2 px-4 rounded inline-flex items-center absolute bottom-0 left-0 sidebar-btn"
                :class="{ active: showSettings }"
                @click="toggleSettings()"
            >
                <svg class="fill-current w-4 h-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><polygon points="3.828 9 9.899 2.929 8.485 1.515 0 10 .707 10.707 8.485 18.485 9.899 17.071 3.828 11 20 11 20 9 3.828 9"/></svg>
            </button>
        </aside>
    </div>
</template>

<script>
import Slide from './components/Slide.vue'

export default {
    name: 'app',

    components: {
        Slide,
    },

    data: () => ({
        title: '',
        markdown: '',
        showSettings: false,
    }),

    computed: {
        slides() {
            return this.markdown
                .trim()
                .split('@end')
                .map(slide => slide.trim());
        },
    },

    methods: {
        setLocalStorage() {
            const settings = {
                title: this.title,
                markdown: this.markdown,
            };

            localStorage.setItem('settings', JSON.stringify(settings));
        },

        getLocalStorage() {
            const settings = JSON.parse(
                localStorage.getItem('settings')
            );

            console.log(settings);

            if (! settings) {
                return;
            }

            if (settings.markdown) {
                this.markdown = settings.markdown;
            }

            if (settings.title) {
                this.title = settings.title;
            }
        },

        toggleSettings() {
            this.showSettings = !this.showSettings;
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

.sidebar {
    transform: translateX(100%);
    transition: transform .5s ease;

    &.active {
        transform: translateX(0);
    }
}

.sidebar-btn {
    transform: translateX(-100%);

    svg {
        transition: transform .5s ease;
    }

    &.active {
        svg {
            transform: rotate(180deg);
        }
    }
}
</style>
