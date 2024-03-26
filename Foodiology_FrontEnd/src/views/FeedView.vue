<!-- <template>
    <div class="max-w-7xl mx-auto grid grid-cols-4 gap-4">
        <div class="main-center col-span-3 space-y-4">
            <div class="bg-white border border-gray-200 rounded-lg">
                <form v-on:submit.prevent="submitForm" method="post">
                    <div class="p-4">
                        <textarea v-model="body" class="p-4 w-full bg-gray-100 rounded-lg"
                            placeholder="Add your recipe!"></textarea>
                    </div>

                    <div class="p-4 border-t border-gray-100 flex justify-between">
                        <a href="#" class="inline-block py-4 px-6 bg-gray-600 text-white rounded-lg">Attach image</a>

                        <button class="inline-block py-4 px-6 bg-pink-600 text-white rounded-lg">Post</button>
                    </div>
                </form>
            </div>
            <div class="p-4 bg-white border border-gray-200 rounded-lg" v-for="post in posts" v-bind:key="post.id">
                <FeedItem v-bind:post="post" />
            </div>
        </div>

        <div class="main-right col-span-1 space-y-4">
            <RecommendedRecipes />
            <TrendingRecipes />

        </div>
    </div>
</template>

<script>
import axios from 'axios'
import RecommendedRecipes from '../components/RecommendedRecipes.vue'
import TrendingRecipes from '../components/TrendingRecipes.vue'
import FeedItem from '../components/FeedItem.vue'

export default {
    name: 'FeedView',
    components: {
        RecommendedRecipes,
        TrendingRecipes,
        FeedItem
    },

    data() {
        return {
            posts: [],
            body: '',
        }
    },

    mounted() {
        this.getFeed()
    },

    methods: {
        getFeed() {
            axios
                .get('/api/posts/')
                .then(response => {
                    console.log('data', response.data)

                    this.posts = response.data
                })
                .catch(error => {
                    console.log('error', error)
                })
        },

        submitForm() {
            console.log('submitForm', this.body)

            axios
                .post('/api/posts/create/', {
                    'body': this.body
                })
                .then(response => {
                    console.log('data', response.data)

                    this.posts.unshift(response.data)
                    this.body = ''
                })
                .catch(error => {
                    console.log('error', error)
                })
        }
    }
}
</script> -->

<template>
    <div class="max-w-7xl mx-auto grid grid-cols-4 gap-4">
        <div class="main-center col-span-3 space-y-4">
            <div class="bg-white border border-gray-200 rounded-lg">
                <form v-on:submit.prevent="submitForm" method="post" enctype="multipart/form-data">
                    <div class="p-4">
                        <input v-model="recipeName" class="p-4 w-full bg-gray-100 rounded-lg" placeholder="Recipe name"
                            required />
                    </div>
                    <div class="p-4">
                        <textarea v-model="ingredients" class="p-4 w-full bg-gray-100 rounded-lg"
                            placeholder="Ingredients (separate with commas)" required></textarea>
                    </div>
                    <div class="p-4">
                        <textarea v-model="steps" class="p-4 w-full bg-gray-100 rounded-lg"
                            placeholder="Steps (separate with new lines)" required></textarea>
                    </div>
                    <div class="p-4">
                        <input type="file" @change="handleFileUpload" hidden ref="fileInput" />
                        <a href="#" class="inline-block py-4 px-6 bg-gray-600 text-white rounded-lg"
                            @click.prevent="triggerFileInput">Attach image</a>
                    </div>
                    <div class="p-4 border-t border-gray-100 flex justify-between">
                        <button class="inline-block py-4 px-6 bg-purple-600 text-white rounded-lg"
                            :disabled="!canSubmit">Post</button>
                    </div>
                </form>
            </div>
            <div class="p-4 bg-white border border-gray-200 rounded-lg" v-for="post in posts" v-bind:key="post.id">
                <FeedItem v-bind:post="post" />
            </div>
        </div>
        <div class="main-right col-span-1 space-y-4">
            <RecommendedRecipes />
            <TrendingRecipes />
        </div>
    </div>
</template>
  
<script>
import axios from 'axios'
import RecommendedRecipes from '../components/RecommendedRecipes.vue'
import TrendingRecipes from '../components/TrendingRecipes.vue'
import FeedItem from '../components/FeedItem.vue'

export default {
    name: 'FeedView',
    components: {
        RecommendedRecipes,
        TrendingRecipes,
        FeedItem
    },

    data() {
        return {
            posts: [],
            recipeName: '',
            ingredients: '',
            steps: '',
            image: null
        }
    },

    computed: {
        canSubmit() {
            return this.recipeName.trim() && this.ingredients.trim() && this.steps.trim() && this.image;
        }
    },

    mounted() {
        this.getFeed()
    },

    methods: {
        getFeed() {
            axios
                .get('/api/posts/')
                .then(response => {
                    console.log('data', response.data)

                    this.posts = response.data
                })
                .catch(error => {
                    console.log('error', error)
                })
        },

        handleFileUpload(event) {
            this.image = event.target.files[0]
        },

        triggerFileInput() {
            this.$refs.fileInput.click();
        },

        submitForm() {
            let formData = new FormData()
            formData.append('recipeName', this.recipeName)
            formData.append('ingredients', this.ingredients)
            formData.append('steps', this.steps)
            formData.append('image', this.image)

            axios
                .post('/api/posts/create/', formData, {
                    headers: {
                        'Content-Type': 'multipart/form-data'
                    }
                })
                .then(response => {
                    console.log('data', response.data)

                    this.posts.unshift(response.data)
                    this.recipeName = ''
                    this.ingredients = ''
                    this.steps = ''
                    this.image = null
                })
                .catch(error => {
                    console.log('error', error)
                })
        }
    }
}
</script>
  