<template>
  <q-page class="bg-primary" style="padding-bottom: 200px">
    <q-list bordered>
      <q-separator v-if="amounts.length > 0" color="orange" />
      <q-slide-item
        v-for="(amount,index) in amounts"
        :key="amount.id"
        @left="onLeft(index, amount.id)"
        @right="onRight(index, amount.id)"
        @action ="action"
        :left-color="amount.paid ? 'primary' : 'green'"
        right-color="red"
      >
        <template v-slot:left>
          <q-card v-if="amount.paid" flat square class="bg-transparent">
            <q-card-section horizontal>
              <div class="ellipsis">
                بازگشت به حالت پرداخت نشده
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
              <div class="ellipsis full-width">
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
              @blur="disableselect(index, amount.id)"
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
              @blur="disableselectprice(index, amount.id)"
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
                  <span v-else> {{ separate(amount.price) }} </span>
                  <span> تومان </span>
                  <span v-if="amount.type === 'income'"> + </span>
                  <span v-else> - </span>
                </div>
              </q-card-section>
            </q-card>
          </div>
          <q-separator vertical color="grey-3" />
          <div @click="selectamoutfunc(amount, 'date')" class="col-3">
            <q-input
              v-if="selectamount.id === amount.id && clickeditem === 'date'"
              input-class="insetshadow q-pr-sm text-primary"
              autofocus
              dir="ltr"
              @blur="disableselect(index, amount.id)"
              bg-color="white"
              borderless
              v-model="amount.date"
            >
              <template v-slot:append>
                <q-popup-proxy v-model="dateOfPayment" cover transition-show="scale" transition-hide="scale">
                  <q-date
                    v-model="amount.date"
                    mask="YYYY-MM-DD"
                    calendar="persian"
                    title="تاریخ پرداخت"
                    :subtitle="amount.date"
                    today-btn
                  >
                    <div class="row items-center justify-end">
                      <q-btn @click="disableselect(index, amount.id)" v-close-popup label="انتخاب" color="primary" flat />
                      <q-space />
                      <q-btn @click="clearselect(index, amount.id)" v-close-popup label="پاک کردن" color="primary" flat />
                    </div>
                  </q-date>
                </q-popup-proxy>
                </template>
            </q-input>
            <q-card v-else style="height:56px;" flat square>
              <q-card-section class="q-pa-none q-pl-xs full-height column justify-center">
                <div class="ellipsis row text-grey-6">
                  <div class="col-8 column justify-center">
                    <span class="row justify-center" v-if="amount.date === null" style="font-size:10px">تاریخ</span>
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
                  <q-space />
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
import { onMounted, defineComponent, reactive, ref, watch } from 'vue'
import expensesincome from 'src/components/expensesincome.vue'
import paidbalance from 'src/components/paidbalance.vue'
import { useQuasar } from 'quasar'
import { Filesystem, Directory, Encoding } from '@capacitor/filesystem'

export default defineComponent({
  name: 'PageIndex',
  setup () {
    const $q = useQuasar()
    const dataformat = reactive({
      first: []
    })
    const appdata = reactive({
      uniqeID: 0
    })
    const clickeditem = ref(null)
    const dateOfPayment = ref(false)
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

    watch(dateOfPayment, (dateOfPayment, prevDateOfPayment) => {
      if (dateOfPayment === false) {
        timer = setTimeout(() => {
          clickeditem.value = null
        }, 100)
      }
    })

    let timer

    function disableselectprice (index, id) {
      let strdata = amounts.value[index].price.toString()
      const find = ['۰', '۱', '۲', '۳', '۴', '۵', '۶', '۷', '۸', '۹']
      const replace = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
      for (let i = 0; i < find.length; i++) {
        const regex = new RegExp(find[i], 'g')
        strdata = strdata.replace(regex, replace[i])
      }
      let datainvalid = false
      for (let i = 0; i < strdata.length; i++) {
        const charCode = strdata.charCodeAt(i)
        if (!/^[\u06F0-\u06F90-9]+$/g.test(String.fromCharCode(charCode)) || charCode === 32) {
          if (charCode !== 32) {
            datainvalid = true
          }
        }
      }
      if (Number(strdata) !== 0) {
        amounts.value[index].price = Number(strdata)
      } else {
        amounts.value[index].price = ''
      }
      if (datainvalid) {
        amounts.value[index].price = ''
      }
      if (clickeditem.value !== null) {
        updateAmount()
        timer = setTimeout(() => {
          clickeditem.value = null
        }, 100)
      }
    }
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
    const storeIncomeAmount = async () => { // fixed
      const amount = reactive({
        id: uniqeID.value + 1,
        example: 'مثل حقوق',
        text: '',
        type: 'income',
        price: '',
        paid: false,
        date: null
      })
      amounts.value.push(amount)
      dataformat.first = amounts.value
      await Filesystem.writeFile({
        path: 'fajetdata.json',
        data: JSON.stringify(dataformat),
        directory: Directory.External,
        encoding: Encoding.UTF8
      })
      //
      uniqeID.value++
      appdata.uniqeID = uniqeID.value
      await Filesystem.writeFile({
        path: 'appdata.json',
        data: JSON.stringify(appdata),
        directory: Directory.External,
        encoding: Encoding.UTF8
      })
      selectamoutfunc(amount, 'text')
    }
    const storeExpenseAmount = async () => { // fixed
      const amount = reactive({
        id: uniqeID.value + 1,
        example: 'مثل اجاره',
        text: '',
        type: 'expense',
        price: '',
        paid: false,
        date: null
      })
      amounts.value.push(amount)
      dataformat.first = amounts.value
      await Filesystem.writeFile({
        path: 'fajetdata.json',
        data: JSON.stringify(dataformat),
        directory: Directory.External,
        encoding: Encoding.UTF8
      })
      //
      uniqeID.value++
      appdata.uniqeID = uniqeID.value
      await Filesystem.writeFile({
        path: 'appdata.json',
        data: JSON.stringify(appdata),
        directory: Directory.External,
        encoding: Encoding.UTF8
      })
      selectamoutfunc(amount, 'text')
    }
    function selectamoutfunc (amountdata, item) {
      const olddataindex = amounts.value.findIndex(amount =>
        amount.id === selectamount.id
      )
      if (olddataindex > -1) {
        disableselect(olddataindex, selectamount.id)
      }
      selectamount.id = amountdata.id
      selectamount.text = amountdata.text
      selectamount.price = amountdata.price
      selectamount.date = amountdata.date
      clearTimeout(timer)
      clickeditem.value = item
      if (item === 'date') {
        dateOfPayment.value = true
      }
    }
    function disableselect (index, id) {
      if (clickeditem.value !== null) {
        updateAmount()
        timer = setTimeout(() => {
          clickeditem.value = null
        }, 100)
      }
    }
    function clearselect (index, id) {
      amounts.value[index].date = null
      if (clickeditem.value !== null) {
        updateAmount()
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
    const updateAmount = async () => {
      dataformat.first = amounts.value
      if (clickeditem.value !== null) {
        await Filesystem.writeFile({
          path: 'fajetdata.json',
          data: JSON.stringify(dataformat),
          directory: Directory.External,
          encoding: Encoding.UTF8
        })
      }
    }

    onMounted(() => {
      getamounts()
      getappdata()
      clearTimeout(timer)
    })

    const getamounts = async () => { // fixed
      try {
        const amountjson = await Filesystem.readFile({
          path: 'fajetdata.json',
          directory: Directory.External,
          encoding: Encoding.UTF8
        })
        amounts.value = JSON.parse(amountjson.data).first
      } catch {
        await Filesystem.writeFile({
          path: 'fajetdata.json',
          data: JSON.stringify(dataformat),
          directory: Directory.External,
          encoding: Encoding.UTF8
        })
      }
    }
    const getappdata = async () => { // fixed
      try {
        const appdatajson = await Filesystem.readFile({
          path: 'appdata.json',
          directory: Directory.External,
          encoding: Encoding.UTF8
        })
        uniqeID.value = JSON.parse(appdatajson.data).uniqeID
      } catch {
        appdata.uniqeID = 0
        await Filesystem.writeFile({
          path: 'appdata.json',
          data: JSON.stringify(appdata),
          directory: Directory.External,
          encoding: Encoding.UTF8
        })
        uniqeID.value = 0
      }
    }
    const onLeft = async (index, id) => {
      timer = setTimeout(() => {
        amounts.value[index].paid = !amounts.value[index].paid
        const data = amounts.value[index]
        $q.dialog({
          dark: true,
          title: 'hi',
          message: JSON.stringify(data)
        }).onOk(() => {
        })
        updateAmount()
      }, 1500)
    }
    function onRight (index, id) {
      $q.dialog({
        message: 'حذف شود؟',
        position: 'bottom',
        ok: {
        },
        cancel: {
          color: 'grey-8'
        }
      }).onOk(() => {
        amounts.value.splice(index, 1)
        updateAmount()
        clearInterval(timer)
      })
    }
    return {
      dateOfPayment,
      getappdata,
      getamounts,
      disableselect,
      disableselectprice,
      clearselect,
      clickeditem,
      selectamount,
      selectamoutfunc,
      storeIncomeAmount,
      storeExpenseAmount,
      updateAmount,
      amounts,
      separate,
      onLeft,
      finalize,
      onRight,
      action ({ side, reset }) {
        if (side === 'left') {
          finalize(reset)
        }
        if (side === 'right') {
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
