<template>
    <div @click="changeitem">
        <div class="row">
            <div
                style="border-radius: 15px;background: rgba(0, 0, 0, 0.2);"
                class="row"
            >
                <q-chip
                    :class="selecteditem === 'balance' ? 'selecteditem' : 'unselecteditem'"
                    :text-color="selecteditem === 'balance' ? 'white' : 'grey-6'"
                    class="q-ma-none"
                    size="sm"
                >
                    تراز
                </q-chip>
                <q-chip
                    :class="selecteditem === 'paid' ? 'selecteditem' : 'unselecteditem'"
                    :text-color="selecteditem === 'paid' ? 'white' : 'grey-6'"
                    class="q-ma-none"
                    size="sm"
                >
                    پرداخت شده
                </q-chip>
            </div>
            <q-space />
        </div>
        <span class="ellipsis full-width text-white text-h6">
            <span v-if="selecteditem === 'balance'">
                <!-- for balance --> +20 تومان
            </span>
            <span v-else>
                <!-- for paid --> -40 تومان
            </span>
        </span>
    </div>
</template>
<script>
import { ref } from 'vue'
export default {
  props: ['amounts'],
  setup (amounts) {
    const selecteditem = ref('balance')
    function changeitem () {
      if (selecteditem.value === 'balance') {
        selecteditem.value = 'paid'
      } else {
        selecteditem.value = 'balance'
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
