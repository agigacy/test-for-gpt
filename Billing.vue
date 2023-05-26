<template>
    <div class="row">
        <div class="col-12">

        <div class="card pb-0 mb-0">

            <div class="input-group col-md-12">
                <div class="col-md-2">
                    <h4 class="font-weight-bold text-uppercase text-align-center pt-1">
                        Billing
                    </h4>
                    <p>
                        Total Record(s): <b>{{ filterBothBillings.length }}</b>
                    <br />
                        Total Page(s): <b>{{ pages }}</b>
                    </p>
                    </div>
                    <div class="col-md-8 mb-1">
                        <div class="input-group-prepend d-flex">
                            <!-- <input type="text" v-model="search" placeholder="Search Subject" /> -->
                            <!-- <input class="form-control" type="text" v-model="filters.companyName" placeholder="Search..." /> -->
                            <input class="form-control p-4" type="text" v-model="search" placeholder="Smart Search..." />
                            <div class="input-group-prepend">
                                <button v-show="whenSearching" v-on:click="clearText" type="button" class="btn btn-outline-secondary ml-1">
                                    <i class="fas fa-sync-alt"> Reset</i>
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="col-md-2 float-right">
                        <div class="input-group-prepend">
                            <!-- <button class="btn btn-primary btn-lg" data-toggle="modal" data-target="#exampleModal"> ORI -->
                            <button class="btn btn-outline-secondary btn-lg ml-1" @click.prevent="add">
                            <i class="fas fa-plus-circle"></i> QUICK ADD
                            </button>
                            <!-- <router-link class="btn btn-outline-primary ml-1" :to="{ name: 'billing.create' }"><a class="nav-link"><i class="fas fa-plus-circle"></i> Add New +</a></router-link> -->
                        </div>
                    </div>
                </div>
            </div>
            <div class="card">
                <spinner v-if="$root.loading"></spinner>
                <table class="table table-striped table-bordered" v-else-if="billings.length">
                    <thead>
                    <tr>
                        <th scope="col" @click="sort('id')" :class="(classes('id'))">#Id</th>
                        <th scope="col" @click="sort('billingNo')" :class="(classes('billingNo'))">Billing No.</th>
                        <th scope="col" @click="sort('fileNo')" :class="(classes('fileNo'))">File No.</th>
                        <th scope="col" @click="sort('billingType')" :class="(classes('billingType'))">Type</th>
                        <th scope="col" @click="sort('subject')" :class="(classes('subject'))">Subject</th>
                        <!-- <th scope="col" @click="sort('billingRef')" :class="(classes('billingRef'))">Ref.</th> -->
                        <th scope="col" @click="sort('totalFees')" :class="(classes('totalFees'))">Legal Fee</th>
                        <!-- <th scope="col" @click="sort('billedDisbursement')" :class="(classes('billedDisbursement'))">Disbursement</th> -->
                        <th scope="col" @click="sort('totalDisbursements')" :class="(classes('totalDisbursements'))">Total Disbursements</th>
                        <th scope="col" @click="sort('totalDiscount')" :class="(classes('totalDiscount'))">Total Discount</th>
                        <th scope="col" @click="sort('created_at')" :class="(classes('created_at'))">Date</th>
                        <th scope="col">Action</th>
                    </tr>
                    </thead>
                    <tbody>
                        <tr v-for="billing in billingsPaginated" :key="billing.id"
                            :class="{ 'table-danger': removeBillingId == billing.id }">
                        <td class="text-center">
                            <div style="width: auto; max-width: 40px;
                                            height: auto;
                                            background-color: #00657D;
                                            border: 1px solid #CCC;
                                            text-align: center; opacity: 0.9" class="rounded-pill text-white p-1 idStyle mt-1 ml-2 align-content-center">{{ billing.id }}</div>
                        </td>
                            <!-- <th class="text-center" scope="row">{{ billing.id }}</th> -->
                            <td @click.prevent="show(billing)" class="text-center font-weight-bold"><a href="" style="text-decoration: none; font-weight: bold; "> {{ billing.billingNo }}
                                <span class="rounded mx-1 pl-1 pr-1 text-white bg-success" title="Total Bill Items">{{ billing.countBillingFees }}</span>
                                </a></td>
                            <td class="text-center"><span class="btn btn-lg btn-success m-1 py-0 px-1 w-15 font-weight-bold">{{ billing.fileNo }}/ {{ billing.fileYear }}</span></td>
                            <td>{{ billing.billingType }}</td>
                            <td style="width: 250px">{{ billing.subject }}</td>
                            <!-- <td>{{ billing.billingRef }}</td> -->
                            <td class="text-right">{{ formatPrice(billing.totalFees) }}</td>
                            <!-- <td>{{ billing.billedDisbursement }}</td> -->
                            <td class="text-right">{{ formatPrice(billing.totalDisbursements) }}</td>
                            <td class="text-right">{{ formatPrice(billing.totalDiscount) }}</td>
                            <td>{{ billing.created_at | formatDate }}</td>
                            <td>
                                <!-- <a href="" class="btn btn-sm btn-outline-secondary" @click.prevent="show(billing)">
                                    <i class="fas fa-search"></i>
                                </a> -->

                                <router-link
                                :to="{ name: 'billing.edit', params: { id: billing.id } }"
                                class="btn btn-sm btn-outline-info"><a><i class="fas fa-file mb-1"></i></a> Manage Items</router-link>
                                <!-- <router-link :to="{ name: 'billing.edit', params: { id: billing.id } }" class="btn btn-sm btn-outline-info"><a><i class="fas fa-file mb-1"></i></a> Edit</router-link> -->
                                <a href="" class="btn btn-sm btn-outline-secondary" @click.prevent="edit(billing)">
                                    <i class="fas fa-edit"></i>
                                </a>
                                <a href="" class="btn btn-sm btn-outline-danger" @click.prevent="remove(billing)">
                                    <i class="fas fa-trash"></i>
                                </a>
                            </td>
                        </tr>
                    </tbody>
                </table>
                <div class="card-footer pb-0 pt-3" style="max-width: 100% !important;">
                    <nav>
                        <ul class="pagination justify-content-center flex-lg-wrap">
                            <li class="page-item" :class="{ disabled: isFirstPage }"><a class="page-link" href="#" @click.prevent="prev">Previous</a></li>
                            <li class="page-item" :class="currentPage === page" v-for="(page, index) in pages" :key="index">
                                <a class="page-link" href="#" @click.prevent="switchPage(page)">{{ page }}</a></li>
                            <li class="page-item" :class="{ disabled: isLastPage }"><a class="page-link" href="#" @click.prevent="next">Next</a></li>
                        </ul>
                    </nav>
                </div>
            </div>
        </div>

        <!-- Modal -->
        <div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true" ref="vuemodal">
            <div class="modal-dialog modal-xl" role="document">
                <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLabel">{{ modalTitle }}</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">
                    <form>
                        <div class="row">
                            <div class="col-sm-6">
                                <!-- <div class="form-group">
                                    <label>File No.</label>
                                    <select type="text" v-model="billing.fileId"
                                    class="form-control" :class="{ 'is-invalid': errors.fileId }">
                                    <option disabled value="">Please select one</option>
                                    <option v-for="(file, index) in files" :key="index" :value="file.id">{{ file.fileNo }}</option>
                                    </select>
                                    <span class="invalid-feedback" v-if="errors.fileId">{{ errors.fileId[0] }}</span>
                                </div> -->
                                <div class="form-group">
                                    <label for="fileId">File No *</label>
                                    <multiselect
                                        v-model="billing.fileId" :class="{ 'is-invalid': errors.fileId }"
                                        :options="files.map(file => file.id)"
                                        :custom-label="opt => files.find(file => file.id == opt).fileNo"

                                        open-direction="bottom"
                                        :allow-empty="false"
                                        :close-on-select="true"
                                        placeholder="Please select one"
                                    >
                                    </multiselect>
                                    <span class="invalid-feedback" v-if="errors.fileId">{{ errors.fileId[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label for="clientId">Client *</label>
                                    <multiselect
                                        v-model="billing.clientId" :class="{ 'is-invalid': errors.clientId }"
                                        :options="clients.map(client => client.id)"
                                        :custom-label="opt => clients.find(client => client.id == opt).clientName"

                                        open-direction="bottom"
                                        :allow-empty="false"
                                        :close-on-select="true"
                                        placeholder="Please select one"
                                    >
                                    </multiselect>
                                    <span class="invalid-feedback" v-if="errors.clientId">{{ errors.clientId[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label for="billingType">Type</label>
                                    <select class="form-control" v-model="billing.billingType" :class="{ 'is-invalid': errors.billingType }">
                                        <option value="" disable hidden>Please select one</option>
                                        <option value=1>1</option>
                                        <option value=2>2</option>
                                        <option value=3>3</option>
                                        <span class="invalid-feedback" v-if="errors.billingType">{{ errors.billingType[0] }}</span>
                                    </select>
                                </div>
                                <!-- <div class="form-group">
                                    <label>Type</label>
                                    <input type="text" v-model="billing.billingType"
                                    class="form-control" :class="{ 'is-invalid': errors.billingType }">
                                    <span class="invalid-feedback" v-if="errors.billingType">{{ errors.billingType[0] }}</span>
                                </div> -->
                                <div class="form-group">
                                    <label>Billing No.</label>
                                    <input type="text" v-model="billing.billingNo" placeholder="Minimum 1 Numbers"
                                    class="form-control" :class="{ 'is-invalid': errors.billingNo }">
                                    <span class="invalid-feedback" v-if="errors.billingNo">{{ errors.billingNo[0] }}</span>
                                </div>
                            </div>
                            <div class="col-sm-6">
                                <div class="form-group">
                                    <label>Subject</label>
                                    <input type="text" v-model="billing.subject" placeholder="Address, Minimum 5 Characters"
                                    class="form-control" :class="{ 'is-invalid': errors.subject }">
                                    <span class="invalid-feedback" v-if="errors.subject">{{ errors.subject[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label>Your Ref. No</label>
                                    <input type="text" v-model="billing.yourRef" placeholder="Minimum 2 Numbers"
                                    class="form-control" :class="{ 'is-invalid': errors.yourRef }">
                                    <span class="invalid-feedback" v-if="errors.yourRef">{{ errors.yourRef[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label>Our Ref. No</label>
                                    <input type="text" v-model="billing.ourRef" placeholder="Minimum 2 Numbers"
                                    class="form-control" :class="{ 'is-invalid': errors.ourRef }">
                                    <span class="invalid-feedback" v-if="errors.ourRef">{{ errors.ourRef[0] }}</span>
                                </div>
                                <!-- <div class="form-group">
                                    <label>Total Fees(RM)</label>
                                    <vue-numeric currency="RM" placeholder="RM 0.00" :precision="2" separator=","
                                        v-model="billing.totalFees" class="form-control" :class="{ 'is-invalid': errors.totalFees }"></vue-numeric>
                                    <span class="invalid-feedback" v-if="errors.totalFees">{{ errors.totalFees[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label>Disbursement(RM)</label>
                                    <vue-numeric currency="RM" placeholder="RM 0.00" :precision="2" separator=","
                                        v-model="billing.totalDisbursements" class="form-control" :class="{ 'is-invalid': errors.totalDisbursements }"></vue-numeric>
                                    <span class="invalid-feedback" v-if="errors.totalDisbursements">{{ errors.totalDisbursements[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label>Discount(RM)</label>
                                    <vue-numeric currency="RM" placeholder=" RM 0.00" :precision="2" separator=","
                                        v-model="billing.totalDiscount" class="form-control" :class="{ 'is-invalid': errors.totalDiscount }"></vue-numeric>
                                    <span class="invalid-feedback" v-if="errors.totalDiscount">{{ errors.totalDiscount[0] }}</span>
                                </div> -->
                            </div>
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                    <button type="button" class="btn btn-primary" @click.prevent="saveOrUpdate">{{ modalTextButton }}</button>
                </div>
                </div>
            </div>
        </div>

        <!-- Modal 2 -->
        <div class="modal fade" id="exampleModal2" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel2" aria-hidden="true" ref="vuemodal2">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content modal-opened">
                <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLabel2">{{ billing.id }} - {{ billing.billingNo }}</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">

                        <div class="table-responsive-lg">
                            <table class="table table-hover table-light">
                                <tbody>
                                    <tr>
                                        <th class="text-right" scope="row">File No:</th>
                                        <td>{{ billing.fileNo }}/{{ billing.fileYear }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Client Name:</th>
                                        <td>{{ billing.client }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">File Type:</th>
                                        <td>{{ billing.billingType }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Billing No.:</th>
                                        <td>{{ billing.billingNo }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Subject:</th>
                                        <td>{{ billing.subject }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Your Ref. No.:</th>
                                        <td >{{ billing.yourRef }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Our Ref. No.:</th>
                                        <td >{{ billing.ourRef }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Total Fee:</th>
                                        <td>{{ formatPrice(billing.totalFees) }}</td>
                                    </tr>
                                    <!-- <tr>
                                        <th scope="row">Disbursement:</th>
                                        <td>{{ billing.billedDisbursement }}</td>
                                    </tr> -->
                                    <tr>
                                        <th class="text-right" scope="row">Disbursement:</th>
                                        <td>{{ formatPrice(billing.totalDisbursements) }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Total Discount:</th>
                                        <!-- <td>{{ formatPrice(billing.totalDiscount).replace(/MYR/g, "RM") }}</td> -->
                                        <td>{{ formatPrice(billing.totalDiscount) }}</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                <div class="modal-footer">
                    <router-link data-dismiss="modal"
                            :to="{ name: 'billing.edit', params: { id: billing.id } }"
                            class="btn btn-sm btn-warning"><a><i class="fas fa-file mb-1"></i></a> Manage Items</router-link>
                    <button type="button" class="btn btn-secondary mr-2" data-dismiss="modal" @click.prevent="edit(billing)">Edit</button>
                    <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                </div>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
import Multiselect from 'vue-multiselect';

export default {

    components: {Multiselect},

    data: function () {
        return {
            billings: [],
            files: [],
            clients: [],

            filters: {
                subject: '',
                billingRef: '',
                created_at: ''
            },

            order: {
                dir: 1,
                column: 'id'
            },

            currentPage: 1,

            billing: {
                id: null,
                fileId: '',
                clientId: '',
                // contactId: '',
                fileNo: '',
                fileYear: '',
                client: '',
                // contact: '',
                billingNo: '',
                billingType: '',
                subject: '',
                yourRef: '',
                ourRef: '',
                footerText: '',
                totalFees: '',
                billedDisbursement: '',
                totalDiscount: '',
                created_at: '',
                // totalDiscount: ''
            },

            isEdit: false,

            isOpen: false,

            errors: {},

            removeBillingId: null,

            search: '',
            search2: '',
            selectedJobTitle: null,

            selectedBillingTotal: 50000,
            currentOptionBillingTotal: 'all',

            selectedBillingIsParty: '',
            currentOptionBillingIsParty: 'all',
        }
    },

    mounted() {
        this.loadAll();
    },

    computed: {
        billingsPaginated () {
            let start = (this.currentPage - 1) * this.perPage
            let end = this.currentPage  * this.perPage
            return this.billingsSorted.slice(start, end)
        },

        billingsSorted () {
        return this.filterBothBillings.sort((a, b) => {
            let left = a[this.order.column] , right = b[this.order.column] ;

            if (isNaN(left) && isNaN(right)) {
               if (left < right) return -1 * this.order.dir;
               else if (left > right) return 1 * this.order.dir;
                else return 0;
            } else {
               return (left - right) * this.order.dir
                }
            });
        },

        sortType () {
            return this.order.dir === 1 ? 'ascending' : 'descending'
        },

        whenSearching () {
            return this.search.length > 0 || this.search2.length > 0;
        },

        filterBothBillings: function(){
                return this.filteredTwo(this.filteredOne(this.billings))
        },

        isFirstPage () {
            return this.currentPage === 1;
        },

        isLastPage () {
            return this.currentPage >= this.pages;
        },

        pages () {
            return Math.ceil(this.filterBothBillings.length / this.perPage);
        },

        perPage () {
            if (this.filterBothBillings.length >= 2000 ) {
                return 50
            }
            else if (this.filterBothBillings.length >= 1000 ) {
                return 30
            }
            else {
                return 15
            }
        },

        modalTitle () {
            return this.isEdit ? "Update Billing" : "Add Billing"
        },

        modalTextButton () {
            return this.isEdit ? "Update" : "Save"
        }

    },

    methods: {

        add () {
            this.isEdit = false;

            this.billing = {
                id: null,
                fileId: '',
                clientId: '',
                billingNo: '',
                billingType: '',
                subject: '',
                yourRef: '',
                ourRef: '',
                footerText: '',
                totalFees: '',
                billedDisbursement: '',
                totalDiscount: '',
                created_at: '',
                }

            $(this.$refs.vuemodal).modal('show');
        },

        edit (billing) {
            this.billing = Object.assign({}, billing);

            this.isEdit = true;

            $(this.$refs.vuemodal).modal('show');

        },

        show (billing) {
            this.billing = Object.assign({}, billing);

            this.isOpen = true;

            $(this.$refs.vuemodal2).modal('show');

        },

        saveOrUpdate () {
            if (this.isEdit) {
                this.update();
            } else {
                this.save();
            }
        },

        update () {
            axios.put('/api/billings/' + this.billing.id, this.billing)
                .then(({ data }) => {

                    let index = this.billings.findIndex(item => item.id === this.billing.id);

                    this.billings.splice(index, 1, data.data);

                    this.isEdit = false;

                    this.errors = {};

                    $(this.$refs.vuemodal).modal('hide');

                    this.$toast.success("Record Updated Successfully.");
                })
                .catch(({ response }) => {
                    this.errors = response.data.errors
                })
        },

        save () {
            axios.post('/api/billings', this.billing)
                .then(({ data }) =>{
                    this.billings.unshift(data.data)

                    this.billing = {
                        id: null,
                        fileId: '',
                        clientId: '',
                        billingNo: '',
                        billingType: '',
                        subject: '',
                        yourRef: '',
                        ourRef: '',
                        footerText: '',
                        totalFees: '',
                        billedDisbursement: '',
                        totalDiscount: '',
                        created_at: '',
                    }

                    this.errors = {};

                    $(this.$refs.vuemodal).modal('hide');

                    this.$toast.success("Record Updated Successfully.");
                })
                .catch(({ response }) => {
                    this.errors = response.data.errors
                })

        },

        remove (billing) {
            if (confirm("Are you sure?")) {
                axios.delete('/api/billings/' + billing.id)
                    .then(res => {
                        this.removeBillingId = billing.id

                        new Promise(resolve => setTimeout(resolve, 1000))
                            .then(() => {
                                this.removedBillingId = null
                                let index = this.billings.findIndex(item => item.id === billing.id);
                                this.billings.splice(index, 1)
                                })
                            })
                    }
        },

        loadAll: function () {

        const loadAll = async () => {
            try{
                axios.get('/api/fileless')
                    .then(({data}) => {
                        this.files = data.data;
                    }),

                axios.get('/api/clientless')
                    .then(({data}) => {
                        this.clients = data.data;
                    })

                axios.get('/api/billings')
                    .then(({data}) => {
                        this.billings = data.data;
                    })

                } catch (err) {
                    console.log(err);
                }
        };
        loadAll();

        },

        switchPage (page) {
            this.currentPage = page;
        },

        prev () {
            if (!this.isFirstPage) {
                this.currentPage--;
            }
        },

        next () {
            if (!this.isLastPage) {
                this.currentPage++;
            }
        },

        classes (column) {
            return [
                'sort-control',
                column === this.order.column ? this.sortType : ''
            ]
        },

        sort (column) {
            this.order.column = column;
            this.order.dir *= -1;
        },

        clearText () {
            this.search = '';
            this.search2 = '';
        },

        filteredOne: function(billings) {

            const value = this.search.charAt(0).toLowerCase() + this.search.slice(1);
            return this.billings.filter((billing) => {
                let re = / /gi;
                // return billing.billingName.toString().indexOf(value.toString().toLowerCase()) > -1 ||
                return  billing.billingNo.toLowerCase().indexOf(value.toLowerCase().toLowerCase()) > -1 ||
                        billing.fileNo.toString().indexOf(value.toString().toLowerCase()) > -1;

                // return  billing.billingNo.toLowerCase().indexOf(value.toLowerCase().toLowerCase()) > -1 ||
                //         billing.subject.toLowerCase().indexOf(value.toLowerCase().toLowerCase()) > -1 ||
                //         billing.billingRef.toLowerCase().indexOf(value.toLowerCase()) > -1 ||
                //         billing.totalFees.toLowerCase().indexOf(value.toLowerCase()) > -1 ||
                //         billing.billedDisbursement.toLowerCase().indexOf(value.toLowerCase()) > -1 ||
                //         billing.totalDisbursements.toLowerCase().indexOf(value.toLowerCase()) > -1 ||
                //         billing.totalDiscount.toLowerCase().indexOf(value.toLowerCase()) > -1 ||
                //         billing.fileNo.toString().indexOf(value.toString().toLowerCase()) > -1 ||
                //         billing.created_at.toLowerCase().indexOf(value.toLowerCase().replace() ) > -1

            });

        },

        filteredTwo: function(billings) {

            const value2 = this.search2.charAt(0).toLowerCase() + this.search2.slice(1);
            return this.filteredOne().filter((billing) => {
                let re = / /gi;
                // return billing.billingName.toString().indexOf(value2.toString().toLowerCase()) > -1 ||
                return  billing.billingNo.toLowerCase().indexOf(value2.toLowerCase().toLowerCase()) > -1 ||
                        billing.fileNo.toString().indexOf(value2.toString().toLowerCase()) > -1;
                //         billing.subject.toLowerCase().indexOf(value2.toLowerCase().toLowerCase()) > -1 ||
                //         billing.billingRef.toLowerCase().indexOf(value2.toLowerCase()) > -1 ||
                //         billing.totalFees.toLowerCase().indexOf(value2.toLowerCase()) > -1 ||
                //         billing.billedDisbursement.toLowerCase().indexOf(value2.toLowerCase()) > -1 ||
                //         billing.totalDisbursements.toLowerCase().indexOf(value2.toLowerCase()) > -1 ||
                //         billing.totalDiscount.toLowerCase().indexOf(value2.toLowerCase()) > -1 ||
                //         billing.fileNo.toString().indexOf(value2.toString().toLowerCase()) > -1 ||
                //         billing.created_at.toLowerCase().indexOf(value2.toLowerCase().replace() ) > -1
            });

        },

        filterBillingTotal: function (event) {

            var val2 = event.target.value;

            switch(val2) {
                case '100':
                    return this.selectedBillingTotal = 100;
                case '500':
                    return this.selectedBillingTotal = 500;
                case 'all':
                    return this.selectedBillingTotal = 2000;
                default:
                    return this.selectedBillingTotal = 10000;
            }
        },

        formatPrice(value) {
            // var formatter = new Intl.NumberFormat('en-US', {
            var formatter = new Intl.NumberFormat('ms', {
                style: 'currency',
                currency: 'MYR',
                minimumFractionDigits: 2,
                currencyDisplay: "symbol",
            });
            return formatter.format(value);
        },
    }

}
</script>
<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>

<style>
    input[type=search] {
            display: inline-block;
            margin-right: 10px !important;
        }
        .fontsearch {
            position: relative;
        }
        .fontsearch i{
            position: absolute;
            left: 10px;
            top: 20px;
            color: #CCCCCC;
        }
</style>
