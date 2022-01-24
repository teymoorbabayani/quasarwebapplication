<template>
    <div @click="changeitem">
        <div class="row">
            <q-space />
            <div
                style="border-radius: 15px;background: rgba(0, 0, 0, 0.2);"
                class="row"
            >
                <q-chip
                    :class="selecteditem === 'income' ? 'selecteditem' : 'unselecteditem'"
                    :text-color="selecteditem === 'income' ? 'white' : 'grey-6'"
                    class="q-ma-none"
                    size="sm"
                >
                    درآمد
                </q-chip>
                <q-chip
                    :class="selecteditem === 'expense' ? 'selecteditem' : 'unselecteditem'"
                    :text-color="selecteditem === 'expense' ? 'white' : 'grey-6'"
                    class="q-ma-none"
                    size="sm"
                >
                    هزینه
                </q-chip>
            </div>
        </div>
        <span class="ellipsis full-width text-white text-h6">
            <span v-if="selecteditem === 'income'">
                <!-- for income --> {{ income }} تومان
            </span>
            <span v-else>
                <!-- for expense --> {{ expense }}- تومان
            </span>
        </span>
    </div>
</template>
<script>
import { ref, computed } from 'vue'
export default {
  props: ['amounts'],
  setup (props) {
    const selecteditem = ref('expense')
    function changeitem () {
      if (selecteditem.value === 'income') {
        selecteditem.value = 'expense'
      } else {
        selecteditem.value = 'income'
      }
    }
    const expense = computed({
      get: () => {
        const result = ref(0)
        if (props.amounts.length > 0) {
          props.amounts.forEach(amount => {
            if (amount.type === 'expense') {
              result.value = result.value + Number(amount.price)
            }
          })
        }
        return result
      }
    })
    const income = computed({
      get: () => {
        const result = ref(0)
        if (props.amounts.length > 0) {
          props.amounts.forEach(amount => {
            if (amount.type === 'income') {
              result.value = result.value + Number(amount.price)
            }
          })
        }
        return result
      }
    })
    return {
      expense,
      income,
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
