<template>
    <div class="row m-0 p-1">
        <div class="col-12 m-0 p-1">
            <div class="card m-0 p-1">
                <div class="input-group col-md-12 m-0 p-1">
                    <div class="col-md-2">
                        <h4 class="font-weight-bold text-uppercase text-align-center pt-1">
                            Billing(s) (ID:  {{ `${this.$route.params.id}` }})
                        </h4>
                        <p>
                            Total Record(s): <b>{{ filteredBillingFees.length }}</b>
                        </p>
                    </div>
                    <div class="col-md-8">
                        <div class="input-group-prepend d-flex">
                            <input class="form-control" type="text" v-model="search" placeholder="Smart Search ..." />
                            <div class="input-group-prepend">
                                <button v-show="whenSearching" v-on:click="clearText" type="button" class="btn btn-outline-secondary ml-1">
                                    <i class="fas fa-sync-alt"> Reset</i>
                                </button>
                                <button class="btn btn-primary btn-lg ml-2" @click.prevent="add">
                                    <i class="fas fa-plus-circle"></i> Add New
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="card m-0 p-1">
                <div class="col-md-12 row my-1">
                    <div class="col-md-6">
                        <a href="" class="btn btn-sm btn-outline-primary my-1 pull-left w-100 py-2" @click.prevent="saveAll">
                            <i class="fas fa-save"></i> Save All
                        </a>
                    </div>
                    <div class="col-md-6">
                        <router-link class="btn btn-lg btn-outline-info mb-0 pull-right mr-2 py-2" style="width: 300px" :to="{ name: 'billing'}">
                            <a><i class="fas fa-arrow-left"></i> Back to List </a>
                        </router-link>
                    </div>
                </div>
                <spinner v-if="$root.loading"></spinner>
                <table class="table table-striped table-bordered" v-else-if="billingfees.length">
                    <thead>
                    <tr style="text-align: center; font-size: 90%">
                        <th scope="col" @click="sort('no')" :class="(classes('no'))">No.</th>
                        <th scope="col" @click="sort('feeName')" :class="(classes('feeName'))">Fee Name</th>
                        <!-- <th scope="col" @click="sort('feeName')" :class="(classes('feeName'))">Fee Name RTE</th> -->
                        <th scope="col" @click="sort('feeAmount')" :class="(classes('feeAmount'))">Fee Amount</th>
                        <th scope="col" @click="sort('feeTax')" :class="(classes('feeTax'))">Fee Tax</th>
                        <th scope="col" @click="sort('feeTotal')" :class="(classes('feeTotal'))">Fee Total</th>
                        <th scope="col">Action</th>
                        <th scope="col">Drag</th>
                    </tr>
                    </thead>
                    <!-- <tbody> -->
                    <transition-group
                        :options="{handle: '.drag-handle'}" @change="onDragComplete"
                        is="draggable"
                        tag="tbody"
                        v-model="billingfeesPaginated"
                        :name="!drag ? 'flip-list' : null"
                        @start="drag = true"
                        @end="drag = false"
                        animation="200"
                    >
                    <!-- <transition-group
                        :options="{handle: '.drag-handle'}" @change="onDragComplete"
                        is="draggable"
                        tag="tbody"
                        v-model="billingfeesPaginated"
                        :name="!drag ? 'flip-list' : null"
                        @start="drag = true"
                        @end="drag = false"
                        animation="200"
                    > -->
                        <tr v-for="(billingfee, index) in billingfeesPaginated" :key="billingfee.id" :class="{ 'table-danger': removeBillingFeeId == billingfee.id }">
                            <td class="text-center" scope="row">{{ (currentPage * perPage + (index + 1))-perPage }} </td>
                            <td contenteditable v-if="isHTML(billingfee.feeName) == false"
                                    @blur="handleCellEdit($event, billingfee, 'feeName')"
                                    v-html="billingfee.feeName"
                                    ref="editor">
                            </td>
                            <td contenteditable v-else @click.prevent="edit(billingfee)"
                                    @blur="handleCellEdit($event, billingfee, 'feeName')"
                                    v-html="billingfee.feeName"
                                    ref="editor">
                            </td>

                            <!-- Temporary keep -->
                            <!-- <td v-if="isHTML(billingfee.feeName) == false"><div contenteditable
                                    @blur="handleCellEdit($event, billingfee, 'feeName')"
                                    v-html="billingfee.feeName"
                                    ref="editor" class="py-2 px-1">
                                </div>
                            </td>
                            <td v-else><div contenteditable @click.prevent="edit(billingfee)"
                                    @blur="handleCellEdit($event, billingfee, 'feeName')"
                                    v-html="billingfee.feeName"
                                    ref="editor" class="py-2 px-1">
                                </div>
                                <div style="width: 100px; min-width: 50px; height: auto; background-color: #757575; border: 1px solid #CCC; text-align: center; opacity: 0.9"
                                class="rounded-pill text-white p-1 idStyle mt-1 ml-1 align-content-center"><i class="ri-html5-fill fa-lg text-light"> Formatted</i></div>
                            </td> -->

                            <!-- <td @focus="editor.commands.setContent(billingfee.feeName)" contenteditable @blur="handleCellEdit($event, billingfee, 'feeName')" v-html="billingfee.feeName"></td> -->
                            <!-- <td class="text-right pr-3 font-weight-bold" contenteditable @blur="handleCellEdit($event, billingfee, 'feeAmount')">{{ billingfee.feeAmount | toCurrency}}</td> -->
                            <td class="text-right pr-3 font-weight-bold" contenteditable @blur="handleCellEdit($event, billingfee, 'feeAmount')">{{ billingfee.feeAmount | toCurrency}}</td>
                            <td class="text-right pr-3" contenteditable @blur="handleCellEdit($event, billingfee, 'feeTax')">{{ billingfee.feeTax }}</td>
                            <td class="text-right pr-3 font-weight-bold text-md" contenteditable @blur="handleCellEdit($event, billingfee, 'feeTotal')">{{ billingfee.feeTotal }}</td>
                            <!-- <td class="d-flex"> -->
                            <td class="space-evenly">

                                <a href="" class="btn btn-sm btn-outline-secondary ml-1" @click.prevent="show(billingfee)">
                                    <i class="fas fa-search"></i>
                                </a>
                                <a href="" class="btn btn-sm btn-outline-secondary ml-1" @click.prevent="edit(billingfee)">
                                    <i class="fas fa-edit"></i>
                                </a>
                                <a href="" class="btn btn-sm btn-outline-danger ml-1" @click.prevent="remove(billingfee)">
                                    <i class="fas fa-trash"></i>
                                </a>
                            </td>
                            <td class="text-center">
                                <!-- <i class="drag-handle fa-lg text-bold">⠿</i> -->
                                <i class="ri-draggable drag-handle font-weight-bold" style="color: #383838; font-size: 2em;"></i>
                            </td>
                        </tr>
                    </transition-group>
                    <!-- </tbody> -->

                </table>
                <a href="" class="btn btn-sm btn-outline-primary ml-1 w-60 mt-1 py-3" @click.prevent="saveAll">
                    <i class="fas fa-save"></i> Save All
                </a>
                <div class="card-footer pb-0 pt-3">
                    <nav>
                        <ul class="pagination justify-content-center">
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
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-billingfee" id="exampleModalLabel">{{ modalBillingFee }}</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">
                    <form>
                        <div class="form-group">
                            <label>Fee Name *</label>
                            <!-- <input type="text" v-model="billingfee.feeName"
                            class="form-control" :class="{ 'is-invalid': errors.feeName }"> -->
                            <div v-if="editor">
                                <button class="btn bg-dark text-light btn-md p-1 mt-1" type="button" @click="editor.chain().focus().toggleBold().run()" :disabled="!editor.can().chain().focus().toggleBold().run()" :class="{ 'is-active': editor.isActive('bold') }">
                                    <i class="fa-lg ri-bold"></i>
                                </button>
                                <button class="btn bg-dark text-light btn-md p-1 mt-1" type="button" @click="editor.chain().focus().toggleItalic().run()" :disabled="!editor.can().chain().focus().toggleItalic().run()" :class="{ 'is-active': editor.isActive('italic') }">
                                    <i class="fa-lg ri-italic"></i>
                                </button>
                                <button class="btn bg-dark text-light btn-md p-1 mt-1" type="button" @click="editor.chain().focus().toggleStrike().run()" :disabled="!editor.can().chain().focus().toggleStrike().run()" :class="{ 'is-active': editor.isActive('strike') }">
                                    <i class="fa-lg ri-strikethrough"></i>
                                </button>
                                <button class="btn bg-dark text-light btn-md p-1 mt-1" type="button" @click="editor.chain().focus().undo().run()" :disabled="!editor.can().chain().focus().undo().run()">
                                    <i class="fa-lg ri-arrow-go-back-line"></i>
                                </button>
                                <button class="btn bg-dark text-light btn-md p-1 mt-1" type="button" @click="editor.chain().focus().redo().run()" :disabled="!editor.can().chain().focus().redo().run()">
                                    <i class="fa-lg ri-arrow-go-forward-line"></i>
                                </button>
                                </div>
                                <editor-content :editor="editor" />
                            <span class="invalid-feedback" v-if="errors.feeName">{{ errors.feeName[0] }}</span>
                        </div>
                        <div class="form-group">
                            <label>Fee Amount</label>
                            <vue-numeric currency="RM" placeholder="RM 0.00" :precision="2" separator=","
                                        v-model="billingfee.feeAmount" class="form-control" :class="{ 'is-invalid': errors.totalFees }"></vue-numeric>
                            <!-- <input type="text" v-model="billingfee.feeAmount"
                            class="form-control" :class="{ 'is-invalid': errors.feeAmount }"> -->
                            <span class="invalid-feedback" v-if="errors.feeAmount">{{ errors.feeAmount[0] }}</span>
                        </div>
                        <div class="form-group">
                            <label>Fee Tax</label>
                            <vue-numeric currency="RM" placeholder="RM 0.00" :precision="2" separator=","
                                        v-model="billingfee.feeTax" class="form-control" :class="{ 'is-invalid': errors.totalFees }"></vue-numeric>
                            <!-- <input type="text" v-model="billingfee.feeTax"
                            class="form-control" :class="{ 'is-invalid': errors.feeTax }"> -->
                            <span class="invalid-feedback" v-if="errors.feeTax">{{ errors.feeTax[0] }}</span>
                        </div>
                        <div class="form-group">
                            <label>Fee Total</label>
                            <vue-numeric currency="RM" placeholder="RM 0.00" :precision="2" separator=","
                                        v-model="billingfee.feeTotal" class="form-control" :class="{ 'is-invalid': errors.totalFees }"></vue-numeric>
                            <!-- <input type="text" v-model="billingfee.feeTotal"
                            class="form-control" :class="{ 'is-invalid': errors.feeTotal }"> -->
                            <span class="invalid-feedback" v-if="errors.feeTotal">{{ errors.feeTotal[0] }}</span>
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
                    <h5 class="modal-billingfee" id="exampleModalLabel2">{{ billingfee.id }} - {{ billingfee.feeTotal }}</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">

                        <div class="table-responsive-lg">
                            <table class="table table-hover table-light">
                                <tbody>
                                    <tr>
                                        <th class="text-right" scope="row" style="width: 150px;">Fee Name :</th>
                                        <!-- <td>{{ billingfee.feeName }}</td> -->
                                        <td v-html="billingfee.feeName"></td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Fee Amount:</th>
                                        <td>{{ formatPrice(billingfee.feeAmount) }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Fee Tax:</th>
                                        <td>{{ formatPrice(billingfee.feeTax) }}</td>
                                    </tr>
                                    <tr>
                                        <th class="text-right" scope="row">Fee Total:</th>
                                        <td>{{ formatPrice(billingfee.feeTotal) }}</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary mr-2" data-dismiss="modal" @click.prevent="edit(billingfee)">Edit</button>
                    <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                </div>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
import { bus } from '../app';
import { Editor, EditorContent } from '@tiptap/vue-2';
import StarterKit from '@tiptap/starter-kit';
import DOMPurify from 'dompurify';
import Link from '@tiptap/extension-link';
import draggable from 'vuedraggable';

export default {
    components: { 
        EditorContent, Editor,
        draggable
        
    },

    data: function () {
        return {
            billingfees: [],

            filters: {
                feeName: '',
                feeAmount: '',
                feeTax: '',
                feeTotal: '',
            },

            order: {
                dir: 1,
                // column: 'id'
                column: 'sorting'
            },

            perPage: 100,

            currentPage: 1,

            billingfee: {
                id: null,
                feeName: '',
                feeAmount: '',
                feeTax: '',
                feeTotal: '',
                billingId: '',
                sorting: ''
            },

            isEdit: false,

            isOpen: false,

            errors: {},

            removeBillingFeeId: null,

            search: '',
            editor: null,
            drag: false,
        }
    },

    mounted() {
        this.loadBillingFees();
        this.editor = new Editor({
            // content: '',
            content: this.billingfee.feeName,
            extensions: [
                StarterKit,
                
                Link.configure({
                    openOnClick: false,
                    HTMLAttributes: {
                        // Change rel to different value
                        // Allow search engines to follow links(remove nofollow)
                        rel: 'noopener noreferrer nofollow',
                        // rel: null,
                        // Remove target entirely so links open in current tab
                        target: '_blank',
                    },
                }),
            ],

            onUpdate: () => {
                const json = this.editor.getJSON();
                const html = DOMPurify.sanitize(this.editor.getHTML(json), { ADD_ATTR: ['target'] });
                // const html = DOMPurify.sanitize(replaceUrls(this.editor.getHTML(json)));
                console.log('json:', json);
                console.log('html:', html);
                this.billingfee.feeName = html;
            }
            
        });
    },

    computed: {
       
        billingfeesPaginated: {
            
            get() {
            // compute the paginated list of items
                let start = (this.currentPage - 1) * this.perPage
                let end = this.currentPage  * this.perPage
                return this.billingfeesSorted.slice(start, end)
            },
            set(value) {
            // update the paginated list of items
                let start = (this.currentPage - 1) * this.perPage
                let end = this.currentPage  * this.perPage
                // return this.billingfeesSorted.slice(start, end)
                this.billingfeesSorted.splice(start, end - start, ...value);
            }
        },

        billingfeesSorted () {
         // return this.products.sort((a, b) => (a.price - b.price) * this.order.dir); //Single sorting
        return this.filteredBillingFees.sort((a, b) => {
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
            return this.search.length > 0;
        },

        filteredBillingByGroup: function () {
            // return this.billingfees.filter(item => item.billingId == `${this.$route.params.id}` )
            return this.billingfees
        },

        filteredBillingFees: function () {
            return this.filteredBillingByGroup.filter((blog) => {
                    return blog.feeName.toLowerCase().match(this.search.toLowerCase()) ||
                        blog.feeTax.toLowerCase().match(this.search.toLowerCase())||
                        blog.feeAmount.toLowerCase().match(this.search.toLowerCase());
            });
        },

        isFirstPage () {
            return this.currentPage === 1;
        },

        isLastPage () {
            return this.currentPage >= this.pages;
        },

        pages () {
            return Math.ceil(this.filteredBillingFees.length / this.perPage);
        },

        modalBillingFee () {
            return this.isEdit ? "Update BillingFee" : "Add BillingFee"
        },

        modalTextButton () {
            return this.isEdit ? "Update" : "Save"
        },

        dragOptions() {
            return {
                animation: 200,
                group: "description",
                disabled: false,
                ghostClass: "ghost"
            };
        }

    },

    methods: {
        async updateSortingNo(item1, item2){
            
            try {

                console.log('Updating stepNo for:', { item1Id: item1.id, Sorting: item1.sorting, item2Id: item2.id, item2sortingNo: item2.sorting });
                // item 2 => drag item | item 1 => target item

                // When not affecting bottom index - move down
                if(item1.sorting > item2.sorting) {
                    var newSorting = item1.sorting - 1;
                    var tempsortingNo = item1.sorting;

                    // Send the updated data to the server using axios
                    await axios.put('/api/billingfees-sorting/' + item1.id, { sorting: newSorting });
                    await axios.put('/api/billingfees-sorting/' + item2.id, { sorting: tempsortingNo });

                    // Rearrange remaining steps
                    const remainingItems = this.billingfeesPaginated.filter(
                            item => item.sorting < item1.sorting && item.sorting > item2.sorting && item.id !== item2.id && item.id !== item1.id
                        );
                        console.log('remaining ids: ', remainingItems);
                    remainingItems.forEach(async (item, index) => {                
                        const newItemsortingNo = item.sorting - 1;
                        await axios.put('/api/billingfees-sorting/' + item.id, { sorting: newItemsortingNo });                
                    });
                    console.log('this is if: when dragged item moving down');
                }else {
                    // When move up

                    var newSorting = item1.sorting + 1;
                    var tempsortingNo = item1.sorting;
                    // Send the updated data to the server using axios
                    await axios.put('/api/billingfees-sorting/' + item1.id, { sorting: newSorting });
                    await axios.put('/api/billingfees-sorting/' + item2.id, { sorting: tempsortingNo });

                    // Rearrange remaining steps
                    const remainingItems = this.billingfeesPaginated.filter(
                        item => item.sorting > item1.sorting && item.sorting < item2.sorting && item.id !== item2.id && item.id !== item1.id
                        // item => item.sorting > item1.sorting && item.id !== item2.id
                        );
                        console.log('remaining ids: ', remainingItems);
                    remainingItems.forEach(async (item, index) => {                
                        const newItemsortingNo = item.sorting + 1;
                        await axios.put('/api/billingfees-sorting/' + item.id, { sorting: newItemsortingNo });                
                    });
                    console.log('this is else: when dragged item moving up');
                }


                this.loadBillingFees();
                } catch (error) {
                console.error('Error updating Sorting Number:', error);
                }
            // const sortedItems = this.filteredBillingFees.slice().sort((a, b) => a.sorting - b.sorting);

            // const item1Index = sortedItems.findIndex(item => item.id === item1.id);
            // const item2Index = sortedItems.findIndex(item => item.id === item2.id);
            // const item1Sorting = sortedItems.find(item => item.id === item1.id).sorting;
            // const item2Sorting = sortedItems.find(item => item.id === item2.id).sorting;

            // console.log('item 1 index: ', + item1Index + ' old sorting: ' + item1Sorting);
            // console.log('item 2 index: ', + item2Index + ' old sorting: ' + item2Sorting);

            // if (item1Index === -1 || item2Index === -1) {
            //     console.error('Error: Unable to find billing fees for the given indices');
            //     return;
            // }

            // // Update sorting number of the affected items
            // const updateItems = sortedItems.slice(Math.min(item1Index, item2Index), Math.max(item1Index, item2Index) + 1)
            //     .map((item, index) => ({
            //         ...item, sorting: item.sorting + index
            //         // ...item, sorting: Math.min(item1.sorting, item2.sorting) + index
            //         // ...item, sorting: Math.min(item1.sorting, item2.sorting) + index
            //     }));
            //     console.log('item 1: ID => ', + item1.id + ' Sorting => ' + item1.sorting );
            //     console.log('item 2: ID => ', + item2.id + ' Sorting => ' + item2.sorting );

            // // Update the database for the affected items
            // const promises = updateItems.map(item => axios.put('/api/billingfees-sorting/' + item.id, item));

            // Promise.all(promises)
            //     .then(() => {
            //         this.$toast.success("Records updated successfully.");
            //     })
            //     .catch(({ response }) => {
            //         this.errors = response.data.errors;
            //     });
        },

        onDragComplete(event) {
            // alert('Item Dragged');
            const item1 = this.billingfeesPaginated[event.moved.newIndex];
            const item2 = this.billingfeesPaginated[event.moved.oldIndex];

            if (item1 && item2) {
                this.updateSortingNo(item1, item2);
                // this.loadChecklistFileItems();
            } else {
                console.error('Error: Unable to find items for the given indices');
            }
        },

        handleCellEdit(event, billingfee, field) {
            // billingfee[field] = event.target.innerText;
            billingfee[field] = DOMPurify.sanitize(event.target.innerHTML);
        },

        add () {
            this.isEdit = false;

            this.billingfee = {
                id: null,
                billingId: `${this.$route.params.id}`,
                feeName: '',
                feeAmount: '',
                feeTax: '',
                feeTotal: '',
                }

                this.editor.commands.setContent('');

            $(this.$refs.vuemodal).modal('show');
        },

        edit (billingfee) {
            this.billingfee = Object.assign({}, billingfee);

            this.isEdit = true;
            this.editor.commands.setContent(this.billingfee.feeName);

            $(this.$refs.vuemodal).modal('show');

        },

        show (billingfee) {
            this.billingfee = Object.assign({}, billingfee);

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
        saveAll() {
            const promises = this.filteredBillingFees.map(billingfee => {
                return axios.put('/api/billingfees/' + billingfee.id, billingfee);
            });

            Promise.all(promises)
                .then(() => {
                this.$toast.success("All records updated successfully.");
                })
                .catch(({ response }) => {
                this.errors = response.data.errors;
                });
        },
        // saveAll() {
        //     const promises = this.billingfees.map(billingfee => {
        //         return axios.put('/api/billingfees/' + billingfee.id, billingfee);
        //     });

        //     Promise.all(promises)
        //         .then(() => {
        //         this.$toast.success("All records updated successfully.");
        //         })
        //         .catch(({ response }) => {
        //         this.errors = response.data.errors;
        //         });
        //     },
        update () {
            axios.put('/api/billingfees/' + this.billingfee.id, this.billingfee)
                .then(({ data }) => {

                    let index = this.billingfees.findIndex(item => item.id === this.billingfee.id);

                    this.billingfees.splice(index, 1, data.data);

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
            axios.post('/api/billingfees', this.billingfee)
                .then(({ data }) =>{
                    this.billingfees.unshift(data.data)
                    // this.departments.unshift(this.department)

                    this.billingfee = {
                        id: null,
                        feeName: '',
                        feeAmount: '',
                        feeTax: '',
                        feeTotal: ''
                    }

                    this.errors = {};

                    $(this.$refs.vuemodal).modal('hide');

                    this.$toast.success("Record Updated Successfully.");

                })
                .catch(({ response }) => {
                    this.errors = response.data.errors
                })

        },

        remove (billingfee) {
            if (confirm("Are you sure?")) {
                axios.delete('/api/billingfees/' + billingfee.id)
                    .then(res => {
                        this.removeBillingFeeId = billingfee.id

                        new Promise(resolve => setTimeout(resolve, 1000))
                            .then(() => {
                                this.removeBillingFeeId = null
                                let index = this.billingfees.findIndex(item => item.id === billingfee.id);
                                this.billingfees.splice(index, 1)
                                })
                            })

                        // let index = this.departments.findIndex(item => item.id === department.id);
                        // this.departments.splice(index, 1);
                    }
        },

        loadBillingFees: function () {
            axios.get('/api/single-bill-billingfees/' + `${this.$route.params.id}`)
            .then(({data}) => {
                this.billingfees = data.data;
            })
            .catch( function (error) {
                console.log(error);
            });

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

        beforeDestroy() {
            this.editor.destroy()
        },

        isHTML(str) {
            var a = document.createElement('div');
            a.innerHTML = str;

            for (var c = a.childNodes, i = c.length; i--; ) {
                if (c[i].nodeType == 1) return true; 
            }

            return false;
        }
    }

}
</script>
<style scoped>
    /* .table-striped>tbody>tr:nth-child(odd)>td, 
    .table-striped>tbody>tr:nth-child(odd)>th {
        background-color: rgba(128, 153, 228, 0.201); 
    } */
    .table-striped>tbody>tr:nth-child(even)>td, 
    .table-striped>tbody>tr:nth-child(even)>th {
        background-color: rgba(16, 165, 245, 0.2); 
        /* background-color: rgba(195, 209, 245, 0.845);  */
    }
    .drag-handle {
        cursor: grab;
    }
    .flip-list-move {
        transition: transform 0.5s;
    }
    .no-move {
        transition: transform 0s;
    }
    .ghost {
        opacity: 0.5;
        background: #c8ebfb;
    }

    .sortable-ghost {
        opacity: 0.5;
        background: #c8ebfb;
    }
</style>
