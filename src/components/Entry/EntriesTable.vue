<template>
    <div>
        <div class="block w-full overflow-x-auto" id="entries-table">

            <div class="container px-4 mx-auto py-3 ">
                <CheckboxButton v-if="isModel == false" 
                    @update:active="action.show_planned = !action.show_planned" :status="action.show_planned"
                    :label="$t('labels.show_planned_entries')"
                />
            </div>

            <div v-if="entries.length === 0">
                <div class="text-center">
                    <p class="text-slate-400 text-lg">{{ $t('labels.no_entries_found') }}</p>
                </div>
            </div>

            <div v-for="(entry, i) in entries" :key="i">
                <div v-if="(entry.planned && action.show_planned) || (!entry.planned)"
                    class="container px-4 mx-auto py-3 border border-solid border-slate-100 shadow" :class="[
                        entry.type == 'debit'
                            ? 'bg-slate-200'
                            : '',
                        entry.type == 'transfer'
                            ? 'transfer-color'
                            : ''
                    ]">
                    <div class="flex flex-wrap">
                        <div class="flex-l w-full px-4">
                            <span v-if="isModel == false" class="text-xs block text-emerald-500 rounded ">{{ entry.date
                                }}</span>
                            <span v-if="isModel == true" class="text-xs block uppercase font-bold rounded ">{{
                                entry.name }}</span>
                        </div>
                    </div>
                    <div class="flex">
                        <div class="w-full px-4 flex-1">
                            <i v-on:click="$router.push(`/app/entries?filter_planned=0&filter_category=${entry.category.id}`)"
                                :class="'text-sky-400 ' + entry.category.icon"> </i> <span
                                class="px-2  text-xs text-slate-700">
                                {{ entry.category.name }} </span>
                            <span class="text-xs rounded block"
                                :class="[entry.type == 'debit' ? 'text-slate-900' : 'text-slate-400']">( {{ entry.wallet }}
                                )
                                {{
                                    entry.payee
                                }}</span>
                        </div>
                        <div class="w-full px-4 flex-1 text-right">
                            <span v-on:click="$router.push(`/app/entries?filter_planned=0&filter_type=${entry.type_amount}`)"
                                class="text-sm block text-slate-700 rounded amount">
                                {{ entry.amount }} <i :class="'fas fa-circle ' + entry.color_amount + ' mr-2'"></i>
                            </span>

                        </div>
                        <div class="flex-l">
                            <EntryActionDropdown>
                                <Action :onAction="() => goToRoute(i)" :label="$t('labels.edit')"
                                    iconClass="fa-solid fa-pen-to-square" />
                                <Action :onAction="() => deleteItem(i)" :label="$t('labels.delete')"
                                    iconClass="fa-solid fa-trash text-red-400" />
                            </EntryActionDropdown>
                        </div>
                    </div>

                    <div class="flex flex-wrap">
                        <div class="flex-l w-full px-4">
                            <span class="text-xs block text-slate-700 rounded ">{{ entry.note }}</span>
                        </div>

                        <div class="w-full px-4 flex-1">
                            <span class="text-xs mt-2 text-slate-700 rounded ">
                                <span v-for="(label, i) in entry.labels"
                                    v-on:click="$router.push(`/app/entries?filter_planned=0&filter_label=${label.id}`)" :key="i"
                                    class="text-xs font-semibold justify-center py-1 px-2 uppercase rounded text-white-600 last:mr-0 mr-1"
                                    :style="'color: #fff; background-color: ' + label.color">{{
                                        label.name
                                    }}</span>
                            </span>
                        </div>

                        <div class="w-full px-4 flex-1 text-right">
                            <span class="text-xs mt-2 block text-slate-700 rounded ">
                                <span v-if="entry.planned == true"
                                    class="'text-xs font-semibold justify-center py-1 px-2 uppercase rounded text-white-600 last:mr-0 mr-1 bg-red-200">{{
                                        $t("labels.planned_entry") }}</span>
                            </span>
                        </div>

                    </div>

                </div>
            </div>

        </div>
        <ConfirmModal ref="confirmModal" />
    </div>
</template>
<script>
import bootstrap from "@/assets/img/bootstrap.jpg";
import angular from "@/assets/img/angular.jpg";
import sketch from "@/assets/img/sketch.jpg";
import react from "@/assets/img/react.jpg";
import vue from "@/assets/img/react.jpg";
import EntryActionDropdown from "@/components/Dropdowns/EntryActionDropdown.vue";
import Action from "@/components/Dropdowns/Action.vue";
import ConfirmModal from '@/components/GenericComponents/ConfirmModal.vue';
import CoreService from "../../services/core.service";
import CheckboxButton from "../Button/CheckboxButton.vue";
import { useRefreshStore } from "../../storage/refresh";

export default {
    props: {
        showPlanned: {
            required: false,
            default: false,
            type: Boolean
        },
        isModel: {
            required: false,
            default: false,
            type: Boolean
        }
    },
    components: {
        EntryActionDropdown, Action, ConfirmModal, CheckboxButton
    },
    setup() {
        const refreshApp = useRefreshStore()
        const apiService = new CoreService()

        return {
            apiService, refreshApp
        }
    },
    data() {
        return {
            vue,
            react,
            sketch,
            angular,
            bootstrap,
            entries: [],
            filter: "",
            planned: false,
            wallet: null,
            selected: {
                wallet: 0
            },
            action: {
                reset: false,
                show_planned: true,
            },
            input: {
                tags: [],
                wallet: [],
            }
        };
    },
    
    created() {
        window.confirm = (message) => {
            return this.$refs.confirmModal.show(message);
        };
    },
    methods: {
        async deleteItem(index) {
            const isPlanned = this.isPlanned
            const isModel = this.isModel

            const userConfirmed = await window.confirm(this.$t('messages.delete_entry'));
            if (userConfirmed) {
                const entryUuid = this.entries[index].id
                if (isModel) {
                    this.apiService.deleteModel(entryUuid)
                } else {
                    this.apiService.deleteEntry(entryUuid, isPlanned)
                }
                this.refreshApp.set(true)
                this.deleteItemFromArray(index)
            }

        },
        deleteItemFromArray(index) {
            this.entries.splice(index, 1);
        },
        goToRoute(index) {
            const entryUuid = this.entries[index].id
            const isModel = this.isModel
            if (isModel) {
                this.$router.push({ "path": `/app/model/${entryUuid}` })
            } else {
                this.$router.push({ "path": `/app/entry/${entryUuid}` })
            }

        },
        getwallet() {
            let _this = this

            this.apiService.wallets().then((res) => {
                let data = res
                data.forEach(function (r) {
                    _this.input.wallet.push(r)
                })
            })
        },
        buildEntriesTable(res) {
            this.entries = []

            let data = res

            let _this = this
            if (data !== undefined) {
                data.forEach(function (r) {

                    let labels = []
                    r.labels.forEach((l) => {
                        if (l.name != "") {
                            labels.push(
                                {
                                    id: l.id,
                                    name: l.name,
                                    color: l.color
                                }
                            )
                        }
                    });



                    const currency = r.currency

                    let formattedDate = null
                    if (r.date_time != null) {
                        const date_time = new Date(r.date_time)
                        formattedDate = date_time.toISOString().slice(0, 19).replace('T', ' ')
                    }

                    let info = {
                        uuid: r.uuid,
                        id: r.uuid,
                        date: formattedDate,
                        amount: parseFloat(r.amount).toFixed(2) + " " + currency.icon,
                        color_amount: r.amount <= 0 ? "text-red-500" : "text-emerald-500",
                        type_amount: r.amount <= 0 ? "expenses" : "incoming",
                        note: r.note,
                        planned: r.planned == 0 || r.planned == undefined ? false : true,
                        category: {
                            id: r.sub_category.id,
                            icon: r.sub_category.category.icon,
                            name: _this.$t('app.' + r.sub_category.slug)
                        },
                        labels: labels,
                        payee: null,
                        transfer: r.type == 'transfer' ? true : false,
                        type: r.type,
                        name: r.name
                    }

                    if(r.wallet != null) {
                        info.wallet = r.wallet.name
                    } else {
                        info.wallet = _this.$t('labels.out_of_wallet')
                    }

                    if (r.transfer_to != null) {
                        info.wallet = r.wallet.name + " <-> " + r.transfer_to.name
                    }

                    if (r.payee != null) {
                        info.payee = "[ debit: " + r.payee.name + "]"
                    }

                    _this.entries.push(info)
                })
            }

        },
    }
};
</script>

<style scoped>
.transfer-color {
    background-color: #ffe08738;
}
</style>
