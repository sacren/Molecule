<script setup lang="ts">
    import { Link as InertiaLink } from '@inertiajs/vue3';
    import type { PropType } from 'vue';

    interface User {
        name: string;
    }

    interface Post {
        id: number;
        title: string;
        content: string;
        created_at: string;
        user: User;
    }

    interface PaginationLink {
        url: string | null;
        label: string;
        active: boolean;
    }

    interface PaginatedPosts {
        data: Post[];
        links: PaginationLink[];
    }

    defineProps({
        posts: {
            type: Object as PropType<PaginatedPosts>,
            required: true,
        },
    });
</script>

<template>
    <div class="p-6 max-w-4xl mx-auto space-y-8">
        <h1 class="text-2xl font-bold mb-6">All Posts</h1>

        <div v-for="post in posts.data" :key="post.id" class="space-y-6">
            <article class="bg-white shadow-lg rounded-lg overflow-hidden border border-gray-200">
                <header class="bg-gradient-to-r from-blue-500 to-purple-600 text-white p-6">
                    <h2 class="text-2xl font-bold leading-tight">{{ post.title }}</h2>
                    <div class="mt-1 text-blue-100">
                        By <span class="font-semibold">{{ post.user.name }}</span>
                        on <time :datetime="post.created_at">
                            {{ new Date(post.created_at).toLocaleString() }}
                        </time>
                    </div>
                </header>

                <!-- Post content -->
                <div class="p-6">
                    <p class="text-gray-700 leading-relaxed">{{ post.content }}</p>
                </div>
            </article>
        </div>

        <!-- Pagination -->
        <div v-if="posts.links.length > 3" class="flex justify-center mt-12 space-x-2">
            <template v-for="(link, index) in posts.links" :key="index">
                <InertiaLink
                        v-if="link.url"
                        :href="link.url"
                        v-html="link.label"
                        class="px-3 py-1 text-sm border rounded transition"
                        :class="{ 'bg-blue-600 text-white border-blue-600': link.active,
                                'text-blue-600 hover:bg-blue-50': !link.active, }">
                </InertiaLink>
                <span v-else v-html="link.label" class="px-3 py-1 text-sm text-gray-400" />
            </template>
        </div>
    </div>
</template>
