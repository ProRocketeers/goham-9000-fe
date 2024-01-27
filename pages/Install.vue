<script lang="ts">
import {defineComponent} from 'vue'
import axios from "axios";
// import Vue from "vue";
// import VueAxios from "vue-axios";

const BACKEND_URL = "http://localhost:3000"
const STATUS = {
  CREATED: "CREATED",
  CLONED: "CLONED",
  DOCKER_IMAGE_BUILT: "IMG_BUILT",
  DOCKER_IMAGE_UPLOADED: "IMG_UPLOADED",
  DEPLOYED: "DEPLOYED",
  CLEARED: "CLEARED",
}
// Vue.use(VueAxios, axios);
export default defineComponent({
     name: "Install",
     data() {
       return {
         projectName: "Muj Projekt",
         projectGitRepositoryUrl: "https://github.com/Fenny/fiber-hello-world",
         projectId: null,
         createdAt: null,
         status: "",
         STATUS: STATUS
       }
     },
     methods: {
       initGitRepository () {
         axios.post(`${BACKEND_URL}/repository`, {
           gitUrl: this.projectGitRepositoryUrl,
           name: this.projectName
         }).then((response) => {
           this.projectId = response.data.ID
           this.createdAt = response.data.CreatedAt
           this.status = response.data.Status
         })
       },
       cloneRepository () {
         axios.post(`${BACKEND_URL}/clone/${this.projectId}`).then((response) => {
           this.status = this.STATUS.CLONED
         })
       },
       buildRepository () {
         axios.post(`${BACKEND_URL}/build/${this.projectId}`).then((response) => {
           this.status = this.STATUS.DOCKER_IMAGE_BUILT
         })
       },
       deployProject () {
         axios.post(`${BACKEND_URL}/deploy/${this.projectId}`).then((response) => {
           this.status = this.STATUS.DEPLOYED
         })
       },
     },
   })
</script>

<template>
  <div>
    <v-text-field
        label="Project name"
        v-model="projectName"
    ></v-text-field>
    <v-text-field
        label="Project git repository url"
        v-model="projectGitRepositoryUrl"
    ></v-text-field>


    <v-btn
        @click="initGitRepository"
        :disabled="status != ''"
    >
      Init repository
    </v-btn>

    <v-btn
        @click="cloneRepository"
        :disabled="status != STATUS.CREATED"
    >
      Clone git repository
    </v-btn>

    <v-btn
        @click="buildRepository"
        :disabled="status != STATUS.CLONED"
    >
      Build git repository
    </v-btn>

    <v-btn
        @click="deployProject"
        :disabled="status != STATUS.DOCKER_IMAGE_BUILT"
    >
      Deploy application
    </v-btn>

    <br>

    <p>{{ projectId }}</p>
    <p>{{ createdAt }}</p>
    <p>{{ status }}</p>
  </div>

</template>

<style scoped>


</style>