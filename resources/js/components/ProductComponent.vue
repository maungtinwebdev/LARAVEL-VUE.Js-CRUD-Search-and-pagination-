<template>
    <div class="container">
        <div class="row justify-content-end mb-2">
            <div class="col-4">
                <button @click="add()" class="btn btn-primary"><i class="fas fa-plus-circle me-2"></i>Create</button>
            </div>
            <div class="col-4">
                <form @submit.prevent="searchproduct()">
                    <div class="input-group">
                        <input v-model="search" type="text" placeholder="Search" class="form-control">
                        <div class="input-group-append">
                            <button type="submit" class="btn btn-primary">
                                <i class="fa-solid fa-magnifying-glass"></i>
                            </button>
                        </div>
                    </div>
                </form>
            </div>

        </div>
        <div class="row d-flex flex-wrap">
            <div class="col-4">
                <div>
                    <!-- left-side -->
                    <div class="card position-sticky">
                        <h4 class="card-header">{{isEditMode? "EDIT": "CREATE"}}</h4>
                        <div class="card-body">
                            <form @submit.prevent="isEditMode? update(): store()">
                                <label class="input-group" for="">Name:</label>
                                <input v-model="product.name" type="text" class="form-control" required>

                                <label for="" class="input-group">Price:</label>
                                <input v-model="product.price" type="number" class="form-control" required>

                                <button type="submit" class="btn my-3 btn-primary"><i class="fas fa-save me-2"></i>{{isEditMode? "Update":"Save"}}</button>

                            </form>
                        </div>
                    </div>
                </div>
            </div>
            <div class="col-8">
                <div>
                    <!-- right-side -->
                    <table class="table table-striped">
                        <thead>
                            <tr>
                                <th>ID</th>
                                <th>Name</th>
                                <th>Price</th>
                                <th>Action</th>
                            </tr>
                        </thead>

                        <tbody>
                            <tr v-for="p in products.data" :key="p.id">
                                <td>{{p.id}}</td>
                                <td>{{p.name}}</td>
                                <td>{{p.price}}ks</td>
                                <td>
                                    <button @click="edit(p)" class="btn btn-success btn-sm" style="font-size: 7px;"><i class="fas fa-edit me-1"></i>Edit</button>
                                    <form @submit.prevent="del(p.id)">
                                        <button class="btn btn-danger btn-sm" style="font-size: 7px;"><i class="fas fa-trash-alt me-1"></i>Del</button>
                                    </form>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                    <Bootstrap5Pagination
                        :data="products"
                        :limit = '1'
                        :keep-length = '3'
                        @pagination-change-page="view"
                    />
                </div>
            </div>
        </div>

    </div>
</template>
<script>
// import pagination from 'laravel-vue-pagination'
import { Bootstrap5Pagination } from 'laravel-vue-pagination';
export default {
    name:"ProductComponent",
    components:{
        Bootstrap5Pagination,
    },
    data(){
        return {
            search:"",
            isEditMode:false,
            products:{},
            product: {
                name:"",
                price:""
            }
        }
    },
    methods:{
        searchproduct(){
            axios.get(`http://localhost:8000/api/product?search=` + this.search)
            .then(res=>{
                this.products = res.data;
            });

        },
        add() {
            this.isEditMode = false;
            this.product = {
                name:"",
                price:"",
            }
        },
        view(page = 1) {
            axios.get(`http://localhost:8000/api/product?page=` + page)
            .then(res=>{
                this.products = res.data;
            });
        },

        store() {
            axios.post("http://localhost:8000/api/product", this.product)
            .then(res=>{
                this.view();
                this.product = {
                    id:"",
                    name:"",
                    product:"",
                }

            });
            return this.view();
        },

        edit(p) {
            this.isEditMode = true;
            this.product.id = p.id;
            this.product.name = p.name;
            this.product.price = p.price;
        },

        update() {
            axios.put(`http://localhost:8000/api/product/${this.product.id}`, this.product)
            .then(res=>{
                this.view();
                this.product.id = "";
                this.product.name = "";
                this.product.price = "";

            })
        },

       del(id) {
            // if(!confirm('Are you sure to Delte?')) {
            //     return;
            // }
            axios.delete(`http://localhost:8000/api/product/${id}`)
            .then(res=>{
                this.product = res.data;
                this.view();
            });
            return this.view();
        }
    },
    // computed: {
    //   filteredData() {
    //     return this.products
    //       .filter(
    //         (entry) => this.products.length
    //           ? Object.keys(this.products[0])
    //               .some(key => ('' + entry[key]).toLowerCase().includes(this.search))
    //           : true
    //       );
    //   }
    // },
    created(){
        this.view();
        this.del();
    }
}
</script>
<style>

</style>
