<script setup>
import { Head, Link } from '@inertiajs/vue3';

defineProps({
    canLogin: {
        type: Boolean,
    },
    canRegister: {
        type: Boolean,
    },
    laravelVersion: {
        type: String,
        required: true,
    },
    phpVersion: {
        type: String,
        required: true,
    },
    products: Array,
    categories: Array,
});

function handleImageError() {
    document.getElementById('screenshot-container')?.classList.add('!hidden');
    document.getElementById('docs-card')?.classList.add('!row-span-1');
    document.getElementById('docs-card-content')?.classList.add('!flex-row');
    document.getElementById('background')?.classList.add('!hidden');
}
</script>

<template>
    <Head title="Home" />
    <div class="bg-gradient-to-br from-blue-50 to-blue-100 min-h-screen flex flex-col">
        <!-- Hero Section -->
        <section class="py-12 text-center">
            <h1 class="text-4xl font-extrabold text-blue-900 mb-4">Welcome to the E-commerce Demo</h1>
            <p class="text-lg text-blue-700 mb-6">Browse our products and categories, built with Laravel, Vue, and Tailwind CSS.</p>
            <a href="#products" class="inline-block bg-blue-600 text-white px-6 py-3 rounded-lg shadow hover:bg-blue-700 transition">Browse Products</a>
        </section>

        <!-- Category List -->
        <section class="max-w-5xl mx-auto w-full mb-10 px-4">
            <div class="flex flex-wrap gap-3 justify-center">
                <span v-for="cat in categories" :key="cat.id" class="bg-blue-100 text-blue-800 px-4 py-2 rounded-full text-sm font-semibold shadow-sm">{{ cat.name }}</span>
            </div>
        </section>

        <!-- Product Grid -->
        <section id="products" class="flex-1 max-w-6xl mx-auto w-full px-4">
            <h2 class="text-2xl font-bold text-blue-900 mb-6 text-center">Products</h2>
            <div v-if="products.length" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-8">
                <div v-for="product in products" :key="product.id" class="bg-white rounded-xl shadow-lg p-6 flex flex-col hover:scale-105 hover:shadow-2xl transition-transform">
                    <div class="flex-1">
                        <h3 class="text-lg font-bold text-blue-800 mb-2">{{ product.name }}</h3>
                        <p class="text-gray-600 mb-3">{{ product.description }}</p>
                    </div>
                    <div class="flex items-center justify-between mt-4">
                        <span class="text-xl font-bold text-blue-700">৳ {{ product.price }}</span>
                        <div class="flex flex-wrap gap-1">
                            <span v-for="cat in product.categories" :key="cat.id" class="bg-blue-200 text-blue-900 px-2 py-1 rounded text-xs">{{ cat.name }}</span>
                        </div>
                    </div>
                </div>
            </div>
            <div v-else class="text-center text-gray-500 py-16">
                No products available.
            </div>
        </section>

        <!-- Footer -->
        <footer class="mt-16 py-6 text-center text-blue-700 text-sm border-t border-blue-200 bg-blue-50">
            © {{ new Date().getFullYear() }} E-commerce Demo. Built with Laravel, Vue, and Tailwind CSS.
        </footer>
    </div>
</template>
