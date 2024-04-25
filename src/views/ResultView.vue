<template>
  <div class="result">
    <button @click="toggleAdminSession">
      {{ isAdmin ? "Logout" : "Login as Admin" }}
    </button>
    <div v-if="isAdmin">
      <p>Welcome Admin!</p>
      <p>{{ editedItem }}</p>
    </div>
    <div class="container mx-auto py-8 px-4">
      <h1 class="text-2xl font-bold mb-4">Past Result</h1>

      <table>
        <thead>
          <tr>
            <th
              class="px-4 py-2 bg-gray-200 text-gray-800 border border-gray-600"
            >
              ID
            </th>
            <th
              class="px-4 py-2 bg-gray-200 text-gray-800 border border-gray-600"
            >
              Record Time
            </th>
            <th
              class="px-4 py-2 bg-gray-200 text-gray-800 border border-gray-600"
            >
              Algorithm
            </th>
            <th
              class="px-4 py-2 bg-gray-200 text-gray-800 border border-gray-600"
            >
              Folder Photos
            </th>
            <th
              class="px-4 py-2 bg-gray-200 text-gray-800 border border-gray-600"
            >
              File Results
            </th>
            <th
              class="px-4 py-2 bg-gray-200 text-gray-800 border border-gray-600"
              v-if="isAdmin"
            >
              Action
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in items" :key="item.the_id" :id="item.the_id">
            <td class="px-4 py-2 border border-gray-600">{{ item.the_id }}</td>
            <td class="px-4 py-2 border border-gray-600">
              {{ item.rec_time }}
            </td>
            <td class="px-4 py-2 border border-gray-600">{{ item.algo }}</td>

            <td
              :class="[
                'px-4',
                'py-2',
                'border',
                'border-gray-600',
                item.the_id,
              ]"
              :id="'fp' + item.the_id"
            >
              {{ item.folder_photos }}
            </td>
            <td
              :class="[
                'px-4',
                'py-2',
                'border',
                'border-gray-600',
                item.the_id,
              ]"
              :id="'fr' + item.the_id"
            >
              {{ item.file_results }}
            </td>
            <td class="px-4 py-2 border border-gray-600" v-if="isAdmin">
              <button
                class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
                @click="editItem(item)"
              >
                Edit
              </button>
              <button
                class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded"
                @click="deleteItem(item.the_id)"
              >
                Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- tes -->
    <div
      id="editPopup"
      class="fixed z-10 inset-0 overflow-y-auto"
      v-if="editedItem"
    >
      <div
        class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0"
      >
        <div class="fixed inset-0 transition-opacity" aria-hidden="true">
          <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
        </div>
        <span
          class="hidden sm:inline-block sm:align-middle sm:h-screen"
          aria-hidden="true"
          >&#8203;</span
        >
        <div
          class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full"
        >
          <!-- Edit form content -->
          <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
            <!-- Edit form fields -->
            <h1 id="form_head">{{ editedItem.the_id }}</h1>
            <input
              type="text"
              class="hidden"
              name="form_head"
              id="form_head_input"
            />
            <div class="mb-4">
              <label
                for="editFolder"
                class="block text-gray-700 text-sm font-bold mb-2"
                >Edit Folder:</label
              >
              <input
                id="editFolder"
                type="text"
                v-model="editedItem.folder_photos"
                class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
              />
              <label
                for="editFile"
                class="block text-gray-700 text-sm font-bold mb-2"
                >Edit File:</label
              >
              <input
                id="editFile"
                type="text"
                v-model="editedItem.file_results"
                class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
              />
            </div>
            <!-- Buttons for save and cancel -->
            <div class="flex justify-end">
              <button
                class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline mr-2"
                name="saveEdit"
                @click="saveEdit(editedItem)"
              >
                Save
              </button>
              <button
                class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
                @click="cancelEdit()"
              >
                Cancel
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- tes -->
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "ResultView",
  data() {
    return {
      isAdmin: sessionStorage.getItem("isAdmin") === "true",
      password: "",
      items: [],
      editedItem: null, // Track the item being edited
    };
  },
  mounted() {
    // Fetch items when the component is mounted
    this.fetchItems();
  },
  methods: {
    // Fetch items from the backend
    fetchItems() {
      axios
        .get("http://127.0.0.1:5000/items")
        .then((response) => {
          this.items = response.data;
        })
        .catch((error) => {
          console.error("Error fetching data:", error);
        });
    },
    toggleAdminSession() {
      if (this.isAdmin) {
        // Logout Admin
        sessionStorage.removeItem("isAdmin");
        this.isAdmin = false;
      } else {
        // Ask for password
        const enteredPassword = prompt("Please enter the admin password:");
        // Check if password is correct (you can replace 'admin123' with your actual password)
        if (enteredPassword === "admin123") {
          // Set isAdmin to true and store in sessionStorage
          this.isAdmin = true;
          sessionStorage.setItem("isAdmin", "true");
        } else {
          alert("Incorrect password! Please try again.");
        }
      }
    },
    isEditing(item) {
      return this.editedItem === item;
    },
    editItem(item) {
      this.editedItem = { ...item }; // Make a copy of the item being edited
    },
    cancelEdit() {
      this.editedItem = null;
    },
    saveEdit(updatedItem) {
      const newformData = new FormData();

      newformData.append("the_id", updatedItem.the_id);
      newformData.append("folder_photos", updatedItem.folder_photos);
      newformData.append("file_results", updatedItem.file_results);
      // Update the item on the backend
      axios
        .post(`http://127.0.0.1:5000/items`, newformData)
        .then((response) => {
          // If the item is successfully updated, update the local items list
        })
        .catch((error) => {
          console.error("Error updating item:", error);
        })
        .finally(() => {
          // Reset editedItem after saving
          this.editedItem = null;
        });
      this.fetchItems();
    },
    deleteItem(itemId) {
      // Confirm with the user before deleting
      if (confirm("Are you sure you want to delete this item?")) {
        // Send a DELETE request to the backend API
        // const newformData = new FormData();

        // newformData.append("the_id", itemId);
        console.log(itemId);
        axios
          .delete(`http://127.0.0.1:5000/items/${itemId}`, itemId)
          .then((response) => {
            console.log("Item deleted:", response.data);
            // If the item is successfully deleted, update the local items list
            // You may want to filter out the deleted item from the items array
            this.items = this.items.filter((item) => item.the_id !== itemId);
          })
          .catch((error) => {
            console.error("Error deleting item:", error);
          });
      }
    },
  },
};
</script>
