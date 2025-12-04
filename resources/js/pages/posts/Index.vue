<script setup lang="ts">
    import { Head, Link as InertiaLink, router } from '@inertiajs/vue3';
    import { reactive } from 'vue';
    import { useDateFormatter } from '@/composables/useDateFormatter';
    import AppLayout from '@/layouts/AppLayout.vue';
    import type { BreadcrumbItem, User } from '@/types';

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

    // Make form data reactive
    const form = reactive({
        title: '',
        content: '',
    });

    const handleSubmit = () => {
        router.post('/posts', form, {
            preserveScroll: true,
            onSuccess: () => {
                form.title = '';
                form.content = '';
            },
            onError: (errors) => {
                console.log(errors);
            },
        });
    };

    const breadcrumbs: BreadcrumbItem[] = [
        {
            title: 'Posts',
            href: '/posts',
        },
    ];
</script>

<template>
    <Head title="Posts" />
    <AppLayout :breadcrumbs="breadcrumbs">
        <div class="p-4 sm:p-6 max-w-4xl mx-auto space-y-8">
            <h1 class="text-2xl font-bold mb-6">All Posts</h1>

            <!-- Create new post form -->
            <section class="bg-white p-6 rounded-lg shadow-md border border-gray-200">
                <h2 class="text-xl font-semibold mb-4">Write a new post</h2>
                <form @submit.prevent="handleSubmit" class="space-y-4">
                    <!-- Content -->
                    <div>
                        <label for="content" class="sr-only">
                            What's on your mind?
                        </label>
                        <textarea
                            id="content"
                            v-model="form.content"
                            rows="3"
                            class="w-full px-4 py-2 border border-gray-300 rounded-md
                                    focus:ring-blue-500 focus:border-blue-500"
                            placeholder="What's on your mind?">
                        </textarea>
                        <div v-if="$page.props.errors.content" class="text-red-600 text-sm mt-1">
                            {{ $page.props.errors.content }}
                        </div>
                    </div>

                    <!-- Submit button -->
                    <div>
                        <button type="submit"
                            class="px-6 py-2 bg-blue-600 text-white font-medium rounded-md
                                    hover:bg-blue-700 disabled:opacity-50
                                    disabled:cursor-not-allowed transition">
                            Create Post
                        </button>
                    </div>
                </form>
            </section>

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
