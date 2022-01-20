<template>
  <q-page class="bg-primary">
    <q-list bordered>
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
        <q-item class="q-pa-none">
          <q-item-section>
            <div class="row">
              <div @click="selectamoutfunc(amount, 'text')" class="col-5">
                <q-input
                  v-if="selectamount.id === amount.id && clickeditem === 'text'"
                  input-class="inset-shadow q-pl-sm"
                  dense
                  bg-color="white"
                  borderless
                  v-model="amount.text"
                  placeholder="مثل خرید لباس" label=""
                />
                <q-card v-else flat square>
                  <q-card-section>
                    <div class="ellipsis">
                      {{ amount.text }}
                    </div>
                  </q-card-section>
                </q-card>
              </div>
              <div @click="selectamoutfunc(amount, 'price')" class="col-4">
                <q-input
                  v-if="selectamount.id === amount.id && clickeditem === 'price'"
                  input-class="inset-shadow q-pl-sm"
                  dense
                  bg-color="white"
                  borderless
                  v-model="amount.price"
                />
                <q-card v-else flat square>
                  <q-card-section>
                    <div class="ellipsis">
                      {{ amount.price }} تومان
                    </div>
                  </q-card-section>
                </q-card>
              </div>
              <div @click="selectamoutfunc(amount, 'date')" class="col-3">
                <q-input
                  v-if="selectamount.id === amount.id && clickeditem === 'date'"
                  input-class="inset-shadow q-pl-sm"
                  dense
                  bg-color="white"
                  borderless
                  v-model="amount.date"
                />
                <q-card v-else flat square>
                  <q-card-section>
                    <div class="ellipsis">
                      <span style="font-size: 10px;" class="column justify-center">تاریخ</span>
                      {{ amount.date }}
                    </div>
                  </q-card-section>
                </q-card>
              </div>
            </div>
          </q-item-section>
        </q-item>
        <q-separator color="orange" />
      </q-slide-item>
    </q-list>
    <div class="row text-center q-pb-xl">
      <div class="col-6">
        <q-btn @click="storeAmount" :ripple="false" color="primary full-width inset-shadow shadow-4">
          <div class="ellipsis">
            اضافه کردن درآمد
          </div>
        </q-btn>
      </div>
      <div class="col-6">
        <q-btn @click="storeAmount" :ripple="false" color="primary full-width inset-shadow">
          <div class="ellipsis">
            اضافه کردن هزینه
          </div>
        </q-btn>
      </div>
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
      text: '',
      price: 0,
      date: null
    })
    const uniqeID = ref(0)
    const amounts = ref([])

    const $q = useQuasar()
    let timer

    function storeAmount () {
      const amount = reactive({
        id: uniqeID.value + 1,
        text: '',
        price: 0,
        date: null
      })
      uniqeID.value++
      amounts.value.push(amount)
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

    onBeforeUnmount(() => {
      clearTimeout(timer)
    })

    return {
      clickeditem,
      selectamount,
      selectamoutfunc,
      storeAmount,
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
</style>
