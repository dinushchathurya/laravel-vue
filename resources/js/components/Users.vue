<template>
    <div class="container">
        <div class="row mt-5" v-if="$gate.isAdminOrAuthor()">
            <div class="col-md-12">
                <div class="card">
                    <div class="card-header">
                    <h3 class="card-title">Users Table</h3>
                        <div class="card-tools">
                            <button class="btn btn-success" @click="newModal">Add New User 
                                <i class ="fas fa-user-plus"></i>
                            </button>
                        </div>
                    </div>   
                    <div class="card-body table-responsive p-0">
                        <table class="table table-hover text-nowrap">
                            <thead>
                            <tr>
                                <th>ID</th>
                                <th>Name</th>
                                <th>E-mail</th>
                                <th>Type</th>
                                <th>Registered at</th>
                                <th>Modify</th>
                            </tr>
                            </thead>
                            <tbody>
                            <tr v-for="user in users.data" :key="user.id">
                                <td>{{user.id}}</td>
                                <td>{{user.name}}</td>
                                <td>{{user.email}}</td>
                                <td>{{user.type | upText}}</td>
                                <td>{{user.created_at | myDate }}</td>
                                <td>
                                    <a href="#" @click="editModal(user)">
                                        <i class="fa fa-edit blue"></i>
                                    </a>
                                    /
                                    <a href="#" @click="deleteUser(user.id)">
                                        <i class="fa fa-trash red"></i>
                                    </a>
                                </td>
                            </tr>
                            </tbody>
                        </table>
                    </div>
                    <div class="card-footer">
                        <pagination :data="users" @pagination-change-page="getResults"></pagination>
                    </div>
                </div>
            </div>
        </div>
          <div class="mt-5" v-if="!$gate.isAdminOrAuthor()">
            <h2 style="text-align:center;">Not Found</h2>
            <not-found></not-found>
        </div>
        <div class="modal fade" id="addNew" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel" v-show="!editmode"><b>Create New User</b></h5>
                        <h5 class="modal-title" id="exampleModalLabel" v-show="editmode"><b>Update User</b></h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <form @submit.prevent="editmode ? updateUser() : createUser()">
                        <div class="modal-body">
                            <div class="form-group">
                                <input v-model="form.name" type="text" name="name" placeholder="Enter your name"
                                    class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                                <has-error :form="form" field="name"></has-error>
                            </div>
                            <div class="form-group">
                                <input v-model="form.email" type="email" name="email" placeholder="Enter your E-mail"
                                    class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                                <has-error :form="form" field="email"></has-error>
                            </div>
                            <div class="form-group">
                                <textarea v-model="form.bio" name="bio" id="bio" placeholder="Enter Short Bio"
                                    class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }"></textarea>
                                <has-error :form="form" field="bio"></has-error>
                            </div>
                            <div class="form-group">
                                <select name="type" v-model="form.type" id="type"
                                    class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                                    <option value="">Select User Type</option>
                                    <option value="admin">Admin</option>
                                    <option value="user">User</option>
                                    <option value="author">Author</option>
                                </select>
                                <has-error :form="form" field="type"></has-error>
                            </div>
                            <div class="form-group">
                                <input v-model="form.password" type="password" name="password" placeholder="Enter your password"
                                    class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                                <has-error :form="form" field="password"></has-error>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
                            <button v-show="editmode" type="submit" class="btn btn-success">Save changes</button>
                            <button v-show="!editmode" type="submit" class="btn btn-primary">Create User</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    export default{
         data () {
            return {
                editmode:false,
                users:{},
                form: new Form({
                    id: '',
                    name: '',
                    email: '',
                    password: '',
                    type: '',
                    bio: '',
                    photo: ''
                })
            }
        },
        methods:{
            getResults(page = 1) {
            axios.get('api/user?page=' + page)
                .then(response => {
                    this.users = response.data;
                });
            },
            updateUser(){
                this.$Progress.start();
                this.form.put('api/user/'+this.form.id)
                .then(() =>{
                    $('#addNew').modal('hide');
                    swal.fire(
                        'Updated!',
                        'User has been updated successfully.',
                        'success'
                        )
                    this.$Progress.finish();
                    Fire.$emit('AfterCreate');
                })
                .catch(() =>{
                    this.$Progress.fail();
                })
            },
            editModal(user){
                this.editmode = true;
                this.form.reset();
                $('#addNew').modal('show');
                this.form.fill(user);
            },
            newModal(){
                this.editmode = false;
                this.form.reset();
                $('#addNew').modal('show');
            },
            deleteUser(id){
                swal.fire({
                    title: 'Are you sure?',
                    text: "You won't be able to revert this!",
                    icon: 'warning',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Yes, delete it!'
                    }).then((result) => {
                        if(result.value){
                            this.form.delete('api/user/'+id).then(()=>{
                                swal.fire(
                                'Deleted!',
                                'User has been deleted.',
                                'success'
                                )
                                Fire.$emit('AfterCreate');
                            }).catch(()=>{
                                swal("Failed!", "There was something went wrong.", "wrong");
                            });
                        }
                    })
            },
            loadUsers(){
                if(this.$gate.isAdminOrAuthor()){
                     axios.get("api/user").then(({ data }) => (this.users = data));
                }
            },
            createUser(){
                this.$Progress.start();
                this.form.post('api/user')
                .then(() =>{
                    Fire.$emit('AfterCreate');
                    $('#addNew').modal('hide')
                    toast.fire({
                        icon: 'success',
                        title: 'User created successfully'
                    });
                    this.$Progress.finish();
                })
                .catch(() =>{

                })
            }
        },
        mounted(){
            Fire.$on('searching', () => {
                let query = this.$parent.search;
                axios.get('api/findUser?q=' + query)
                .then((data)=>{
                    this.users - data.data;
                })
                .catch(()=>{

                })
            });
            this.loadUsers();
            Fire.$on('AfterCreate', () => {
                this.loadUsers();
            });
        //    setInterval(() => this.loadUsers(), 3000)
        }
    }
</script>