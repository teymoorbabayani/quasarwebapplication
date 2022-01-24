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
                <!-- for balance --> {{ balance }} تومان
                <span>{{ balancetype }}</span>
            </span>
            <span v-else>
                <!-- for paid --> {{ paid }} تومان
                <span>{{ paidtype }}</span>
            </span>
        </span>
    </div>
</template>
<script>
import { ref, computed } from 'vue'
export default {
  props: ['amounts'],
  setup (props) {
    const selecteditem = ref('balance')
    function changeitem () {
      if (selecteditem.value === 'balance') {
        selecteditem.value = 'paid'
      } else {
        selecteditem.value = 'balance'
      }
    }
    const paid = computed({
      get: () => {
        const result = ref(0)
        if (props.amounts.length > 0) {
          props.amounts.forEach(amount => {
            if (amount.paid) {
              if (amount.type === 'income') {
                result.value = result.value + Number(amount.price)
              } else {
                result.value = result.value - Number(amount.price)
              }
            }
          })
        }
        if (result.value < 0) {
          return result.value * -1
        } else {
          return result
        }
      }
    })
    const paidtype = computed({
      get: () => {
        const result = ref(0)
        if (props.amounts.length > 0) {
          props.amounts.forEach(amount => {
            if (amount.paid) {
              if (amount.type === 'income') {
                result.value = result.value + Number(amount.price)
              } else {
                result.value = result.value - Number(amount.price)
              }
            }
          })
        }
        if (result.value < 0) {
          return '-'
        } else {
          return ''
        }
      }
    })
    const balancetype = computed({
      get: () => {
        const result = ref(0)
        if (props.amounts.length > 0) {
          props.amounts.forEach(amount => {
            if (amount.type === 'income') {
              result.value = result.value + Number(amount.price)
            } else {
              result.value = result.value - Number(amount.price)
            }
          })
        }
        if (result.value < 0) {
          return '-'
        } else {
          return ''
        }
      }
    })
    const balance = computed({
      get: () => {
        const result = ref(0)
        if (props.amounts.length > 0) {
          props.amounts.forEach(amount => {
            if (amount.type === 'income') {
              result.value = result.value + Number(amount.price)
            } else {
              result.value = result.value - Number(amount.price)
            }
          })
        }
        if (result.value < 0) {
          return result.value * -1
        } else {
          return result
        }
      }
    })
    return {
      paid,
      paidtype,
      balance,
      balancetype,
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
