<template>
  <form>
    <div class="relative flex flex-col min-w-0 break-words bg-white rounded mb-6 xl:mb-0 shadow-lg items-center">
      <div class="flex flex-wrap py-3">
        <!-- ##### menu ########### -->
        <div class="w-full">
          <nav
            class="relative flex flex-wrap items-center justify-between px-2 py-3 navbar-expand-lg bg-emerald-500 rounded">
            <div class="container px-4 mx-auto flex flex-wrap items-center justify-between">
              <div class="w-full" id="example-navbar-info">
                <ul class="flex flex-row list-none ml-auto justify-center">
                  <li class="nav-item">
                    <a class="border-slate-100 px-3 py-2 flex items-center text-xs uppercase font-bold leading-snug text-white hover:opacity-75"
                      href="javascript:void(0)" v-on:click="toggleTabs(1)"
                      v-bind:class="{ 'text-emerald-600 ': action.openTab !== 1, 'text-white bg-emerald-600': action.openTab === 1 }">
                      {{ $t('labels.expenses') }}
                    </a>
                  </li>
                  <li class="nav-item">
                    <a class="border-slate-100 px-3 py-2 flex items-center text-xs uppercase font-bold leading-snug text-white hover:opacity-75"
                      href="javascript:void(0)" v-on:click="toggleTabs(2)"
                      v-bind:class="{ 'text-emerald-600 ': action.openTab !== 2, 'text-white bg-emerald-600': action.openTab === 2 }">
                      {{ $t('labels.incoming') }}
                    </a>
                  </li>
                  <li class="nav-item" v-if="isModel === false && isPlanned === false">
                    <a class="border-slate-100 px-3 py-2 flex items-center text-xs uppercase font-bold leading-snug text-white hover:opacity-75"
                      href="javascript:void(0)" v-on:click="toggleTabs(3)"
                      v-bind:class="{ 'text-emerald-600 ': action.openTab !== 3, 'text-white bg-emerald-600': action.openTab === 3 }">
                      {{ $t('labels.transfer') }}
                    </a>
                  </li>
                  <li class="nav-item" v-if="isModel === false">
                    <a class="border-slate-100 px-3 py-2 flex items-center text-xs uppercase font-bold leading-snug text-white hover:opacity-75"
                      href="javascript:void(0)" v-on:click="toggleTabs(4)"
                      v-bind:class="{ 'text-emerald-600 ': action.openTab !== 4, 'text-white bg-emerald-600': action.openTab === 4 }">
                      {{ $t('labels.debit') }}
                    </a>
                  </li>
                </ul>
              </div>
            </div>
          </nav>
        </div>
        <!-- ##### menu ########### -->
      </div>
      <div
        v-bind:class="{ 'bg-red-200': action.openTab === 1, 'bg-sky-200': action.openTab === 2, 'bg-emerald-200': action.openTab === 3, 'bg-orange-200': action.openTab === 3 }"
        class="container relative flex flex-col min-w-0 break-words w-full mb-6 rounded-lg border-0 flex-auto p-4">

        <div class="flex flex-wrap">

          <div class="lg:w-12/12 px-2 w-full "
            v-if="this.isModel === false && input.model.length > 0 && (action.openTab === 2 || action.openTab === 1) && !this.isPlanned">
            <select v-model="model" v-on:change="retriveModel()" id="model" v-if="action.models"
              class="w-full border-0 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full ease-linear transition-all duration-150">
              <option value="0">{{ $t('labels.choose_a_model') }}</option>
              <option v-for="(item, k) in input.model" :key="k" :value="item.uuid">{{ item.name }}</option>
            </select>
          </div>

          <div class="lg:w-6/12 px-2 py-2 w-full">

            <select v-model="account" id="account" required
              class="border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full ease-linear transition-all duration-150">
              <option value="-1">{{ $t('labels.choose_wallet_account') }}</option>
              <option value="" v-if="action.openTab === 3">{{ $t('labels.out_of_wallet') }}</option>
              <option v-for="item in input.account" :key="item.id" :value="item.id">{{ item.name }}</option>
            </select>
          </div>

          <div class="px-2 py-2 w-full" :class="{
            'lg:w-4/12': action.openTab == 4,
            'lg:w-6/12': action.openTab != 4,
          }">

            <select v-if="action.hidecategory == false" v-model="category" id="category"
              class="w-full border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring ease-linear transition-all duration-150">
              <option value="0">{{ $t('labels.choose_a_category') }}</option>
              <option v-for="item in input.category" :key="item.id" :value="item.id">{{ item.name }}
              </option>
            </select>

            <select v-if="action.hidecategory == true && !action.hidetransfer_to" v-model="transferto" id="transferto"
              class="w-full border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring ease-linear transition-all duration-150">
              <option :value=false>{{ $t('labels.choose_a_wallet_to_transfer_to') }}</option>
              <option value="">{{ $t('labels.out_of_wallet') }}</option>
              <option v-for="item in input.account" :key="item.id" :value="item.id">{{ item.name }}</option>
            </select>

            <select v-model="debit" v-if="action.hidetransfer_to"
              class="w-full border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring ease-linear transition-all duration-150">
              <option value="0">{{ $t('labels.choose_an_option') }}</option>
              <option value="njn76298fm">{{ $t('labels.create_new_debit') }}</option>
              <option v-for="item in input.debit" :key="item.id" :value="item.id">{{ item.name }}</option>
            </select>
          </div>

          <div class="px-2 py-2 w-full lg:w-12/12" v-if="action.openTab == 4">
            <input v-model="debit_name" type="text" :placeholder="$t('labels.debit_name')" id="debit"
              v-if="debit == 'njn76298fm'"
              class="w-full border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full ease-linear transition-all duration-150">
          </div>

          <div class="px-2 py-2 lg:w-6/12" v-if="action.openTab == 4">
            <label for="income" class="uppercase text-slate-600 text-xs font-bold mb-2">
              {{ $t('labels.incoming') }}
              <input type="radio" id="income" value="+"
                class="border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring ease-linear transition-all duration-150"
                v-model="action.debit_type" />
            </label>
          </div>

          <div class="px-2 py-2 lg:w-6/12" v-if="action.openTab == 4">
            <label for="expense" class="uppercase text-slate-600 text-xs font-bold mb-2">
              {{ $t('labels.expenses') }}
              <input type="radio" id="expense" value="-"
                class="border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring ease-linear transition-all duration-150"
                v-model="action.debit_type" />
            </label>
          </div>

        </div>
        <div class="flex flex-wrap">
          <div class="lg:w-6/12 px-2 py-2 w-full">
            <input v-on:change="amountCast()" v-model="amount" type="tel" placeholder="0" id="amount"
              class="w-full border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full ease-linear transition-all duration-150">
          </div>

          <div v-if="action.showDetails" class="lg:w-6/12 px-2 py-2 w-full">
            <select v-model="currency"
              class="w-full border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full ease-linear transition-all duration-150">
              <option v-for="item in input.currency" :key="item.id" :value="item.id">{{ $t('app.' + item.slug) }}
              </option>
            </select>
          </div>

          <div class="lg:w-6/12 px-2 py-2 w-full" v-if="isPlanned">
            <select id="planning" v-model="planning"
              class="w-full border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full ease-linear transition-all duration-150">
              <option value="0">{{ $t('labels.choose_planned_type') }}</option>
              <option required v-for="(item, k) in input.planning" :key="k" :value="item">{{ $t('labels.' + item) }}
              </option>
            </select>
          </div>

          <div class="lg:w-6/12 px-2 py-2 w-full" v-if="isPlanned">
            <span class="text-xs">{{ $t('labels.date_start') }}</span>
            <VueDatePicker v-model="date"></VueDatePicker>
          </div>

          <div class="lg:w-6/12 px-2 py-2 w-full" v-if="isPlanned">
            <span class="text-xs">{{ $t('labels.date_end') }}</span>
            <VueDatePicker v-model="end_date_time" no-today></VueDatePicker>
          </div>

        </div>

        <div v-if="!action.showDetails">
          <button v-on:click="action.showDetails = true"
            class="w-full text-xs py-1 bg-yellow-500 text-white active:bg-amber-600 font-bold uppercase rounded shadow hover:shadow-md outline-none focus:outline-none ease-linear transition-all duration-150"
            type="button">
            {{ $t('labels.show_details') }}
          </button>
        </div>

        <div v-if="action.showDetails" class="row border rounded border-slate-500 py-3 border-dashed">
          <div class="flex flex-wrap">
            <div class="lg:w-12/12 px-2 w-full text-center">
              <label class="text-xs w-full" for="tags">{{ $t('labels.choose_one_of_currently_tags') }}</label>
              <select v-model="label" multiple id="tags"
                class="w-full border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full ease-linear transition-all duration-150">
                <option
                  class="text-xs font-semibold justify-center py-1 px-2 uppercase rounded text-white-600 last:mr-0 mr-1"
                  :style="'color: #fff; background-color: ' + item.color" v-for="item in input.tags" :key="item.id"
                  :value="item.id">
                  {{ item.name }}
                </option>
              </select>
            </div>

            <div class="lg:w-12/12 px-2 py-2 w-full flex">
            <div v-for="(item, i) in input.tags" :key="i">
              <span v-on:click="removeTag(item)"
                class="ml-2 text-xs font-semibold justify-center py-1 px-2 uppercase rounded text-white-600 last:mr-0 mr-1"
                v-if="label.includes(item.id)" :style="'color: #fff; background-color: ' + item.color">{{
                  item.name
                }}
                <i class="fas fa-times close-icon"></i>
              </span>
            </div>
          </div>

          </div>

          <div class="flex flex-wrap ">
            <div class="lg:w-12/12 px-2 w-full">
              <input :placeholder="$t('labels.or_nsert_new_tag_name')" v-model="newlabel" type="text" id="tag"
                class="border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full ease-linear transition-all duration-150">
            </div>
          </div>

        </div>

        <div v-if="action.showDetails" class="flex flex-wrap py-3">

          <div class="flex w-full mb-2" v-if="!isModel && !isPlanned">
            <VueDatePicker v-model="date"></VueDatePicker>
          </div>

          <div class="w-full flex mb-2">
            <select v-model="payment_type" id="payment_type"
              class="w-full border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full ease-linear transition-all duration-150">
              <option v-for="item in input.payment_type" :key="item.id" :value="item.id">{{ $t('app.' + item.slug)
                }}</option>
            </select>
          </div>

          <!-- <CheckboxButton v-if="!isModel && !isPlanned" @update:active="confirmed = !confirmed" :status="confirmed"
            :label="$t('labels.payment_confirm')" /> -->

          <CheckboxButton v-if="!isModel && !isPlanned" @update:active="exclude_from_stats = !exclude_from_stats" :status="exclude_from_stats"
            :label="$t('labels.exclude_from_stats')" />


          <div class="lg:w-12/12 w-full bm-2">
            <textarea v-model="note" type="text" :placeholder="$t('labels.add_here_your_note')" id="note" rows="2"
              class="border-0 px-3 py-5 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full ease-linear transition-all duration-150" />
          </div>

          <!-- <CheckboxButton v-if="!isModel && !isPlanned" @update:active="action.save_as_model = !action.save_as_model" :status="action.save_as_model"
            :label="$t('labels.save_as_model')" /> -->

          <div class="flex py-2 border border-solid w-full border-slate-500 shadow rounded"
            v-if="isModel || action.save_as_model === true">
            <div class="px-2 w-full">
              <input v-model="name" type="text" :placeholder="$t('labels.write_temlate_name')" id="name"
                class="border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full ease-linear transition-all duration-150">
            </div>
            <div class="px-2 w-full">
              <button v-on:click="setModel()"
                class="w-full text-xs py-3 bg-yellow-500 text-white active:bg-amber-600 font-bold uppercase px-4 py-2 rounded shadow hover:shadow-md outline-none focus:outline-none ease-linear transition-all duration-150"
                type="button">
                {{ $t('labels.save_template') }}
              </button>
            </div>
          </div>
        </div>

      </div>

      <div class="flex flex-wrap py-3" v-if="this.isModel === false">
        <div class="lg:w-12/12 px-2 w-full">
          <button v-on:click="setEntry()"
            class="w-full bg-emerald-500 text-white active:bg-emerald-600 font-bold uppercase text-sm px-6 py-3 rounded-full shadow hover:shadow-lg outline-none focus:outline-none mr-1 mb-1 ease-linear transition-all duration-150"
            type="button">
            {{ $t('labels.insert_new_entry') }}
          </button>
        </div>
      </div>

      <div :class="'text-white px-6 py-4 border-0 rounded relative mb-4' + action.alert_color" v-if="this.action.alert">
        <span class="text-xl inline-block mr-5 align-middle">
          <i class="fas fa-bell"></i>
        </span>
        <span class="inline-block align-middle mr-8">
          {{ this.action.alert_message }}
        </span>
        <button v-on:click="closeAlert()"
          class="absolute bg-transparent text-2xl font-semibold leading-none right-0 top-0 mt-4 mr-6 outline-none focus:outline-none">
          <span>×</span>
        </button>
      </div>
      <AlertModal ref="alertModal" />
    </div>
  </form>
</template>

<script>
import VueDatePicker from '@vuepic/vue-datepicker';
import '@vuepic/vue-datepicker/dist/main.css'
import { useAppSettings } from '../../storage/settings.store';
import AlertModal from '../GenericComponents/AlertModal.vue';
import {libs} from '../../libs';
import CoreService from '../../services/core.service';
import CheckboxButton from '../Button/CheckboxButton.vue';

export default {
  props: {
    entryId: {
      type: String,
      default: null,
    },
    isPlanned: {
      type: Boolean,
      default: false,
    },
    isModel: {
      type: Boolean,
      default: false,
    },
    typeOfEntry: {
      type: String,
      default: 'expense'
    }
  },
  setup() {
    const settingsStore = useAppSettings()
    const settings = settingsStore.get()
    const apiService = new CoreService()

    return {
      settings, apiService
    }
  },
  data() {
    return {
      action: {
        reset: false,
        models: false,
        openTab: 1,
        alert: false,
        alert_message: null,
        alert_color: 'bg-red-500',
        hidecategory: false,
        hidedebit: false,
        dateUpdated: false,
        hidetransfer_to: false,
        disabled_debit_name: true,
        debit_type: '-',
        showDetails: false,
        isMobile: false,
        save_as_model: false,
        modelId: null,
      },
      date: null,
      amount: null,
      type: "expenses",
      category: 0,
      label: [],
      note: null,
      currency: 1,
      account: "-1",
      payment_type: 1,
      model: 0,
      newlabel: null,
      planned: false,
      confirmed: true,
      waranty: false,
      uuid: null,
      transferto: false,
      geolocalization: "",
      name: "",
      debit_name: null,
      end_date_time: null,
      planning: 0,
      exclude_from_stats: false,
      debit: null,
      input: {
        tags: [],
        category: [],
        payment: [],
        account: [],
        currency: [],
        payment_type: [],
        model: [],
        planning: ["daily", "monthly", "yearly"],
        debit: [],
      }
    }
  },
  components: {
    VueDatePicker, AlertModal, CheckboxButton
  },
  created() {
    window.alert = (message, type = 'success') => {
      this.$refs.alertModal.show(message, type);
    };
  },
  mounted() {
    const settings = this.settings
    this.action.openTab = 1
    this.action.isMobile = this.checkIfMobile()
        
    const queryString = this.$route.query
        if(queryString.showDetails === 'true') {
            this.action.showDetails = true
        } else {
          this.action.showDetails = !this.action.isMobile
        }

    this.time()
    this.getCategory()
    this.getCurrency()
    this.getAccount()
    this.getLabels()
    this.getPaymentType()
    if (this.entryId != null && this.isModel === false) {
      this.getEntry()
    } else {
      this.resetForm()
    }

    if (this.entryId != null && this.isModel === true) {
      this.action.modelId = this.entryId
      this.retriveModel()
    }

    if (this.isModel === false) {
      this.getModels()
      this.getDebit()
    }

    this.currency = settings.currency.id
    this.payment_type = settings.payment_type_id

    if (this.$route.query.show === 'payee') {
      this.toggleTabs(4)
    }

  },
  methods: {
    time() {
        let dateTime = new Date()
        this.date = dateTime.toISOString().split('T')[0] + " " + dateTime.toLocaleTimeString()
    },
    getDebit() {
      let _this = this
      this.apiService.debt().then((res) => {
        let data = res
        data.forEach(function (r) {
          _this.input.debit.push(r)
        })
      })
    },
    getCategory() {
      let _this = this
      this.apiService.subCategories().then((res) => {
        let data = res
        data.forEach(function (sub) {
          _this.input.category.push({
            id: sub.id,
            name: _this.$t('app.' + sub.slug),
          })
        })
        _this.input.category.sort(function (a, b) {
          return a.name.localeCompare(b.name);
        });

      })
    },
    getPaymentType() {
      let _this = this
      this.apiService.paymentstype().then((res) => {
        res.forEach(function (r) {
          _this.input.payment_type.push(r)
        })
      })
    },
    getModels() {
      let _this = this
      this.apiService.model().then((res) => {
        let data = res
        if (data.length > 0) {
          _this.action.models = true
        }
        data.forEach(function (r) {
          _this.input.model.push(r)
        })
      })
    },
    getEntry() {
      let _this = this
      this.action.reset = true

      this.toggleTabs(this.typeOfEntry)
      this.apiService.getEntryDetail(this.entryId, this.isPlanned).then((res) => {
        let model = res

        _this.amount = Math.abs(model.amount)
        if (model.type == 'incoming') {
          _this.action.openTab = 2
        }

        if (model.type == 'expenses') {
          _this.action.openTab = 1
        }

        if (model.type == 'transfer') {
          _this.action.openTab = 3
        }

        if (model.type == 'debit') {
          _this.action.openTab = 4
        }

        _this.type = model.type
        _this.category = model.sub_category.id
        _this.note = model.note
        _this.currency = model.currency.id
        _this.account = model.wallet.id
        _this.payment_type = model.payment_type.id
        _this.waranty = model.waranty == 1 ? true : false
        _this.confirmed = model.confirmed == 1 ? true : false
        _this.action.dateUpdated = true
        _this.uuid = model.uuid
        _this.date = model.date_time
        _this.planning = model.planning
        _this.transfer_realtion = model.transfer_relation
        _this.planned = model.planning
        _this.end_date_time = model.end_date_time

        if (model.type == 'debit') {
          _this.action.hidecategory = true
          _this.action.hidetransfer_to = true
          _this.debit = model.payee_id
          if (model.amount >= 0) {
            _this.action.debit_type = '+'
          }
        }

        if (model.transfer == 1) {
          _this.action.hidecategory = true
          _this.transferto = model.transfer_id
        }

        model.labels.forEach((item) => {
          _this.label.push(item.id)
        });
      })

    },
    retriveModel() {
      const id = this.entryId === null ? this.model : this.entryId

      this.apiService.getModel(id).then((res) => {

        const model = res
        const _this = this
        _this.amount = model.amount

        if (model.type == "expenses") {
          _this.amount = _this.amount * -1
        }

        if (model.type == "incoming") {
          this.action.openTab = 2
        }

        _this.type = model.type
        _this.category = model.sub_category.id
        _this.note = model.note
        _this.currency = model.currency.id
        _this.account = model.wallet.id
        _this.payment_type = model.payment_type.id
        _this.name = model.name
        _this.action.reset = true
        _this.label = []
        _this.action.save_as_model = true
        _this.action.modelId = model.uuid

        model.labels.forEach((item) => {
          _this.label.push(item.id)
        });

      })

    },
    getLabels() {
      let _this = this
      this.apiService.labels().then((res) => {
        let data = res
        data.forEach(function (r) {
          _this.input.tags.push(r)
        })
      })
    },
    setModel() {
      const label = labels(this)

      this.validateBefore()
      let data = {
        name: this.name,
        amount: this.amount,
        note: this.note,
        labels: label,
        account_id: this.account,
        category_id: this.category,
        currency_id: this.currency,
        type: this.type,
        payment_type: this.payment_type,
      }

      if (this.type == "expenses") {
        data.amount = this.amount * -1
      }

      this.apiService.setModel(data, this.entryId).then(() => {
        alert(this.$t('messages.model_saved'), "success")
      }).catch(() => {
        alert(this.$t('messages.generic_error'), "error")
      })
    },

    validateBefore() {

      if (this.account == -1 && this.action.openTab != 3) {
        alert(this.$t('messages.validation.choose_wallet'), "error")
        return false
      }

      if (this.category == 0 && this.type != "transfer" && this.type != "debit") {
        alert(this.$t('messages.validation.choose_category'), "error")
        return false
      }

      let num = this.amount + 9
      if (this.amount == null || isNaN(num)) {
        alert(this.$t('messages.validation.insert_amount'), "error")
        return false
      }

      // transfer
      if (this.action.openTab == 3) {

        if (this.account == -1) {
          alert(this.$t('messages.validation.choose_wallet'), "error")
          return false
        }

        if (this.account == this.transferto) {
          alert(this.$t('messages.validation.choose_wallet_transfer'), "error")
          return false
        }

        if (this.transferto == -1) {
          alert(this.$t('messages.validation.choose_wallet_transfer'), "error")
          return false
        }

      }

      // debit
      if (this.action.openTab == 4) {
        if (this.debit == 'njn76298fm' && this.debit_name == null) {
          alert(this.$t('messages.validation.insert_payee'), "error")
          return false
        }

        if (this.account == this.transferto) {
          alert(this.$t('messages.validation.choose_choose_wallet_transferallet'), "error")
          return false
        }

        if (this.account == this.transferto) {
          alert(this.$t('messages.validation.choose_wallet_transfer'), "error")
          return false
        }

      }

      if(this.isPlanned) {
        if(this.end_date_time != null) {
          if(this.end_date_time < this.date) {
            // check if end date is greater than start date
            alert(this.$t('messages.validation.end_date_greater_than_start_date'), "error")
          }
        }

        if(this.date == null) {
          alert(this.$t('messages.validation.choose_start_date'), "error")
          return false
        }

        const now = new Date()
        if(this.end_date_time != null) {
          if(this.end_date_time < now.toISOString().split('T')[0]) {
            alert(this.$t('messages.validation.end_date_greater_than_today'), "error")
            return false
          }
        }

        if(this.date < now.toISOString().split('T')[0]) {
          alert(this.$t('messages.validation.start_date_greater_than_today'), "error")
          return false
        }

      }

      if(this.isModel || this.action.save_as_model) {
        if(this.name == null) {
          alert(this.$t('messages.validation.insert_model_name'), "error")
          return false
        }
      }

      return true

    },

    setEntry() {

      if (this.validateBefore()) {
        const _this = this
        const label = labels(this)

        let data = {
          amount: this.amount,
          note: this.note,
          labels: label,
          account_id: this.account,
          category_id: this.category,
          currency_id: this.currency,
          payment_type: this.payment_type,
          uuid: this.uuid,
          transfer_id: this.transferto,
          date_time: this.date,
          confirmed: this.confirmed,
          waranty: this.waranty,
          geolocalization: this.geolocalization,
          planning: this.planning,
          transfer_realtion: this.transfer_relation,
          end_date_time: this.end_date_time,
          exclude_from_stats: this.exclude_from_stats,
          type: this.type,
        }
        let path = this.type

        if (this.type == "expenses") {
          path = 'expense'
          data.amount = this.amount * -1
        }

        if (this.type == "incoming") {
          path = 'income'
        }

        if (this.type == "debit") {
          if (this.action.debit_type == '-') {
            data.amount = this.amount * -1
          } else {
            data.amount = this.amount
          }

          data.payee_id = (this.debit == 'njn76298fm') ? this.debit_name : this.debit

        }

        this.apiService.setEntry(path, data, this.isPlanned, this.entryId).then(() => {
          if (!this.entryId) {
            _this.resetForm()
          }

          this.time()
          alert(this.$t('labels.entry_saved'), "success")

        }).catch(() => {
          alert(this.$t('messages.generic_error'), "error")
        })
      }
    },
    getCurrency() {
      let _this = this
      this.apiService.currencies().then((res) => {
        let data = res
        data.forEach(function (r) {
          _this.input.currency.push(r)
        })
      })
    },
    getAccount() {
      let _this = this
      this.apiService.accounts('?order[name]=asc&filters[archived]=false').then((res) => {
        let data = res
        data.forEach(function (r) {
          _this.input.account.push(r)
        })

        //if we have only one wallet set selected
        if(_this.input.account.length >= 1) {
          _this.account = _this.input.account[0].id
        }
      })
    },
    closeAlert: function () {
      this.action.alert = false;
    },
    removeTag: function (el) {
      //remove item form label with el.id
      this.label.forEach((item, i) => {
        if (item == el.id) {
          this.label.splice(i, 1)
        }
      })
    },
    toggleTabs: function (tabNumber) {
      switch (tabNumber) {
        case 1:
        case 'expenses':
          this.type = "expenses"
          this.category = 0
          this.debit = 0
          this.debit_name = null
          this.action.hidecategory = false
          this.action.hidetransfer_to = false
          break;
        case 2:
        case 'incoming':
          this.type = "incoming"
          this.debit = 0
          this.category = 0
          this.debit_name = null
          this.action.hidecategory = false
          this.action.hidetransfer_to = false
          break;
        case 3:
        case 'transfer':
          this.type = "transfer"
          this.debit = 0
          this.debit_name = null
          this.action.hidecategory = true
          this.action.hidetransfer_to = false
          this.category = 0
          break;
        case 4:
        case 'debit':
          this.type = "debit"
          this.debit = 0
          this.debit_name = null
          this.action.hidecategory = true
          this.action.hidetransfer_to = true
          this.category = 0
          break;
        default:

      }
      this.action.openTab = tabNumber
    },
    amountCast() {
      if (this.amount != null) {
        this.amount = this.amount.replace(",", ".")
      }
    },
    checkIfMobile() {
      if (window.innerWidth <= 768) {
        return true;
      } else {
        return false;
      }
    },
    resetForm() {
        this.amount = null,
        this.label = [],
        this.note = null,
        this.model = 0,
        this.newlabel = null,
        this.action.dateUpdated = false
        this.exclude_from_stats = false
        this.time()
    }
  }
};

function labels(_this) {
  let label = []

  // first check if new label is set and add it to the label array
  if (_this.newlabel != null) {
    const tags = _this.newlabel.split(',')
    tags.forEach(function (r) {
      label.push({
        name: r,
        color: libs.generateRandomColor()
      })
    })
  }

  // then check if the defaults labels is set and add it to the label array
  if (_this.label.length > 0) {
    _this.label.forEach(function (r) {
      label.push({
        name: _this.input.tags.find(x => x.id == r).id,
        color: null
      })
    })
  }

  return label
}

</script>

<style scoped>
.dp__input {
  border: none !important
}

.h44 {
  height: 44px !important
}

input.dp__pointer.dp__input_readonly.dp__input.dp__input_icon_pad.dp__input_reg {
  border: none !important;
}
</style>
