<template>
    <div @click="changeitem">
        <div class="row">
            <q-space />
            <div
                style="border-radius: 15px;background: rgba(0, 0, 0, 0.2);"
                class="row"
            >
                <q-chip
                    :class="selecteditem === 'plus' ? 'selecteditem' : 'unselecteditem'"
                    :text-color="selecteditem === 'plus' ? 'white' : 'grey-6'"
                    class="q-ma-none"
                    size="sm"
                >
                    درآمد
                </q-chip>
                <q-chip
                    :class="selecteditem === 'mines' ? 'selecteditem' : 'unselecteditem'"
                    :text-color="selecteditem === 'mines' ? 'white' : 'grey-6'"
                    class="q-ma-none"
                    size="sm"
                >
                    هزینه
                </q-chip>
            </div>
        </div>
        <span class="ellipsis full-width text-white text-h6">
            <span v-if="selecteditem === 'plus'">
                <!-- for plus --> +20 تومان
            </span>
            <span v-else>
                <!-- for mines --> -40 تومان
            </span>
        </span>
    </div>
</template>
<script>
import { ref } from 'vue'
export default {
  props: ['amounts'],
  setup (amounts) {
    const selecteditem = ref('mines')
    function changeitem () {
      if (selecteditem.value === 'plus') {
        selecteditem.value = 'mines'
      } else {
        selecteditem.value = 'plus'
      }
    }
    return {
      changeitem,
      selecteditem
    }
  }
}
</script>

<style>
.selecteditem {
    background: rgba(0, 0, 0, 0.4);
}
.unselecteditem {
    background: rgba(0, 0, 0, 0);
}
</style>
