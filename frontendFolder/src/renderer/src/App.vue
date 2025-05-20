<script setup lang="ts">
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'
import TopBar from './components/TopBar.vue'
import ContentModels from './assets/contentModels.json'

const appName = ref('plug')
const drawerVisible = ref(true)
const rail = ref(false)

onMounted(() => {
  drawerVisible.value = true
  rail.value = true
})

const handleToggleDrawer = (): void => {
  if (drawerVisible.value) {
    rail.value = !rail.value
  } else {
    drawerVisible.value = true
    rail.value = false
  }
}

const prompt = ref('')
const selectedModel = ref(null)
const contentModels = ref(ContentModels)
const sentData = ref(null)

const handleSend = (): void => {
  const dataToSend = {
    prompt: prompt.value,
    selectedModel: selectedModel.value,
    timestamp: new Date().toLocaleString()
  }

  sentData.value = dataToSend

  prompt.value = ''
  selectedModel.value = null
}

const loading = ref(false)
function load(): void {
  loading.value = true
  setTimeout(() => (handleSend(), (loading.value = false)), 3000)
}
</script>

<template>
  <v-app>
    <TopBar :app-name="appName" @toggle-drawer="handleToggleDrawer" />

    <v-navigation-drawer
      v-model="drawerVisible"
      app
      :permanent="true"
      :rail="rail"
      color="grey-darken-3"
      width="256"
      @update:rail="(newRailState) => (rail = newRailState)"
    >
      <v-list>
        <!--TODO: Fix this to be able to navigate between different pages
            and their icons can be clicked and interacted with-->
        <v-list-item prepend-icon="mdi-home">
          <v-btn variant="text"> Generate text</v-btn>
        </v-list-item>
        <v-list-item prepend-icon="mdi-cog">
          <v-btn variant="text"> Generate text</v-btn>
        </v-list-item>
      </v-list>

      <template #append>
        <div class="pa-2">
          <v-btn block @click="rail = !rail">
            <v-icon v-if="rail" icon="mdi-arrow-expand-right"></v-icon>
            <v-icon v-else icon="mdi-arrow-expand-left"></v-icon>
          </v-btn>
        </div>
      </template>
    </v-navigation-drawer>

    <v-main class="bg-grey-darken-2">
      <v-container fluid>
        <h2>This is the text place</h2>
        <!--TODO find a better design to add here for the main text generation page-->
        <v-textarea
          v-model="prompt"
          variant="outlined"
          label="What idea do you need more context on ?"
        ></v-textarea>
        <v-select
          v-model="selectedModel"
          label="Content model"
          :items="contentModels"
          item-title="model"
          item-value="id"
          variant="outlined"
        ></v-select>
        <v-btn :loading="loading" @click="load">Plug it</v-btn>

        <!--<div>
          <div v-for="model in ContentModels" :key="model.id">
            <div v-if="selectedModel == model.id">
              {{ model.description }}
            </div>
          </div>
        </div>-->

        <div v-if="sentData">
          <h2>Simulated Database Entry:</h2>
          <p><strong>Prompt:</strong> {{ sentData.prompt }}</p>
          <p><strong>Selected Model:</strong> {{ sentData.selectedModel }}</p>
          <p v-if="sentData.timestamp"><strong>Timestamp:</strong> {{ sentData.timestamp }}</p>
        </div>
        <!--<div v-else>
          <p>No data "sent" yet.</p>
        </div>-->
      </v-container>
    </v-main>
  </v-app>
</template>
