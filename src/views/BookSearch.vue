<script setup>
    import { onMounted, ref } from 'vue';

    class Book {
        constructor(id, title, imgSrc, tags, author, url) {
            this.id = id;
            this.title = title;
            this.imgSrc = `${API_URL}/data/books/images/${imgSrc}`; 
            this.tags = tags;
            this.author = author;
            this.url = url;
        }
    };

    const API_KEY = import.meta.env.VITE_API_KEY;
    const API_URL = import.meta.env.VITE_API_URL;

    const searchQuery = ref('');
    const searchType = ref('title'); // Add this line for search type
    
    const books = ref([]);

    const isLoading = ref(false);
    const error = ref(null);

    const showWarning = ref(false);
    const isWarningLeaving = ref(false);

    const showTagModal = ref(false);
    const selectedBook = ref(null);
    const newTag = ref('');
    const tagError = ref(null);
    const isTagErrorLeaving = ref(false);

    const searchBooks = async () => {
        if (!searchQuery.value.trim()) {
            if (showWarning.value) return; 
            showWarning.value = true;
            setTimeout(() => {
                isWarningLeaving.value = true; 
                setTimeout(() => {
                    showWarning.value = false;
                    isWarningLeaving.value = false; 
                }, 100); 
            }, 2000);
            return;
        }

        isLoading.value = true;
        error.value = null;

        try {
            const response = await fetch(`${API_URL}/data/books/search?q=${encodeURIComponent(searchQuery.value)}&type=${searchType.value}`, {
                method: "GET",
                headers: {
                    "X-API-Key": API_KEY,
                    "Content-Type": "application/json"
                }
            });

            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }

            const data = await response.json();
            books.value = data.data.map(book => new Book(
                book.id,
                book.title,
                book.imgSrc,
                book.tags,
                book.author,
                book.url
            ));
        } catch (err) {
            error.value = err.message;
            console.error("Error searching books:", err);
        } finally {
            isLoading.value = false;
        }
    };

    const fetchAllBooks = async () => {
        isLoading.value = true;
        error.value = null;

        try {
            const response = await fetch(`${API_URL}/data/books/all`, {
                method: "GET",
                headers: {
                    "X-API-Key": API_KEY,
                    "Content-Type": "application/json"
                }
            });

            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }

            const data = await response.json();
            books.value = data.data.map(book => new Book(
                book.id,
                book.title,
                book.imgSrc,
                book.tags,
                book.author,
                book.url
            ));
        } catch (err) {
            error.value = err.message;
            console.error("Error fetching books:", err);
        } finally {
            isLoading.value = false;
        }
    };

    const openTagModal = (book) => {
        selectedBook.value = book;
        showTagModal.value = true;
        newTag.value = '';
    };

    const closeTagModal = () => {
        showTagModal.value = false;
        selectedBook.value = null;
    };

    const addTag = async () => {
        const trimmedTag = newTag.value.trim();
        if (!trimmedTag || !selectedBook.value) return;

        if (selectedBook.value.tags.includes(trimmedTag)) {
            tagError.value = "This tag already exists!";
            setTimeout(() => {
                isTagErrorLeaving.value = true;
                setTimeout(() => {
                    tagError.value = null;
                    isTagErrorLeaving.value = false;
                }, 100);
            }, 2000);
            return;
        }

        try {
            const response = await fetch(`${API_URL}/data/books/${selectedBook.value.id}/tags`, {
                method: 'POST',
                headers: {
                    'X-API-Key': API_KEY,
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ tag: trimmedTag })
            });

            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }

            selectedBook.value.tags.push(trimmedTag);
            closeTagModal();
        } catch (err) {
            tagError.value = err.message;
            console.error("Error adding tag:", err);
        }
    };

    onMounted(() => {
        fetchAllBooks();
    });

</script>
<template>
    <div class="books-container">

        <!-- Search Box -->
        <div class="search-container">
            <select 
                v-model="searchType"
                class="search-type"
            >
                <option value="title">Book Title</option>
                <option value="author">Author</option>
                <option value="tag">Tag</option>
            </select>
            <input
                v-model="searchQuery"
                type="text"
                :placeholder="'Search by ' + searchType + '...'"
                class="search-input"
                @keyup.enter="searchBooks"
            >
            <button @click="searchBooks" class="search-button">
                Search
            </button>
        </div>
        
        <!-- Warning Window -->
        <div v-if="showWarning" 
             :class="['warning-message', { 'fade-out': isWarningLeaving }]">
            Please enter a search term
        </div>

        <!-- Main Demonstration -->
        <div v-if="isLoading">Loading...</div>
        <div v-else-if="error">Error: {{ error }}</div>
        <div v-else class="container">
            <div v-for="book in books" :key="book.title" class="book-card">
                <img :src="book.imgSrc" alt="Book Image" class="image">
                <h2 class="title">{{ book.title }}</h2>
                <div class="tags">
                    <span v-for="tag in book.tags" :key="tag" class="tag">{{ tag }}</span>
                    <button @click="openTagModal(book)" class="add-tag-button">
                        +
                    </button>
                </div>
                <div class="author">Author: {{ book.author }}</div>
                <a :href="book.url" target="_blank" class="link">Link</a>
            </div>
        </div>

        <!-- Add Tag Modal -->
        <div v-if="showTagModal" class="modal-overlay" @click="closeTagModal">
            <div class="modal-content" @click.stop>
                <h3 class="modal-title">Add New Tag</h3>
                <input 
                    v-model="newTag"
                    type="text"
                    placeholder="Enter new tag"
                    class="tag-input"
                    @keyup.enter="addTag"
                >
                <div v-if="tagError" 
                    :class="['warning-message', { 'fade-out': isTagErrorLeaving }]">
                    {{ tagError }}
                </div>
                <div class="modal-buttons">
                    <button @click="addTag" class="add-button">Add Tag</button>
                    <button @click="closeTagModal" class="cancel-button">Cancel</button>
                </div>
            </div>
        </div>
    </div>
</template>
<style scoped>
    .books-container {
        padding: 20px;
        max-width: 1200px;
        margin: 0 auto;
    }

    .container {
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        gap: 20px;
    }

    .book-card {
        background: #f5f5f5;
        padding: 20px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        border-radius: 10px;
        text-align: center;
        transition: transform 0.2s;
    }

    .book-card:hover {
        transform: translateY(-5px);
        box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
    }

    .image {
        width: 100%;
        height: 200px;
        object-fit: cover;
        border-radius: 10px;
    }

    .title {
        font-size: 20px;
        margin: 10px 0;
    }

    .tags {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        gap: 5px;
        margin-bottom: 10px;
    }

    .tag {
        background: #ddd;
        padding: 5px 10px;
        border-radius: 5px;
        font-size: 14px;
    }

    .author {
        font-size: 14px;
        color: #555;
        margin-bottom: 10px;
    }

    .link {
        display: inline-block;
        text-decoration: none;
        color: white;
        background: #007BFF;
        padding: 8px 15px;
        border-radius: 5px;
    }

    .search-container {
        display: flex;
        gap: 10px;
        margin-bottom: 20px;
        align-items: center;
    }

    .search-input {
        flex: 1;
        padding: 10px;
        border: 1px solid #ddd;
        border-radius: 5px;
        font-size: 16px;
    }

    .search-button {
        padding: 10px 20px;
        background: #007BFF;
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        transition: background 0.2s;
    }

    .search-button:hover {
        background: #0056b3;
    }

    .search-type {
        padding: 10px;
        border: 1px solid #ddd;
        border-radius: 5px;
        font-size: 16px;
        background-color: white;
        min-width: 120px;
    }

    .warning-message {
        background-color: #fff3cd;
        color: #856404;
        padding: 12px;
        border-radius: 5px;
        margin-bottom: 20px;
        border: 1px solid #ffeeba;
        text-align: center;
        animation: fadeIn 0.2s ease-in;
    }

    .warning-message.fade-out {
        animation: fadeOut 0.1s ease-out forwards;
    }

    .add-tag-button {
        background: #007BFF;
        color: white;
        width: 24px;
        height: 24px;
        border-radius: 12px;
        border: none;
        cursor: pointer;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 18px;
        padding: 0;
        margin-left: 5px;
    }

    .modal-overlay {
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background: rgba(0, 0, 0, 0.5);
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 1000;
    }

    .modal-content {
        background: white;
        padding: 20px;
        border-radius: 8px;
        width: 300px;
    }

    .modal-title {
        margin-bottom: 15px;
        font-size: 18px;
    }

    .error-message {
        color: red;
        margin-bottom: 10px;
    }

    .tag-input {
        width: 100%;
        padding: 8px;
        border: 1px solid #ddd;
        border-radius: 4px;
        margin-bottom: 15px;
    }

    .modal-buttons {
        display: flex;
        gap: 10px;
        justify-content: flex-end;
    }

    .add-button, .cancel-button {
        padding: 8px 15px;
        border-radius: 4px;
        border: none;
        cursor: pointer;
    }

    .add-button {
        background: #007BFF;
        color: white;
    }

    .cancel-button {
        background: #6c757d;
        color: white;
    }

    /* Responsive Design */
    @media (max-width: 1024px) {
        .container {
            grid-template-columns: repeat(2, 1fr);
        }
    }

    @media (max-width: 768px) {
        .container {
            grid-template-columns: 1fr;
        }
    }

    @keyframes fadeIn {
        from {
            opacity: 0;
            transform: translateY(-10px);
        }
        to {
            opacity: 1;
            transform: translateY(0);
        }
    }

    @keyframes fadeOut {
        from {
            opacity: 1;
            transform: translateY(0);
        }
        to {
            opacity: 0;
            transform: translateY(-10px);
        }
    }
</style>