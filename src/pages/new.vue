<template>
  <q-page class="bg-primary" style="padding-bottom: 200px">
    <q-card>
      <div class="row q-py-md">
      hi
        <div class="col-12 q-pa-sm">
          <q-btn class="full-width" color="grey-9" label="ذخیره جی سان" @click="writeSecretFile" />
        </div>
        <div class="col-12 q-pa-sm">
          <q-btn class="full-width" color="grey-9" label="اضافه کردن جی سان" @click="addToSecretFile" />
        </div>
        <div class="col-12 q-pa-sm">
          <q-btn class="full-width" color="grey-9" label="حذف کردن جی سان" @click="removeFromSecretFile" />
        </div>
        <div class="col-12 q-pa-sm">
          <q-btn class="full-width" color="grey-9" label="نمایش اطلاعات" @click="readSecretFile" />
        </div>
        <div class="col-12 q-pa-sm">
          <q-btn class="full-width" color="grey-9" label="حذف اطلاعات" @click="deleteSecretFile" />
        </div>
        <div v-if="mydata !== null">
            {{ mydata }}
        </div>
      </div>
    </q-card>
  </q-page>
</template>

<script>
import { defineComponent, reactive, ref } from 'vue'
import { useQuasar } from 'quasar'
import { Filesystem, Directory, Encoding } from '@capacitor/filesystem'

export default defineComponent({
  name: 'PageIndex',
  setup () {
    const $q = useQuasar()
    const uniqeID = ref(0)
    const mydata = ref(null)
    const writeSecretFile = async () => {
      const amount = reactive({
        id: uniqeID.value + 1,
        example: 'مثل حقوق',
        text: '',
        type: 'income',
        price: '',
        paid: false,
        date: null
      })
      try {
        await Filesystem.writeFile({
          path: 'thisisatest.json',
          data: JSON.stringify(amount),
          directory: Directory.External,
          encoding: Encoding.UTF8
        })
        $q.dialog({
          dark: true,
          title: 'Alert2',
          message: 'writedFile'
        }).onOk(() => {
        })
      } catch (e) {
        $q.dialog({
          dark: true,
          title: 'Alert2',
          message: 'error' + e
        }).onOk(() => {
        })
      }
    }

    const addToSecretFile = async () => {
      /* const amount = reactive({
        id: uniqeID.value + 1,
        example: 'مثل حقوق',
        text: '',
        type: 'income',
        price: '',
        paid: false,
        date: null
      }) */
      const teymoordata = ref('teymoor')
      try {
        await Filesystem.writeFile({
          path: 'thisisatest.text',
          data: teymoordata.value,
          directory: Directory.External,
          encoding: Encoding.UTF8
        })
        $q.dialog({
          dark: true,
          title: 'Alert2',
          message: 'writedFile'
        }).onOk(() => {
        })
      } catch (e) {
        $q.dialog({
          dark: true,
          title: 'Alertcatch',
          message: 'error' + e
        }).onOk(() => {
        })
      }
    }
    const removeFromSecretFile = async () => {
      const amount = reactive({
        id: uniqeID.value + 1,
        example: 'مثل حقوق',
        text: '',
        type: 'income',
        price: '',
        paid: false,
        date: null
      })
      try {
        await Filesystem.writeFile({
          path: 'thisisatest.json',
          data: amount.value,
          directory: Directory.External,
          encoding: Encoding.UTF8
        })
        $q.dialog({
          dark: true,
          title: 'Alert2',
          message: 'writedFile'
        }).onOk(() => {
        })
      } catch (e) {
        $q.dialog({
          dark: true,
          title: 'Alert2',
          message: 'error' + e
        }).onOk(() => {
        })
      }
    }

    const readSecretFile = async () => {
      try {
        // data
        const contents = await Filesystem.readFile({
          path: 'thisisatest.json',
          directory: Directory.External,
          encoding: Encoding.UTF8
        })
        // mydata.value = JSON.parse(contents.data)
        mydata.value = contents.data
        // address file
        const title = await Filesystem.getUri({
          path: 'thisisatest.json',
          directory: Directory.External,
          encoding: Encoding.UTF8
        })
        $q.dialog({
          dark: true,
          title: title.uri,
          message: JSON.parse(contents.data).example
        }).onOk(() => {
        })
      } catch (e) {
        $q.dialog({
          dark: true,
          title: 'Alert2',
          message: 'error' + e
        }).onOk(() => {
        })
      }
    }
    const deleteSecretFile = async () => {
      console.log('deleteSecretFile')
      await Filesystem.deleteFile({
        path: 'thisisatest.json',
        directory: Directory.External
      })
      $q.dialog({
        dark: true,
        title: 'Alert',
        message: 'deleteSecretFile'
      }).onOk(() => {
      })
    }

    return {
      mydata,
      // for test
      writeSecretFile,
      addToSecretFile,
      removeFromSecretFile,
      readSecretFile,
      deleteSecretFile
      // end test
    }
  }
})
</script>
