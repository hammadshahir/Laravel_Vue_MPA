<script setup>
    import axios from 'axios';
    import { ref, onMounted } from 'vue';
    import { Form, Field, useForm } from 'vee-validate';
    import * as yup from 'yup';

    const users = ref([]);
    const editing = ref(false);
    const formValues = ref();
    const form = ref(null);

    const getUsers = () => {
    axios.get('/api/users')
        .then((response) => {
        users.value = response.data;
        });
    }

    const validationSchema = yup.object({
      name: yup.string().required('Full Name is required'),
      email: yup.string().email('Email must be a valid email').required('Email is required'),
      password: yup.string().min(8, 'Password must be at least 8 characters').required('Password is required')
    });

    const { handleSubmit, resetForm } = useForm({
      validationSchema
    });

    const editUserSchema = yup.object({
      name: yup.string().required('Full Name is required'),
      email: yup.string().email('Email must be a valid email').required('Email is required'),
    });

    const createUser = handleSubmit((values, { resetForm, setErrors }) => {
    axios.post('/api/users', values)
        .then((response) => {
        users.value.unshift(response.data);
        $('#userFormModal').modal('hide');
        resetForm();
        })
        .catch((error) => {
        if (error.response && error.response.data) {
            setErrors(error.response.data.errors);
        }
        });
    });

    const editUser = (user) => {
      console.log(user);
      editing.value = true;
      form.value.resetForm();
      $('#userFormModal').modal('show');
      formValues.value = {
        id: user.id,
        name: user.name,
        email: user.email,
      };
    };

    const addUser = () => {
      editing.value = false;
      $('#userFormModal').modal('show');

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
          <button @click="addUser" type="button" class="mt-2 mb-2 btn btn-success">
            Add User
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
              <td>{{user.create_date}}</td>
              <td>Default</td>
              <td>
                <a class="btn btn-primary btn-sm" href="#" @click.prevent="editUser(user)"> <i class="fa fa-edit"></i> </a>
                <a class="btn btn-danger btn-sm" href="#"><i class="fa fa-trash"></i></a>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>

  <!-- Modal -->
  <div class="modal fade" id="userFormModal" tabindex="-1" role="dialog" aria-labelledby="createUserModal" aria-hidden="true">
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="createUserModal">
            <span v-if="editing">Edit user</span>
            <span v-else>Add a new user</span>
          </h5>

          <button type="button" class="close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-body">
          <Form ref="form" @submit="createUser" :validation-schema="validationSchema" v-slot="{ errors }" :initial-values="formValues">
            <div class="form-group">
              <label for="name">Name</label>
              <Field name="name" type="text" class="form-control" :class="{'is-invalid': errors.name}" id="name" placeholder="Enter full name" />
              <span class="invalid-feedback">{{ errors.name }}</span>
            </div>
            <div class="form-group">
              <label for="email">Email address</label>
              <Field name="email" type="email" class="form-control" :class="{'is-invalid': errors.email}" id="email" placeholder="Enter email" />
              <span class="invalid-feedback">{{ errors.email }}</span>
            </div>
            <div class="form-group">
              <label for="password">Password</label>
              <Field name="password" type="password" class="form-control" :class="{'is-invalid': errors.password}" id="password" placeholder="Password" />
              <span class="invalid-feedback">{{ errors.password }}</span>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
              <button type="submit" class="btn btn-success">Save</button>
            </div>
          </Form>
        </div>
      </div>
    </div>
  </div>
</template>
