<template>
    <div>
      <h2>Contact List</h2>
      
      <button @click="sortContacts">Sort by Name</button>
      
      <ul>
        <li v-for="contact in contacts" :key="contact.id">{{ contact.fullName }}</li>
      </ul>
      <div v-if="loading">Loading...</div>
      <div v-if="error">Error fetching contacts: {{ error }}</div>
      
      <input v-model="searchTerm" placeholder="Search by name" />
      <button @click="searchContacts">Search</button>

    </div>
</template>

<script>
    export default {
        data() {
            return {
            contacts: [],
            loading: false,
            error: null,
            searchTerm: '',
            };
        },
        mounted() {
            // Fetch contacts when the component is mounted
            this.fetchContacts();
        },
        methods: {
            async fetchContacts() {
            // Make a GET request to your API endpoint to fetch contacts
                try {
                    this.loading = true;
                    const response = await fetch('http://localhost:8080/api/contacts');
                    const data = await response.json();
                    this.contacts = data;
                } catch (error) {
                    console.error('Error fetching contacts:', error);
                    this.error = 'Failed to fetch contacts. Please try again.';
                } finally {
                    this.loading = false;
                }
            },
            async sortContacts() {
                try {
                    const response = await fetch('http://localhost:8080/api/contacts/sorted');
                    const data = await response.json();
                    this.contacts = data;
                } catch (error) {
                    console.error('Error fetching sorted contacts:', error);
                }
            },
            async searchContacts() {
                try {
                    const response = await fetch(`http://localhost:8080/api/contacts/contactsByName?name=${this.searchTerm}`);
                    if (!response.ok) {
                        throw new Error(`HTTP error! Status: ${response.status}`);
                    }
                    const data = await response.json();
                    this.contacts = data;
                } catch (error) {
                    console.error('Error searching contacts:', error);
                    this.error = `Error searching contacts: ${error.message}`;
                }
            }
        },
    };
</script>