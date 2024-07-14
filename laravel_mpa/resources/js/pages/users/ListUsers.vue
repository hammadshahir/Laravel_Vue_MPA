<script setup>
import axios from 'axios';
import { ref, onMounted, reactive } from 'vue';

const users = ref([]);

const form = reactive({
        name: '',
        email: '',
        password: ''
    }) ;

    const getUsers = () => {
        axios.get('/api/users')
        .then((response) => {
            users.value = response.data;
        })
    }

    const createUser = () => {
        axios.post('/api/users', form)
        .then((response) => {
            // push data
            users.value.unshift(response.data);
            // Clear the Modal and close it
            form.name = '';
            form.email = '';
            form.password = '';
            $('#createUserModal').modal('hide');
        });
    }

    onMounted(() => {
        getUsers();
    });

</script>
<template>

    <div class="content-header">
            <div class="container-fluid">
                <div class="row mb-2">
                    <div class="col-sm-6">
                        <h1 class="m-0">Users</h1>
                        <button type="button" class="mt-2 mb-2 btn btn-success" data-toggle="modal" data-target="#createUserModal">
                            Add new user
                        </button>
                    </div>
                    <div class="col-sm-6">
                        <ol class="breadcrumb float-sm-right">
                            <li class="breadcrumb-item"><a href="/admin/dashboard">Home</a></li>
                            <li class="breadcrumb-item active">Users</li>
                        </ol>
                    </div>
                </div>
            </div>
        </div>

        <div class="content">
            <div class="container-fluid">
                <div class="row">
                    <table class="table table-bordered">
                        <thead class="thead-light">
                            <tr>
                                <th scope="col">Sr. #</th>
                                <th scope="col">Name</th>
                                <th scope="col">Email</th>
                                <th scope="col">Joining Date</th>
                                <th scope="col">Role</th>
                                <th scope="col">Options</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(user, index) in users" :key="user.id">
                                <th scope="row">{{index + 1}}</th>
                                <td>{{user.name}}</td>
                                <td>{{user.email}}</td>
                                <td>2023-01-15</td>
                                <td>Default</td>
                                <td>
                                    <button class="btn btn-primary btn-sm">Edit</button>
                                    <button class="btn btn-danger btn-sm">Delete</button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- Modal -->
    <div class="modal fade" id="createUserModal" tabindex="-1" role="dialog" aria-labelledby="createUserModal" aria-hidden="true">
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title" id="createUserModal">Add new user</h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                </button>
            </div>
        <div class="modal-body">
            <form>
                <div class="form-group">
                    <label for="name">Name</label>
                    <input v-model="form.name" type="text" class="form-control" id="name" placeholder="Enter name">

                </div>
                <div class="form-group">
                    <label for="email1">Email address</label>
                    <input v-model="form.email" type="email" class="form-control" id="email1" placeholder="Enter email">

                </div>
                <div class="form-group">
                    <label for="password">Password</label>
                    <input v-model = "password" type="password" class="form-control" id="password" placeholder="Password">
                </div>
                <!-- <div class="form-check">
                    <input type="checkbox" class="form-check-input" id="exampleCheck1">
                    <label class="form-check-label" for="exampleCheck1">Remember me</label>
                </div> -->
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                    <button @click="createUser" type="button" class="btn btn-success">Save</button>
                </div>
        </form>
    </div>

        </div>
    </div>
    </div>
</template>
