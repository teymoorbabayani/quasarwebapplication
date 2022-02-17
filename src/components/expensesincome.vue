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
                <!-- for income --> {{ separate(income) }} تومان
            </span>
            <span v-else>
                <!-- for expense --> {{ separate(expense) }}
                <span v-if="expense !== 0">-</span>
                 تومان
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
        return result.value
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
        return result.value
      }
    })

    function separate (Number) {
      Number += ''
      Number = Number.replace(',', '')
      const x = Number.split('.')
      let y = toEnglishDigits(x[0])
      const z = x.length > 1 ? '.' + x[1] : ''
      const rgx = /(\d+)(\d{3})/
      while (rgx.test(y)) {
        y = y.replace(rgx, '$1' + ',' + '$2')
      }
      return y + z
    }
    function toEnglishDigits (str) {
    // convert persian digits [۰۱۲۳۴۵۶۷۸۹]
      let e = '۰'.charCodeAt(0)
      str = str.replace(/[۰-۹]/g, function (t) {
        return t.charCodeAt(0) - e
      })
      // convert arabic indic digits [٠١٢٣٤٥٦٧٨٩]
      e = '٠'.charCodeAt(0)
      str = str.replace(/[٠-٩]/g, function (t) {
        return t.charCodeAt(0) - e
      })
      return str
    }
    return {
      separate,
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
