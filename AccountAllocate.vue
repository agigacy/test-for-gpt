<template>
    <div class="row">
        <div class="col-12">
            <div class="card pb-0 mb-0">
                <div class="input-group col-md-12">
                    <div class="col-md-2">
                        <h4 class="font-weight-bold text-uppercase text-align-center pt-1">
                            AccountAllocate
                        </h4>
                        <p>
                            Total Record(s): <b>{{ filterAccountAllocatesAdvanced.length }}</b>
                            <br />
                            Total Page(s): <b>{{ pages }}</b>
                        </p>
                    </div>
                    <div class="col-md-7 mb-1">
                        <div class="input-group-prepend d-flex">
                            <!-- <select v-model="perPage" @change="changePerPage">
                            <option v-for="option in perPageOptions" :key="option" v-bind:value="option">
                                {{ option }}
                            </option>
                            </select> -->
                            <div class="input-group-prepend d-flex mr-2 mobile-hidden">
                                <select @change="filterAccountAllocateType" v-model="currentOptionAccountAllocateType" class="px-4">
                                    <option value="all">Type: All</option>
                                    <option class="px-4" value="Bill">Bill</option>
                                    <option class="px-4" value="Collection">Collection</option>
                                </select>
                            </div>

                            <div class="fontsearch mr-1">
                                <input class="form-control p-4" type="search" v-model="search" placeholder=" Smart Search 1..." />
                                <i class="fa fa-search fa-md"></i>
                            </div>
                            <div class="fontsearch mr-1">
                                <input class="form-control p-4" type="search" v-model="search2" placeholder=" Smart Search 2..." />
                                <i class="fa fa-search fa-md"></i>
                            </div>
                            <div class="input-group-prepend d-flex">
                                <button v-show="whenSearching" v-on:click="clearText" type="button" class="btn btn-outline-secondary ml-1">
                                    <i class="fas fa-sync-alt"> Reset</i>
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="col-md-2 float-right mobile-hidden">
                        <div class="input-group-prepend">
                            <button class="btn btn-outline-secondary ml-1" @click.prevent="add">
                            <i class="fas fa-plus-circle"></i> QUICK ADD
                            </button>
                        </div>
                    </div>
                </div>
            </div>
            <div class="card">
                <spinner v-if="$root.loading"></spinner>
                <table class="table table-striped table-bordered" v-else-if="filterAccountAllocatesAdvanced.length">
                    <thead>
                    <tr style="text-align: center; font-size: 90%">
                        <th>No. </th>
                        <th scope="col" @click="sort('id')" :class="(classes('id'))">#Id</th>
                        <th scope="col" @click="sort('fileNo')" :class="(classes('fileNo'))">File No</th>
                        <th scope="col" @click="sort('billingNo')" :class="(classes('billingNo'))">Billing No</th>
                        <th scope="col" @click="sort('accountNumber')" :class="(classes('accountNumber'))">Account Number</th>
                        <th scope="col" @click="sort('allocateType')" :class="(classes('allocateType'))">Type</th>
                        <th scope="col" @click="sort('allocateAmount')" :class="(classes('allocateAmount'))">Allocate Amount</th>
                        <th scope="col" @click="sort('balanceAmount')" :class="(classes('balanceAmount'))">Balance Amount</th>
                        <th scope="col" @click="sort('deductAmount')" :class="(classes('deductAmount'))">Deduct Amount</th>
                        <th scope="col" @click="sort('isCommisson')" :class="(classes('isCommisson'))">Is Commisson</th>
                        <th scope="col" @click="sort('CommissonDate')" :class="(classes('CommissonDate'))">Commisson Date</th>
                        <th scope="col" @click="sort('created_at')" :class="(classes('created_at'))">Created Date</th>
                        <th scope="col">Action</th>
                    </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(accountallocate, index) in accountallocatesPaginated" :key="index"
                            :class="{ 'table-danger': removeAccountAllocateId == accountallocate.id }">
                            <td class="text-center" scope="row">{{ (currentPage * perPage + (index + 1))-perPage }} </td>
                            <td scope="row" class="mobile-hidden">
                                <div style="width: auto; height: auto; background-color: #00657D; border: 1px solid #CCC; text-align: center; opacity: 0.9; object-fit: contain;" class="rounded-pill text-white p-1 idStyle">
                                    {{ accountallocate.id }}
                                </div>
                            </td>
                            <td>{{ accountallocate.fileNo }}</td>
                            <td>{{ accountallocate.billingNo }}</td>
                            <td>{{ accountallocate.accountNumber }}</td>
                            <td>{{ accountallocate.allocateType }}</td>
                            <td>{{ accountallocate.allocateAmount }}</td>
                            <td>{{ accountallocate.balanceAmount }}</td>
                            <td>{{ accountallocate.deductAmount }}</td>
                            <td>{{ accountallocate.isCommisson }}</td>
                            <td>{{ accountallocate.CommissonDate }}</td>
                            <td class="text-center">{{ accountallocate.created_at | formatDate }}</td>
                            <td>
                                <a href="" class="btn btn-sm btn-outline-secondary mobile-hidden" @click.prevent="show(accountallocate)">
                                    <i class="fas fa-search"></i>
                                </a>

                                <a href="" class="btn btn-sm btn-outline-secondary mobile-hidden" @click.prevent="edit(accountallocate)">
                                    <i class="fas fa-edit"></i>
                                </a>
                                <a href="" class="btn btn-sm btn-outline-danger mobile-hidden" @click.prevent="remove(accountallocate)">
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
                            <!-- <button v-if="pagination.prev" @click="fetchPage(pagination.prev)">
                                Previous
                            </button>
                            <button v-for="page in totalPages" :key="page" @click="fetchPage(getPageUrl(page))">
                                {{ page }}
                            </button>
                            <button v-if="pagination.next" @click="fetchPage(pagination.next)">
                                Next
                            </button> -->
                            <!-- <button v-if="pagination.prev" @click="fetchPage(pagination.prev)">
                                Previous
                            </button>
                            <button v-for="page in visiblePages" :key="page" @click="fetchPage(getPageUrl(page))">
                                {{ page }}
                            </button>
                            <button v-if="pagination.next" @click="fetchPage(pagination.next)">
                                Next
                            </button> -->
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
                                <div class="form-group">
                                    <label for="fileId">File</label>
                                    <multiselect
                                        v-model="accountallocate.fileId" :class="{ 'is-invalid': errors.fileId }"
                                        :options="files.map(file => file.id)"
                                        :custom-label="opt => files.find(file => file.id == opt).fileNo"

                                        open-direction="bottom"
                                        :allow-empty="false"
                                        :close-on-select="true"
                                        :multiple="false"
                                    >
                                    </multiselect>
                                    <span class="invalid-feedback" v-if="errors.fileId">{{ errors.fileId[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label for="billingId">Billing</label>
                                    <multiselect
                                        v-model="accountallocate.billingId" :class="{ 'is-invalid': errors.billingId }"
                                        :options="billings.map(billing => billing.id)"
                                        :custom-label="opt => billings.find(billing => billing.id == opt).billingNo"

                                        open-direction="bottom"
                                        :allow-empty="false"
                                        :close-on-select="true"
                                        :multiple="false"
                                    >
                                    </multiselect>
                                    <span class="invalid-feedback" v-if="errors.billingId">{{ errors.billingId[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label>Account Number *</label>
                                    <input type="text" v-model="accountallocate.accountNumber"
                                    class="form-control" :class="{ 'is-invalid': errors.accountNumber }" placeholder="Minimum 8 numbers">
                                    <span class="invalid-feedback" v-if="errors.accountNumber">{{ errors.accountNumber[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label>AccountAllocate Type *</label>
                                    <select class="form-control" v-model="accountallocate.allocateType" :class="{ 'is-invalid': errors.allocateType }">
                                        <option value="" disable hidden>Please select one</option>
                                        <option value=Collection>Collection</option>
                                        <option value=Bill>Bill</option>
                                        <span class="invalid-feedback" v-if="errors.allocateType">{{ errors.allocateType[0] }}</span>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <label>AccountAllocate Allocate Amount *</label>
                                    <input type="text" v-model="accountallocate.allocateAmount"
                                    class="form-control" :class="{ 'is-invalid': errors.allocateAmount }" placeholder="Minimum 2 character">
                                    <span class="invalid-feedback" v-if="errors.allocateAmount">{{ errors.allocateAmount[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label>Balance Amount *</label>
                                    <input type="text" v-model="accountallocate.balanceAmount"
                                    class="form-control" :class="{ 'is-invalid': errors.balanceAmount }" placeholder="Minimum 2 character">
                                    <span class="invalid-feedback" v-if="errors.balanceAmount">{{ errors.balanceAmount[0] }}</span>
                                </div>
                            </div>
                            <div class="col-sm-6">
                                <div class="form-group">
                                    <label>Deduct Amount</label>
                                    <input type="text" v-model="accountallocate.deductAmount"
                                    class="form-control" :class="{ 'is-invalid': errors.deductAmount }" placeholder="Minimum 1 numbers">
                                    <span class="invalid-feedback" v-if="errors.deductAmount">{{ errors.deductAmount[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label>Is Commisson</label>
                                    <input type="text" v-model="accountallocate.isCommisson"
                                    class="form-control" :class="{ 'is-invalid': errors.isCommisson }" placeholder="Minimum 1 numbers">
                                    <span class="invalid-feedback" v-if="errors.isCommisson">{{ errors.isCommisson[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label>Commisson Date</label>
                                    <input type="text" v-model="accountallocate.CommissonDate"
                                    class="form-control" :class="{ 'is-invalid': errors.CommissonDate }" placeholder="Minimum 1 numbers">
                                    <span class="invalid-feedback" v-if="errors.CommissonDate">{{ errors.CommissonDate[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label>Balance</label>
                                    <input type="text" v-model="accountallocate.balance"
                                    class="form-control" :class="{ 'is-invalid': errors.balance }" placeholder="Minimum 1 numbers">
                                    <span class="invalid-feedback" v-if="errors.balance">{{ errors.balance[0] }}</span>
                                </div>
                                <div class="form-group">
                                    <label>Paid</label>
                                    <input type="text" v-model="accountallocate.paid"
                                    class="form-control" :class="{ 'is-invalid': errors.paid }" placeholder="Minimum 1 numbers">
                                    <span class="invalid-feedback" v-if="errors.paid">{{ errors.paid[0] }}</span>
                                </div>
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

        <!-- Modal 2 Quick view -->
        <div class="modal fade" id="exampleModal2" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel2" aria-hidden="true" ref="vuemodal2">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content modal-opened">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel2">{{ accountallocate.id }} - {{ accountallocate.accountNumber }}</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <div class="table-responsive-lg">
                            <table class="table table-hover table-light">
                                <tbody>
                                    <tr>
                                        <th class="text-right" scope="row">Account Number:</th>
                                        <td>{{ accountallocate.accountNumber }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Balance Amount:</th>
                                        <td>{{ accountallocate.balanceAmount }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Allocate Amount:</th>
                                        <td>{{ accountallocate.allocateAmount }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">AccountAllocate Type:</th>
                                        <td>{{ accountallocate.allocateType }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Deduct Amount:</th>
                                        <td>{{ accountallocate.deductAmount }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Is Commisson:</th>
                                        <td>{{ accountallocate.isCommisson }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Commisson Date No.:</th>
                                        <td>{{ accountallocate.CommissonDate }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Fax No.:</th>
                                        <td>{{ accountallocate.faxNo }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Balance Address:</th>
                                        <td>{{ accountallocate.balance }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">AccountAllocate Address:</th>
                                        <td>{{ accountallocate.accountallocateAddress }}</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                    <div class="modal-footer">
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

    components: {
        Multiselect,
    },

    data: function () {
        return {
            accountallocates: [],

            // pagination: {},
            // totalRecords: 0,
            // totalPages: 0,
            // visiblePages: [],
            // perPage: 10,
            // perPageOptions: [10, 25, 50, 100],

            files: [],

            billings: [],

            filters: {
                billingNo: '',
                fileNo: '',
                accountNumber: '',
                allocateAmount: '',
                balanceAmount: '',
            },

            order: {
                dir: 1,
                column: 'id'
            },

            currentPage: 1,

            accountallocate: {
                id: null,
                fileId: '',
                fileNo: '',
                fileYear: '',
                billingId: '',
                billingNo: '',
                accountId: '',
                accountNumber: '',
                allocateType: '',
                allocateAmount: '',
                balanceAmount: '',
                deductAmount: '',
                isCommisson: '',
                assignCommissonBy: '',
                CommissonDate: '',
                created_at: '',
                createdBy: '',
                updatedBy: '',
            },

            isEdit: false,

            isOpen: false,

            errors: {},

            removeAccountAllocateId: null,

            search: '',
            search2: '',

            blog: '',

            sortAccountAllocateType: '',

            selectedAccountAllocateType: '',
            selectedAccountAllocateTotal: 50000,

            currentOptionAccountAllocateType: 'all',
            currentOptionAccountAllocateTotal: 'all',

            user: '',

            natureCode: '',

            roles: [],

            editor: null,
            showFullId: false
        }
    },

    mounted() {
        this.loadAccountAllocatesContacts();
        axios.get('/api/user').then( (response) => {
                this.user = response;
                console.log(response.data.isAdmin);
            });

        console.log(this.$userId + this.$userRole);
    },

    computed: {
        accountallocatesPaginated () {
            let start = (this.currentPage - 1) * this.perPage
            let end = this.currentPage  * this.perPage
            return this.accountallocatesSorted.slice(start, end)
        },

        accountallocatesSorted () {
        return this.filterAccountAllocatesAdvanced.sort((a, b) => {
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

        isFirstPage () {
            return this.currentPage === 1;
        },

        isLastPage () {
            return this.currentPage >= this.pages;
        },

        pages () {
            return Math.ceil(this.filterAccountAllocatesAdvanced.length / this.perPage);
        },

        perPage () {
            if (this.filterAccountAllocatesAdvanced.length >= 2000 ) {
                return 50
            }
            else if (this.filterAccountAllocatesAdvanced.length >= 1000 ) {
                return 30
            }
            else {
                return 15
            }
        },

        modalTitle () {
            return this.isEdit ? "Update AccountAllocate" : "Add AccountAllocate"
        },

        modalTextButton () {
            return this.isEdit ? "Update" : "Save"
        },

        filterAccountAllocatesAdvanced: function() {

                return this.currentOptionAccountAllocateType === 'all'
                ? this.filteredTwo(this.filteredOne(this.accountallocates)).splice(0, this.selectedAccountAllocateTotal)
                : this.filteredTwo(this.filteredOne(this.accountallocates)).filter(item => item.allocateType == this.selectedAccountAllocateType).splice(0, this.selectedAccountAllocateTotal);

        },

        outputAccountAllocateAddress() {
            return this.accountallocate.accountallocateAddress.replace(',', ', \n');
        },

        // ...mapGetters(['getActiveTimerString']),
        // activeTimerString() {
        //     return this.getActiveTimerString
        // },

        // ...mapGetters(['getLastRecordedTime']),
        // astRecordedTime() {
        //     return this.getLastRecordedTime
        // },
        // ...mapState({
        //     activeTimerString: state => state.timer.activeTimerString,
        //     lastRecordedTime: state => state.timer.lastRecordedTime
        // })


    },

    // created() {
    //     this.fetchPage(`http://localhost:8000/api/accountallocates?page=1&per_page=${this.perPage}`);
    //     this.totalPages = 300; // 替换为你的总页数
    //     this.calculateVisiblePages();
    // },

    methods: {

        add () {
            this.isEdit = false;

            this.accountallocate = {
                id: null,
                fileId: '',
                billingId: '',
                accountId: '',
                accountNumber: '',
                allocateType: '',
                allocateAmount: '',
                balanceAmount: '',
                deductAmount: '',
                isCommisson: '',
                assignCommissonBy: '',
                CommissonDate: '',
                }

            $(this.$refs.vuemodal).modal('show');
        },

        edit (accountallocate) {

            this.accountallocate = Object.assign({}, accountallocate);

            this.isEdit = true;

            $(this.$refs.vuemodal).modal('show');

        },

        show (accountallocate) {
            this.accountallocate = Object.assign({}, accountallocate);

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
            axios.put('/api/accountallocates/' + this.accountallocate.id, this.accountallocate)
                .then(({ data }) => {

                    let index = this.accountallocates.findIndex(item => item.id === this.accountallocate.id);

                    this.accountallocates.splice(index, 1, data.data);

                    this.isEdit = false;

                    this.errors = {};

                    $(this.$refs.vuemodal).modal('hide');

                    this.$toast.success("Record Created Successfully.")
                })
                .catch(({ response }) => {
                    this.errors = response.data.errors
                })
        },

        save () {
            axios.post('/api/accountallocates', this.accountallocate)
                .then(({ data }) =>{
                    this.accountallocates.unshift(data.data)

                    this.accountallocate = {
                    id: null,
                    fileId: '',
                    billingId: '',
                    accountId: '',
                    accountNumber: '',
                    allocateType: '',
                    allocateAmount: '',
                    balanceAmount: '',
                    deductAmount: '',
                    isCommisson: '',
                    assignCommissonBy: '',
                    CommissonDate: '',
                    }

                    this.errors = {};

                    $(this.$refs.vuemodal).modal('hide');

                    this.$toast.success("Record Created Successfully.")
                })
                .catch(({ response }) => {
                    this.errors = response.data.errors
                })

        },

        remove (accountallocate) {
            if (confirm("Are you sure?")) {
                axios.delete('/api/accountallocates/' + accountallocate.id)
                    .then(res => {
                        this.removeAccountAllocateId = accountallocate.id

                        new Promise(resolve => setTimeout(resolve, 1000))
                            .then(() => {
                                this.removedAccountAllocateId = null
                                let index = this.accountallocates.findIndex(item => item.id === accountallocate.id);
                                this.accountallocates.splice(index, 1)
                                })
                            })

                    }
        },

        loadAccountAllocatesContacts: function () {

            const loadAccountAllocatesContacts = async () => {
                try{
                        axios.get('/api/accountallocates')
                            .then(({data}) => {
                                this.accountallocates = data.data;
                            }),
                        axios.get('/api/files')
                            .then(({data}) => {
                                this.files = data.data;
                            }),
                        axios.get('/api/billings')
                            .then(({data}) => {
                                this.billings = data.data;
                            })
                } catch (err) {
                    console.log(err);
                }
        };
        loadAccountAllocatesContacts();

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
            this.selectedAccountAllocateType = 1;
            this.selectedAccountAllocateTotal = 20000;
        },

        filteredOne: function(accountallocates) {

            const value = this.search.charAt(0).toLowerCase() + this.search.slice(1);
            return this.accountallocates.filter((accountallocate) => {
                let re = / /gi;
                return accountallocate.billingNo.toString().indexOf(value.toString().toLowerCase()) > -1 ||
                        accountallocate.fileNo.toString().indexOf(value.toString().toLowerCase()) > -1 ||
                        accountallocate.accountNumber.toString().indexOf(value.toString().toLowerCase()) > -1 ||
                        accountallocate.allocateType.toLowerCase().indexOf(value.toLowerCase()) > -1 ||
                        accountallocate.allocateAmount.toLowerCase().indexOf(value.toLowerCase()) > -1 ||
                        accountallocate.balanceAmount.toLowerCase().indexOf(value.toLowerCase()) > -1;

            });

        },

        filteredTwo: function(accountallocates) {

            const value2 = this.search2.charAt(0).toLowerCase() + this.search2.slice(1);
            return this.filteredOne().filter((accountallocate) => {
                let re = / /gi;
                return accountallocate.billingNo.toString().indexOf(value2.toString().toLowerCase()) > -1 ||
                        accountallocate.fileNo.toString().indexOf(value2.toString().toLowerCase()) > -1 ||
                        accountallocate.accountNumber.toString().indexOf(value2.toString().toLowerCase()) > -1 ||
                        accountallocate.allocateType.toLowerCase().indexOf(value2.toLowerCase()) > -1 ||
                        accountallocate.allocateAmount.toLowerCase().indexOf(value2.toLowerCase()) > -1 ||
                        accountallocate.balanceAmount.toLowerCase().indexOf(value2.toLowerCase()) > -1;

            });

        },

        filterAccountAllocateType: function (evt) {

            var val = evt.target.value;

            switch(val) {
                case 'Bill':
                    return this.selectedAccountAllocateType = 'Bill';
                    // code block
                    break;
                case 'Collection':
                    return this.selectedAccountAllocateType = 'Collection';
                    // code block
                    break;
                case 'all':
                    return this.selectedAccountAllocateType != 2;
                    // code block
                    break;
                default:
                    return this.selectedAccountAllocateType = 2; // flatten([1], [2], [0]);
            }

        },

        filterAccountAllocateTotal: function (event) {

            var val2 = event.target.value;

            switch(val2) {
                case '100':
                    return this.selectedAccountAllocateTotal = 100;
                    // code block
                    break;
                case '500':
                    return this.selectedAccountAllocateTotal = 500;
                    // code block
                    break;
                case 'all':
                    return this.selectedAccountAllocateTotal = 2000;
                    // code block
                    break;
                default:
                    return this.selectedAccountAllocateTotal = 10000;
            }

        },

        // fetchPage(url) {
        //     axios.get(url)
        //         .then(response => {
        //             this.accountallocates = response.data.data;
        //             this.pagination = response.data.links;
        //             this.totalRecords = response.data.meta.total;
        //             this.totalPages = response.data.meta.last_page;
        //             this.calculateVisiblePages();
        //         })
        //         .catch(error => {
        //         console.error(error);
        //         });
        // },
        // getPageUrl(page) {
        //     return `http://localhost:8000/api/accountallocates?page=${page}&per_page=${this.perPage}`;
        // },
        // calculateVisiblePages() {
        // const currentPage = this.pagination.current_page;
        // const totalPages = this.totalPages;
        // const range = 2; // 附近可见页码的范围，根据需要进行调整

        // let start = Math.max(currentPage - range, 1);
        // let end = Math.min(currentPage + range, totalPages);

        // if (start === 1) {
        //     end = Math.min(end + range - start + 1, totalPages);
        // } else if (end === totalPages) {
        //     start = Math.max(start - (range - (totalPages - end)) - 1, 1);
        // }

        // // 仅显示当前页附近的页码
        // this.visiblePages = Array.from({ length: end - start + 1 }, (_, i) => start + i);
        // },
        // changePerPage() {
        // // 每页记录数改变时的操作
        // this.fetchPage(`http://localhost:8000/api/accountallocates?page=1&per_page=${this.perPage}`);
        // },
    }

}
</script>
