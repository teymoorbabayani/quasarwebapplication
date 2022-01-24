<template>
  <q-page class="bg-primary" style="padding-bottom: 200px">
    <q-list bordered>
      <q-separator v-if="amounts.length > 0" color="orange" />
      <q-slide-item
        v-for="(amount,index) in amounts"
        :key="amount.id"
        @left="onLeft(index)"
        @right="onRight(index)"
        @action ="action"
        :left-color="amount.paid ? 'primary' : 'green'"
        right-color="red"
      >
        <template v-slot:left>
          <q-card v-if="amount.paid" flat square class="bg-transparent">
            <q-card-section horizontal>
              <div class="ellipsis">
                برگشتن به حالت پرداخت نشده
              </div>
            </q-card-section>
          </q-card>
          <q-card v-else flat square class="bg-transparent">
            <q-card-section horizontal>
              <q-icon size="xs" name="done" />
              <div class="ellipsis">
                تغییر وضعیت به پرداخت شد
              </div>
            </q-card-section>
          </q-card>
        </template>
        <template v-slot:right>
          <q-card flat square class="bg-transparent">
            <q-card-section horizontal>
              <div class="ellipsis">
                حذف
              </div>
              <q-icon size="xs" color="white" name="delete" />
            </q-card-section>
          </q-card>
        </template>
        <div class="row full-width">
          <div @click="selectamoutfunc(amount, 'text')" class="col">
            <q-input
              item-aligned
              class="full-width q-pa-none"
              v-if="selectamount.id === amount.id && clickeditem === 'text'"
              :input-class="[{'text-green insetshadow q-pl-sm' : amount.type === 'income'}, {'text-red insetshadow q-pl-sm' : amount.type !== 'income'}]"
              autofocus
              hide-bottom-space
              @blur="disableselect"
              bg-color="white"
              borderless
              v-model="amount.text"
              :placeholder="amount.example"
            />
            <q-card v-else style="height:56px;" flat square>
              <q-card-section class="q-pa-sm full-height column justify-center">
                <div
                  class="ellipsis"
                  :class="[amount.type === 'income' ? 'text-green' : 'text-red', amount.paid ? 'text-strike' : '']"
                >
                  {{ amount.text }}
                </div>
              </q-card-section>
            </q-card>
          </div>
          <div @click="selectamoutfunc(amount, 'price')" class="col">
            <q-input
              v-if="selectamount.id === amount.id && clickeditem === 'price'"
              :input-class="[{'text-green insetshadow q-pr-sm' : amount.type === 'income'}, {'text-red insetshadow q-pr-sm' : amount.type !== 'income'}]"
              autofocus
              @blur="disableselect"
              bg-color="white"
              borderless
              dir="ltr"
              v-model="amount.price"
            />
            <q-card v-else style="height:56px;" flat square>
              <q-card-section class="q-pa-sm full-height column justify-center">
                <div
                  class="ellipsis"
                  :class="[amount.type === 'income' ? 'text-green' : 'text-red', amount.paid ? 'text-strike' : '']"
                >
                  <span v-if="amount.price === ''"> 0 </span>
                  <span v-else> {{ amount.price }} </span>
                  <span> تومان </span>
                  <span v-if="amount.type === 'income'"> + </span>
                  <span v-else> - </span>
                </div>
              </q-card-section>
            </q-card>
          </div>
          <q-separator vertical color="grey-3" />
          <div @click="selectamoutfunc(amount, 'date')" class="col">
            <q-input
              v-if="selectamount.id === amount.id && clickeditem === 'date'"
              input-class="insetshadow q-pr-sm text-primary"
              autofocus
              dir="ltr"
              @blur="disableselect"
              bg-color="white"
              borderless
              v-model="amount.date"
            />
            <q-card v-else style="height:56px;" flat square>
              <q-card-section class="q-pa-sm full-height column justify-center">
                <div class="ellipsis row text-grey-6">
                  <div class="col-10 column justify-center">
                    <span v-if="amount.date === null" style="font-size:10px">تاریخ</span>
                    <span v-else class="row justify-center text-primary">
                      <span
                      :class="amount.paid ? 'text-strike' : ''"
                      >
                        {{ amount.date }}
                      </span>
                    </span>
                  </div>
                  <q-space />
                  <div class="column justify-center">
                    <q-icon name="expand_more" />
                  </div>
                </div>
              </q-card-section>
            </q-card>
          </div>
        </div>
        <q-separator color="orange" />
      </q-slide-item>
    </q-list>
    <!-- btns for set income or expense -->
    <div class="text-center text-white shadow-2">
      <q-card
        square
        class="bg-transparent inset-shadow"
      >
      <q-card-actions
        class="row q-pa-none artinSharp"
        align="around"
      >
        <div @click="storeIncomeAmount" class="col">
          <q-btn size="lg" class="ellipsis full-width">
            اضافه کردن درآمد
          </q-btn>
        </div>
        <q-separator vertical />
        <div @click="storeExpenseAmount" class="col">
          <q-btn size="lg" class="ellipsis full-width">
            اضافه کردن هزینه
          </q-btn>
        </div>
      </q-card-actions>
      </q-card>
    </div>
    <!-- footer -->
    <div v-show="clickeditem === null" class="fixed-bottom bg-primary shadow-up-2">
      <q-card
        square
        class="bg-transparent"
      >
      <q-card-actions
        class="row q-pa-none artinSharp"
        align="around"
      >
        <!-- left side -->
        <div class="col cursor-pointer q-pa-sm text-left">
          <paidbalance :amounts="amounts"></paidbalance>
        </div>
        <!-- right side -->
        <div class="col cursor-pointer q-pa-sm text-right">
          <expensesincome :amounts="amounts"></expensesincome>
        </div>
      </q-card-actions>
      </q-card>
    </div>
  </q-page>
</template>

<script>
import { onBeforeUnmount, defineComponent, reactive, ref } from 'vue'
import expensesincome from 'src/components/expensesincome.vue'
import paidbalance from 'src/components/paidbalance.vue'

export default defineComponent({
  name: 'PageIndex',
  setup () {
    const clickeditem = ref(null)
    const selectamount = reactive({
      id: 0,
      example: '',
      text: '',
      type: '',
      price: '',
      paid: false,
      date: null
    })
    const uniqeID = ref(0)
    const amounts = ref([])

    let timer

    function storeIncomeAmount () {
      const amount = reactive({
        id: uniqeID.value + 1,
        example: 'مثل حقوق',
        text: '',
        type: 'income',
        price: '',
        paid: false,
        date: null
      })
      uniqeID.value++
      amounts.value.push(amount)
      selectamoutfunc(amount, 'text')
    }
    function storeExpenseAmount () {
      const amount = reactive({
        id: uniqeID.value + 1,
        example: 'مثل اجاره',
        text: '',
        type: 'expense',
        price: '',
        paid: false,
        date: null
      })
      uniqeID.value++
      amounts.value.push(amount)
      selectamoutfunc(amount, 'text')
    }
    function selectamoutfunc (amountdata, item) {
      selectamount.id = amountdata.id
      selectamount.text = amountdata.text
      selectamount.price = amountdata.price
      selectamount.date = amountdata.date
      clearTimeout(timer)
      clickeditem.value = item
    }
    function disableselect () {
      if (clickeditem.value !== null) {
        timer = setTimeout(() => {
          clickeditem.value = null
        }, 100)
      }
    }
    function finalize (reset) {
      timer = setTimeout(() => {
        reset()
      }, 1000)
    }

    function updateAmount (index) {
      //
    }

    onBeforeUnmount(() => {
      clearTimeout(timer)
    })

    return {
      disableselect,
      clickeditem,
      selectamount,
      selectamoutfunc,
      storeIncomeAmount,
      storeExpenseAmount,
      updateAmount,
      amounts,
      onLeft (index) {
        if (amounts.value[index].paid) {
          timer = setTimeout(() => {
            amounts.value[index].paid = false
          }, 1500)
        } else {
          timer = setTimeout(() => {
            amounts.value[index].paid = true
          }, 1500)
        }
      },
      finalize,
      onRight (index) {
        amounts.value.splice(index, 1)
      },
      action ({ side, reset }) {
        if (side === 'left') {
          finalize(reset)
        }
      }
    }
  },
  components: {
    expensesincome,
    paidbalance
  }
})
</script>

<style>
.q-list--separator {
  background-color: #F8831A;
}
.insetshadow {
  border: 1px solid #F8831A;
   -moz-box-shadow:    inset 0 0 5px #000000;
   -webkit-box-shadow: inset 0 0 5px #000000;
   box-shadow:         inset 0 0 5px #000000;
}
</style>
