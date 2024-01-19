<template>
    <div>
        <h2>Contact List</h2>
        
        <button @click="sortContacts">Sort by Name</button>
        
        <ul>
            <li v-for="contact in contacts" :key="contact.id">{{ contact.fullName }}</li>
        </ul>
        <div v-if="loading">Loading...</div>
        <div v-if="error">Error fetching contacts: {{ error }}</div>
        
        <h3>Search list by name</h3>
        <input v-model="searchTerm" placeholder="Search by name" />
        <button @click="searchContacts">Search</button>


        <h3>Add contact</h3>
        <form @submit.prevent="addContact">
            <label for="name">Name:</label>
            <input v-model="newContact.fullName" type="text" id="name" required><br />

            <label for="codeName">Code Name:</label>
            <input v-model="newContact.codeName" type="text" id="codeName" required><br />

            <label for="phoneNumber">Phone:</label>
            <input v-model="newContact.phoneNumber" type="tel" id="phoneNumber" required><br />

            <button type="submit">Add Contact</button>
        </form>

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
            newContact: {
                fullName: '',
                codeName: '',
                phoneNumber: '',
            },
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
            },
            async addContact() {
            try {
                const response = await fetch('http://localhost:8080/api/contacts', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify(this.newContact),
                });

                if (!response.ok) {
                    throw new Error(`HTTP error! Status: ${response.status}`);
                }

                const addedContact = await response.json();
                this.contacts.push(addedContact); // Assuming the backend returns the added contact
                this.resetForm(); // Optional: Clear the form after successful addition
            } catch (error) {
                console.error('Error adding contact:', error);
                this.error = `Error adding contact: ${error.message}`;
                }
            },
            // Optional: Method to reset the form after successful addition
            resetForm() {
                this.newContact = {
                    name: '',
                    email: '',
                    phone: '',
                };
            },
        },
    };
</script>