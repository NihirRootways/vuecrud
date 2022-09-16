<template>
    <div class="container mt-5">
        <div class="form-group mb-3">
            <label for="name">Name</label>
            <input type="text" name="name" placeholder="Enter name" class="form-control" v-model="item.name">
        </div>
        <div class="form-group mb-3">
            <label for="phone">phone</label>
            <input type="text" name="phone" placeholder="Enter phone" class="form-control" v-model="item.phone">
        </div>
        <button class="btn btn-primary btn-block" @click="save">
            {{ isEditing ? 'Update' : 'Save' }}
        </button>
        <div class="col-md-12 mt-3" v-if="list.length > 0">
            <h2 class="text-center"> Telephone Numbers </h2>
            <ul class="list-group">
                <li
                    class="list-group-item"
                    v-for="item in list"
                    :key="item.id"
                    >
                    {{ item.name }} ( {{ item.phone }} )
                    <span class="float-right" style="float: right;">
                        <button class="btn btn-warning btn-sm mr-2" @click="editTel(item)">Edit</button>
                        <button class="btn btn-danger btn-sm mr-2" @click="deleteTel(item.id)">Delete</button>
                    </span>
                </li>
            </ul>
        </div>
    </div>

</template>

<script>
import axios from 'axios';

    export default{
        name:"Directory",
        data(){
            return{
                list:[],
                item:{
                    name:"",
                    phone:""
                },
                temp_id: null,
                isEditing: false,
            }
        },
        mounted(){
            this.fetchAll();
        },
        methods:{
            fetchAll(){
                axios.get('/api/tel')
                .then(res=> this.list = res.data)
            },
            emptyAll(){
                this.item={
                    name:"",
                    phone:""
                }
            },
            save(){
                let methods = axios.post;
                let url = '/api/tel';
                if(this.isEditing){
                    methods = axios.put;
                    url = `/api/tel/${this.temp_id}`;
                }
                try{
                    methods(url, this.item)
                    .then(res => {
                        this.fetchAll()
                        this.item = {
                            name: "",
                            tel: ""
                        }
                        this.temp_id = null
                        this.isEditing = false
                    })
                }catch(e){
                    console.log(e)
                }
            },
            editTel(tel){
                this.item={
                    name:tel.name,
                    phone:tel.phone,
                },
                this.temp_id = tel.id;
                this.isEditing = true;
            },
            deleteTel(id){
                try{
                    axios.delete(`api/tel/${id}`)
                    .then(res=>this.fetchAll());
                }catch(e){

                }
            }
        }
    }
</script>

<style scoped>

</style>
