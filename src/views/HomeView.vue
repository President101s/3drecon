<template>
  <div class="home container mx-auto py-8 px-4">
    <h1 class="text-2xl font-bold mb-4">Welcome to our Website</h1>
    <p>This is a webpage to help YOU convert images into 3d model.</p>

    <label for="folder_name">Name the Project:</label>
    <input
      type="text"
      id="folder_name"
      name="folder_name"
      class="px-3 py-1 border rounded-md focus:outline-none focus:border-blue-500"
      placeholder="Enter your text..."
      v-model="folder_name"
    /><br /><br />

    <!-- Dropdown Menu -->
    <div class="mt-4">
      <label for="options" class="font-bold mb-2 px-2"
        >Select an Algorithm:</label
      >
      <select
        id="options"
        name="thealgo"
        class="border border-gray-300 rounded px-4 py-2"
        v-model="selectedAlgorithm"
      >
        <option value="OPENSfM">OPENSfM</option>
        <option value="OPENMVG">OPENMVG</option>
        <option value="OPENMVS">OPENMVS</option>
      </select>
    </div>

    <!-- <button
      class="mt-4 bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded inline-block"
      name="submit"
      @click="addData()"
    >
      Insert Data
    </button> -->
    <br /><br />
    <input type="file" ref="fileInput" multiple @change="handleFileUpload" />
    <button
      class="mt-4 bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded inline-block"
      @click="addData()"
    >
      Upload
    </button>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "HomeView",
  data() {
    return {
      folder_name: "",
      selectedAlgorithm: "OPENSfM",
      selectedFiles: [],
    };
  },

  methods: {
    handleFileUpload(event) {
      // Access the selected files from the input event
      this.selectedFiles = event.target.files;
    },
    addData() {
      const formData = new FormData();
      formData.append("folder_name", this.folder_name);
      formData.append("algorithm", this.selectedAlgorithm);
      for (let i = 0; i < this.selectedFiles.length; i++) {
        let file = this.selectedFiles[i];
        formData.append("photos[]", file);
      }

      axios
        .post("http://127.0.0.1:5000/add_data", formData)
        .then((response) => {
          console.log("Data added successfully:", response.data);
          // Optionally, you can perform additional actions after successful data insertion
        })
        .catch((error) => {
          console.error("Error adding data:", error);
        });
      this.folder_name = "";
      this.selectedAlgorithm = "OPENSfM";
      this.$refs.fileInput.value = null;
    },
  },
};
</script>
