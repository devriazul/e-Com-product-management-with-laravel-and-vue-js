<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head, router } from '@inertiajs/vue3';
import { ref, onMounted } from 'vue';

const tab = ref('products');
const products = ref([]);
const categories = ref([]);
const showProductForm = ref(false);
const showCategoryForm = ref(false);
const form = ref({ name: '', description: '', price: '', category_ids: [] });
const catForm = ref({ name: '', description: '' });
const editingId = ref(null);
const catEditingId = ref(null);

onMounted(() => {
    // TODO: Replace with Inertia/axios fetch
    products.value = [
        { id: 1, name: 'Demo Product', description: 'Description', price: 100, categories: [{id:1, name:'Cat1'}] },
    ];
    categories.value = [
        { id: 1, name: 'Cat1', description: 'Category 1' },
        { id: 2, name: 'Cat2', description: 'Category 2' },
    ];
});

function resetForm() {
    form.value = { name: '', description: '', price: '', category_ids: [] };
    editingId.value = null;
}
function resetCatForm() {
    catForm.value = { name: '', description: '' };
}
function editProduct(product) {
    form.value = {
        name: product.name,
        description: product.description,
        price: product.price,
        category_ids: product.categories.map(c => c.id),
    };
    editingId.value = product.id;
    showProductForm.value = true;
}
function saveProduct() {
    if (editingId.value) {
        router.put(`/products/${editingId.value}`, form.value, {
            onSuccess: () => {
                resetForm();
                showProductForm.value = false;
            },
        });
    } else {
        router.post('/products', form.value, {
            onSuccess: () => {
                resetForm();
                showProductForm.value = false;
            },
        });
    }
}
function deleteProduct(id) {
    if (confirm('Are you sure you want to delete this product?')) {
        router.delete(`/products/${id}`);
    }
}
function editCategory(cat) {
    catForm.value = { name: cat.name, description: cat.description };
    catEditingId.value = cat.id;
    showCategoryForm.value = true;
}
function saveCategory() {
    if (catEditingId.value) {
        router.put(`/categories/${catEditingId.value}`, catForm.value, {
            onSuccess: () => {
                resetCatForm();
                showCategoryForm.value = false;
                catEditingId.value = null;
            },
        });
    } else {
        router.post('/categories', catForm.value, {
            onSuccess: () => {
                resetCatForm();
                showCategoryForm.value = false;
            },
        });
    }
}
function deleteCategory(id) {
    if (confirm('Are you sure you want to delete this category?')) {
        router.delete(`/categories/${id}`);
    }
}
</script>

<template>
    <Head title="Dashboard" />
    <AuthenticatedLayout>
        <template #header>
            <div class="flex items-center justify-between">
                <div class="flex gap-4">
                    <button :class="tab==='products' ? 'border-b-2 border-blue-600 text-blue-700' : 'text-gray-500'" class="px-4 py-2 font-semibold focus:outline-none" @click="tab='products'">Products</button>
                    <button :class="tab==='categories' ? 'border-b-2 border-blue-600 text-blue-700' : 'text-gray-500'" class="px-4 py-2 font-semibold focus:outline-none" @click="tab='categories'">Categories</button>
                </div>
                <div>
                    <button v-if="tab==='products'" @click="showProductForm=true; resetForm();" class="bg-blue-600 text-white px-4 py-2 rounded shadow hover:bg-blue-700">Create Product</button>
                    <button v-if="tab==='categories'" @click="showCategoryForm=true; resetCatForm();" class="bg-blue-600 text-white px-4 py-2 rounded shadow hover:bg-blue-700">Create Category</button>
                </div>
            </div>
        </template>
        <div class="py-8">
            <div class="mx-auto max-w-7xl sm:px-6 lg:px-8">
                <div class="overflow-hidden bg-white shadow-sm sm:rounded-lg p-6">
                    <!-- Products Tab -->
                    <div v-if="tab==='products'">
                        <!-- Product List -->
                        <table class="min-w-full table-auto border mb-8">
                            <thead>
                                <tr class="bg-gray-100">
                                    <th class="px-4 py-2 border">Name</th>
                                    <th class="px-4 py-2 border">Description</th>
                                    <th class="px-4 py-2 border">Price</th>
                                    <th class="px-4 py-2 border">Categories</th>
                                    <th class="px-4 py-2 border">Actions</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="product in products" :key="product.id">
                                    <td class="border px-4 py-2">{{ product.name }}</td>
                                    <td class="border px-4 py-2">{{ product.description }}</td>
                                    <td class="border px-4 py-2">{{ product.price }}</td>
                                    <td class="border px-4 py-2">
                                        <span v-for="cat in product.categories" :key="cat.id" class="inline-block bg-gray-200 rounded px-2 py-1 mr-1">{{ cat.name }}</span>
                                    </td>
                                    <td class="border px-4 py-2">
                                        <button @click="editProduct(product)" class="text-blue-600 mr-2">Edit</button>
                                        <button @click="deleteProduct(product.id)" class="text-red-600">Delete</button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                        <!-- Product Form Modal -->
                        <div v-if="showProductForm" class="fixed inset-0 bg-black bg-opacity-30 flex items-center justify-center z-50">
                            <div class="bg-white rounded-lg shadow-lg p-8 w-full max-w-md relative">
                                <button @click="showProductForm=false" class="absolute top-2 right-2 text-gray-400 hover:text-gray-700">&times;</button>
                                <h3 class="text-lg font-bold mb-4">{{ editingId ? 'Edit' : 'Create' }} Product</h3>
                                <form @submit.prevent="saveProduct" class="space-y-4">
                                    <div>
                                        <label class="block font-medium">Name</label>
                                        <input v-model="form.name" class="w-full border rounded p-2" required />
                                    </div>
                                    <div>
                                        <label class="block font-medium">Description</label>
                                        <textarea v-model="form.description" class="w-full border rounded p-2"></textarea>
                                    </div>
                                    <div>
                                        <label class="block font-medium">Price</label>
                                        <input v-model="form.price" type="number" step="0.01" class="w-full border rounded p-2" required />
                                    </div>
                                    <div>
                                        <label class="block font-medium">Categories</label>
                                        <select v-model="form.category_ids" multiple class="w-full border rounded p-2">
                                            <option v-for="cat in categories" :key="cat.id" :value="cat.id">{{ cat.name }}</option>
                                        </select>
                                    </div>
                                    <div class="flex gap-2">
                                        <button type="submit" class="bg-blue-600 text-white px-4 py-2 rounded">{{ editingId ? 'Update' : 'Create' }} Product</button>
                                        <button type="button" @click="resetForm(); showProductForm=false;" class="px-4 py-2 rounded border">Cancel</button>
                                    </div>
                                </form>
                            </div>
                        </div>
                    </div>
                    <!-- Categories Tab -->
                    <div v-if="tab==='categories'">
                        <table class="min-w-full table-auto border mb-8">
                            <thead>
                                <tr class="bg-gray-100">
                                    <th class="px-4 py-2 border">Name</th>
                                    <th class="px-4 py-2 border">Description</th>
                                    <th class="px-4 py-2 border">Actions</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="cat in categories" :key="cat.id">
                                    <td class="border px-4 py-2">{{ cat.name }}</td>
                                    <td class="border px-4 py-2">{{ cat.description }}</td>
                                    <td class="border px-4 py-2">
                                        <button @click="editCategory(cat)" class="text-blue-600 mr-2">Edit</button>
                                        <button @click="deleteCategory(cat.id)" class="text-red-600">Delete</button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                        <!-- Category Form Modal -->
                        <div v-if="showCategoryForm" class="fixed inset-0 bg-black bg-opacity-30 flex items-center justify-center z-50">
                            <div class="bg-white rounded-lg shadow-lg p-8 w-full max-w-md relative">
                                <button @click="showCategoryForm=false; catEditingId=null;" class="absolute top-2 right-2 text-gray-400 hover:text-gray-700">&times;</button>
                                <h3 class="text-lg font-bold mb-4">{{ catEditingId ? 'Edit' : 'Create' }} Category</h3>
                                <form @submit.prevent="saveCategory" class="space-y-4">
                                    <div>
                                        <label class="block font-medium">Name</label>
                                        <input v-model="catForm.name" class="w-full border rounded p-2" required />
                                    </div>
                                    <div>
                                        <label class="block font-medium">Description</label>
                                        <textarea v-model="catForm.description" class="w-full border rounded p-2"></textarea>
                                    </div>
                                    <div class="flex gap-2">
                                        <button type="submit" class="bg-blue-600 text-white px-4 py-2 rounded">{{ catEditingId ? 'Update' : 'Create' }} Category</button>
                                        <button type="button" @click="resetCatForm(); showCategoryForm=false; catEditingId=null;" class="px-4 py-2 rounded border">Cancel</button>
                                    </div>
                                </form>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>
