<template>
  <q-page class="bg-primary q-pb-xl">
    <q-list bordered>
      <q-separator v-if="amounts.length > 0" color="orange" />
      <q-slide-item
        v-for="(amount,index) in amounts"
        :key="amount.id"
        @left="onLeft"
        @right="onRight(index)"
        left-color="green"
        right-color="red"
      >
        <template v-slot:left>
          <q-card flat square class="bg-transparent">
            <q-card-section horizontal>
              <q-icon size="xs" name="done" />
              <div class="ellipsis">
                تغییر وضعیت به انجام شد
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
              :input-class="[{'text-green insetshadow q-pl-sm' : amount.type === 'plus'}, {'text-red insetshadow q-pl-sm' : amount.type !== 'plus'}]"
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
                  :class="amount.type === 'plus' ? 'text-green' : 'text-red' "
                >
                  {{ amount.text }}
                </div>
              </q-card-section>
            </q-card>
          </div>
          <div @click="selectamoutfunc(amount, 'price')" class="col">
            <q-input
              v-if="selectamount.id === amount.id && clickeditem === 'price'"
              :input-class="[{'text-green insetshadow q-pr-sm' : amount.type === 'plus'}, {'text-red insetshadow q-pr-sm' : amount.type !== 'plus'}]"
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
                  :class="amount.type === 'plus' ? 'text-green' : 'text-red' "
                >
                  <span v-if="amount.type === 'plus'"> + </span>
                  <span v-else> - </span>
                  <span v-if="amount.price === ''"> 0 </span>
                  <span v-else> {{ amount.price }} </span>
                  <span> تومان </span>
                </div>
              </q-card-section>
            </q-card>
          </div>
          <q-separator vertical color="grey-3" />
          <div @click="selectamoutfunc(amount, 'date')" class="col">
            <q-input
              v-if="selectamount.id === amount.id && clickeditem === 'date'"
              input-class="insetshadow q-pr-sm"
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
                      <span>
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
          <q-btn class="ellipsis full-width">
            اضافه کردن درآمد
          </q-btn>
        </div>
        <q-separator vertical />
        <div @click="storeExpenseAmount" class="col">
          <q-btn class="ellipsis full-width">
            اضافه کردن هزینه
          </q-btn>
        </div>
      </q-card-actions>
      </q-card>
    </div>
  </q-page>
</template>

<script>
import { useQuasar } from 'quasar'
import { onBeforeUnmount, defineComponent, reactive, ref, watch } from 'vue'

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
      date: null
    })
    const uniqeID = ref(0)
    const amounts = ref([])

    const $q = useQuasar()
    let timer

    function storeIncomeAmount () {
      const amount = reactive({
        id: uniqeID.value + 1,
        example: 'مثل حقوق',
        text: '',
        type: 'plus',
        price: '',
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
        type: 'mines',
        price: '',
        date: null
      })
      uniqeID.value++
      amounts.value.push(amount)
      selectamoutfunc(amount, 'text')
    }
    function updateAmount (index) {
      //
    }
    function deleteAmount (index) {
      amounts.value.splice(index, 1)
    }
    watch(amounts.value, (amounts, prevamounts) => {
      // console.log(amounts)
    })

    function selectamoutfunc (amountdata, item) {
      selectamount.id = amountdata.id
      selectamount.text = amountdata.text
      selectamount.price = amountdata.price
      selectamount.date = amountdata.date
      clickeditem.value = item
    }
    function finalize (reset) {
      timer = setTimeout(() => {
        reset()
      }, 1000)
    }
    function disableselect () {
      if (clickeditem.value !== null) {
        clickeditem.value = null
      }
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
      deleteAmount,
      amounts,
      onLeft ({ reset }) {
        $q.notify('done or not done')
        finalize(reset)
      },
      finalize,
      onRight (index) {
        deleteAmount(index)
        $q.notify('delete')
      }
    }
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
