<script setup lang="ts">
    import { Link as InertiaLink } from '@inertiajs/vue3';
    import type { PropType } from 'vue';
    import { useDateFormatter } from '@/composables/useDateFormatter';
    import AppLayout from '@/layouts/AppLayout.vue';
    import type { BreadcrumbItem } from '@/types';

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

    interface PostsType {
        posts: PaginatedPosts;
    }

    defineProps<PostsType>();

    const { formatDate } = useDateFormatter();

    const breadcrumbs: BreadcrumbItem[] = [
        {
            title: 'Posts',
            href: '/posts',
        },
    ];
</script>

<template>
    <AppLayout :breadcrumbs="breadcrumbs">
        <div class="p-6 max-w-4xl mx-auto space-y-8">
            <h1 class="text-2xl font-bold mb-6">All Posts</h1>

            <!-- Empty state when no posts exist -->
            <div v-if="!posts.data.length" class="text-center py-8 text-gray-500">
                No posts found.
            </div>

            <div v-for="post in posts.data" :key="post.id" class="space-y-6">
                <article class="bg-white shadow-lg rounded-lg overflow-hidden border border-gray-200">
                    <header class="bg-gradient-to-r from-blue-500 to-purple-600 text-white p-6">
                        <h2 class="text-2xl font-bold leading-tight">{{ post.title }}</h2>
                        <div class="mt-1 text-blue-100">
                            By <span class="font-semibold">{{ post.user.name }}</span>
                            on <time :datetime="post.created_at">
                                {{ formatDate(post.created_at) }}
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
            <nav v-if="posts.links.length > 3"
                 class="flex justify-center mt-12 space-x-2"
                 aria-label="Pagination">
                <template v-for="(link, index) in posts.links" :key="index">
                    <InertiaLink
                            v-if="link.url"
                            :href="link.url"
                            :aria-label="`Go to page ${link.label}`"
                            v-html="link.label"
                            :class="[ 'px-3 py-1 text-sm border rounded transition',
                                    link.active
                                    ? 'bg-blue-600 text-white border-blue-600'
                                    : 'text-blue-600 hover:bg-blue-50', ]">
                    </InertiaLink>
                    <span v-else
                          v-html="link.label"
                          :aria-label="`Page ${link.label}`"
                          class="px-3 py-1 text-sm text-gray-400" />
                </template>
            </nav>
        </div>
    </AppLayout>
</template>
