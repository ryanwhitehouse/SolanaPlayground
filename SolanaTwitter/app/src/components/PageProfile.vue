<script setup>
import { ref, watchEffect } from 'vue'
import { fetchTweets, authorFilter } from '@/api'
import TweetForm from '@/components/TweetForm'
import TweetList from '@/components/TweetList'
import { useWorkspace } from '@/composables'

import { useWallet } from '@solana/wallet-adapter-vue'
const { connected } = useWallet()
const workspace = useWorkspace()
const { wallet } = workspace

const tweets = ref([])
const loading = ref(true)

watchEffect(() => {
    if (! wallet.value) return
    fetchTweets(workspace, [authorFilter(wallet.value.publicKey.toBase58())])
        .then(fetchedTweets => tweets.value = fetchedTweets)
        .finally(() => loading.value = false)
})

const addTweet = tweet => tweets.value.push(tweet)
</script>

<template>
    <div v-if="connected" class="border-b px-8 py-4 bg-gray-50">
         {{ wallet.publicKey.toBase58() }}
    </div>
    <tweet-form @added="addTweet"></tweet-form>
    <tweet-list :tweets="tweets" :loading="loading"></tweet-list>
</template>
