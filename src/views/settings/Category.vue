<template>
    <section class="relative py-16 bg-slate-200">
        <div class="container mx-auto px-4">
            <div class="relative flex flex-col min-w-0 break-words bg-white w-full mb-6 shadow-xl rounded-lg ">
                <div
                    class="relative flex flex-col min-w-0 break-words w-full mb-6 shadow-lg rounded-lg bg-white border-0">
                    <HeaderButton back="/app/settings" title="Category settings" />

                    <div class="container px-4 mx-auto" v-on:click="openModal(null, null)">

                        <!-- <button
                            class="bg-emerald-500 text-white active:bg-emerald-600 font-bold uppercase text-xs px-4 py-2 rounded shadow hover:shadow-md outline-none focus:outline-none mr-1 mb-1 ease-linear transition-all duration-150"
                            type="button">
                            {{ $t('labels.add_new_category') }}
                        </button> -->

                    </div>

                    <!-- categories -->
                    <div class="container px-4 mx-auto " v-for="(item, k) in categories" :key="k"
                        v-on:click="showSub(k)">
                        <div class="flex border border-dotted m-1">
                            <div class="flex p-2">
                                <i :class="'fa-lg ' + item.icon + ' ' + item.type"></i>
                            </div>
                            <div class="flex lg:w-10/12 p-2">
                                <p>
                                    {{item.name}}
                                </p>
                            </div>
                        </div>


                        <div v-if="opentab == k">
                            <div class="container px-4 mx-auto " v-for="(subItem, kk) in item.subCategories" :key="kk">
                                <div class="flex border border-dotted m-1">
                                    <div class="flex p-2">
                                    </div>
                                    <div class="flex lg:w-10/12 p-2">
                                        <p class="w-full">
                                            {{ $t('app.' + subItem.slug) }}

                                            <span v-on:click="openModal(k, subItem.id)" v-if="subItem.custom == 1"
                                                class="text-xs align-right font-semibold  py-1 px-2 rounded text-slate-600 bg-slate-200 uppercase">
                                                edit
                                            </span>

                                        </p>
                                    </div>
                                </div>
                            </div>

                        </div>

                    </div>

                </div>
            </div>
        </div>
    </section>
</template>
<script>

import HeaderButton from '@/components/Button/HeaderButton.vue';
import CoreService from '../../services/core.service';
import '@vuepic/vue-datepicker/dist/main.css'

export default {
    components: {
        HeaderButton
    },
    setup() {
        const apiService = new CoreService()

        return {
            apiService
        }
    },
    data() {
        return {
            categories: [],
            opentab: null,
            form: {
                type: ['incoming', 'expenses', 'investments'],
            },
            modal: {
                id: null,
                name: null,
                parent_category: [0],
                exclude_stats: false,
            }
        }
    },
    mounted: function () {
        this.apiService.categories().then((res) => {
            let _this = this
            res.forEach(function (item) {
                _this.categories.push({
                    id: item.id,
                    name: _this.$t('app.' + item.slug),
                    icon: item.icon,
                    subCategories: item.subCategories,
                })
            })
            _this.categories.sort(function (a, b) {
                return a.name.localeCompare(b.name);
            });

        })
    },
    methods: {
        showSub(id) {
            if (this.opentab == id) {
                this.opentab = null
            } else {
                this.opentab = id
            }

        },
        openModal(id, subId) {
            let path = '';
            if (id !== null) {
                path = `${id}/${subId}`
            }
            this.$router.push({ path: `/app/settings/category/edit/${path}` })
        },
    }
};
</script>

<style>
.vacp-color-input-group {
    display: none !important;
}

.vacp-copy-button {
    display: none !important;
}

.expenses {
    color: red
}

.incoming {
    color: green
}
</style>