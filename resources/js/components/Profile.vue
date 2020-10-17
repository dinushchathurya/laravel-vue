<template>
    <div class="container">
        <div class="row">
            <div class="col-md-12 mt-3">
                <div class="card card-widget widget-user">
                    <div class="widget-user-header text-white" style="background-image:url('./img/maxresdefault.jpg')">
                        <h3 class="widget-user-username text-right">Elizabeth Pierce</h3>
                        <h5 class="widget-user-desc text-right">Web Designer</h5>
                    </div>
                    <div class="widget-user-image">
                        <img class="img-circle" src="" alt="User Avatar">
                    </div>
                    <div class="card-footer">
                        <div class="row">
                            <div class="col-sm-4 border-right">
                                <div class="description-block">
                                    <h5 class="description-header">3,200</h5>
                                    <span class="description-text">SALES</span>
                                </div>
                            </div>
                            <div class="col-sm-4 border-right">
                                <div class="description-block">
                                    <h5 class="description-header">13,000</h5>
                                    <span class="description-text">FOLLOWERS</span>
                                </div>
                            </div>
                            <div class="col-sm-4">
                                <div class="description-block">
                                    <h5 class="description-header">35</h5>
                                    <span class="description-text">PRODUCTS</span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="card">
            <div class="card-header p-2">
                <ul class="nav nav-pills">
                    <li class="nav-item"><a class="nav-link" href="#activity" data-toggle="tab">Activity</a></li>
                    <li class="nav-item"><a class="nav-link active" href="#settings" data-toggle="tab">Settings</a></li>
                </ul>
            </div>
            <div class="card-body">
                <div class="tab-content">
                    <div class="tab-pane" id="activity">
                    </div>
                    <div class="tab-pane active" id="settings">
                        <form class="form-horizontal">
                            <div class="form-group">
                                <label for="inputName" class="col-sm-2 col-form-label">Name</label>
                                <div class="col-sm-10">
                                    <input v-model="form.name" type="text" name="name" placeholder="Name"
                                        class="form-control">
                                </div>
                            </div>
                            <div class="form-group">
                                <label for="inputEmail" class="col-sm-2 col-form-label">Email</label>
                                <div class="col-sm-10">
                                    <input type="email" v-model="form.email" class="form-control" id="inputEmail"
                                        placeholder="Email">
                                </div>
                            </div>
                            <div class="form-group">
                                <label for="inputExperience" class="col-sm-2 col-form-label">Experience</label>
                                <div class="col-sm-10">
                                    <textarea class="form-control" id="inputExperience"
                                        placeholder="Experience"></textarea>
                                </div>
                            </div>
                            <div class="form-group ">
                                <label for="inputExperience" class="col-sm-2 col-form-label">Profile Photo</label>
                                <div class="col-sm-12">
                                    <input type="file" @change="updateprofile" class="form-control" name="photo">
                                </div>
                            </div>
                            <div class="form-group">
                                <label for="inputName" class="col-sm-2 col-form-label">Passport</label>
                                <div class="col-sm-12">
                                    <input type="passport" class="form-control" id="inputName" placeholder="Name">
                                </div>
                            </div>
                            <div class="form-group  mt-3">
                                <div class="offset-sm-2 col-sm-10">
                                    <button type="submit" @click.prevent="updateInfo"
                                        class="btn btn-danger">Submit</button>
                                </div>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
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
        mounted() {
            console.log('Component mounted.')
        },
        methods: {
            updateInfo() {
                this.$Progress.start();
                this.form.put('api/profile/')
                    .then(() => {
                        this.$Progress.finish();
                    })
                    .catch(() => {
                        this.$Progress.fail();
                    })
            },
            updateprofile(e) {
                let file = e.target.files[0];
                let reader = new FileReader();
                if (file['size'] < 2111775) {
                    reader.onloadend = (file) => {
                        this.form.photo = reader.result;
                    }
                    reader.readAsDataURL(file);
                }else{
                    swal.fire({
                        type : 'error',
                        icon: 'error',
                        title: ' Oops...',
                        text :'Your are uploading a large file',
                    })
                }
            }
        },
        created() {
            axios.get("api/profile").then(({
                data
            }) => (this.form.fill(data)));
        }
    }

</script>
