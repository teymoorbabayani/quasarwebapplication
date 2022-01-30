<template>
  <q-layout view="lHh Lpr lFf">
    <q-header class="shadow-up-16">
      <q-toolbar class="text-center">

        <q-toolbar-title class="full-width artinSharp text-h4">
          <span class="bg-red">
            <div>
              <div>
                برنامه ریز بودجه <q-badge align="top" color="primary">فاجت</q-badge>
              </div>
            </div>
          </span>
        </q-toolbar-title>

      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from 'vue'

export default defineComponent({
  name: 'MainLayout',
  setup () {
    const leftDrawerOpen = ref(false)

    return {
      leftDrawerOpen,
      toggleLeftDrawer () {
        leftDrawerOpen.value = !leftDrawerOpen.value
      }
    }
  }
})
</script>
