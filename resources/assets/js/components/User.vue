<template>
    <div class="container">
        <div class="row mt-5">
            <div class="col-12">
                <div class="card" v-if="$gate.isAdmin()">
                    <div class="card-header">
                        <h3 class="card-title">Responsive Hover Table</h3>

                        <div class="card-tools">
                            <button class="btn btn-success" @click="newModal">
                                Add New <i class="fas fa-user-plus"></i>
                            </button>
                        </div>
                    </div>
                    <!-- /.card-header -->
                    <div class="card-body table-responsive p-0">
                        <table class="table table-hover">
                            <thead>
                                <tr>
                                    <th>ID</th>
                                    <th>Name</th>
                                    <th>Email</th>
                                    <th>Type</th>
                                    <th>Registered At</th>
                                    <th>Modify</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="user in users" :key="user.id">
                                    <td>{{ user.id }}</td>
                                    <td>{{ user.name }}</td>
                                    <td>{{ user.email }}</td>
                                    <td>{{ user.type | upText }}</td>
                                    <td>{{ user.created_at | myDate }}</td>
                                    <td>
                                        <a href="#" @click="editModal(user)">
                                            <i class="fa fa-edit blue"></i>
                                        </a>
                                        /
                                        <a
                                            href="#"
                                            @click="deleteUser(user.id)"
                                        >
                                            <i class="fa fa-trash red"></i>
                                        </a>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                    <!-- /.card-body -->
                </div>
                <!-- /.card -->
            </div>
        </div>

        <!-- add user modal -->
        <div
            class="modal fade"
            id="addModal"
            tabindex="-1"
            role="dialog"
            aria-labelledby="addModalLabel"
            aria-hidden="true"
        >
            <form @submit.prevent="editmode ? updateUser() : createUser()">
                <div class="modal-dialog" role="document">
                    <div class="modal-content">
                        <div class="modal-header">
                            <h5
                                v-show="!editmode"
                                class="modal-title"
                                id="exampleModalLabel"
                            >
                                Add New User
                            </h5>
                            <h5
                                v-show="editmode"
                                class="modal-title"
                                id="exampleModalLabel"
                            >
                                Edit User Info
                            </h5>
                            <button
                                type="button"
                                class="close"
                                data-dismiss="modal"
                                aria-label="Close"
                            >
                                <span aria-hidden="true">&times;</span>
                            </button>
                        </div>
                        <div class="modal-body">
                            <div class="form-group">
                                <label>Name</label>
                                <input
                                    placeholder="Name"
                                    v-model="form.name"
                                    type="text"
                                    name="name"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has('name')
                                    }"
                                />
                                <has-error
                                    :form="form"
                                    field="name"
                                ></has-error>
                            </div>

                            <div class="form-group">
                                <label>Email</label>
                                <input
                                    placeholder="Email"
                                    v-model="form.email"
                                    type="email"
                                    name="email"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has('email')
                                    }"
                                />
                                <has-error
                                    :form="form"
                                    field="email"
                                ></has-error>
                            </div>

                            <div class="form-group">
                                <label>Bio</label>
                                <input
                                    placeholder="Bio"
                                    v-model="form.bio"
                                    type="text"
                                    name="bio"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has('bio')
                                    }"
                                />
                                <has-error :form="form" field="bio"></has-error>
                            </div>

                            <div class="form-group">
                                <label>User Type</label>
                                <select
                                    v-model="form.type"
                                    type="text"
                                    name="type"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has('type')
                                    }"
                                >
                                    <option value="">Select your role</option>
                                    <option value="admin">Admin</option>
                                    <option value="user">User</option>
                                    <option value="author">Author</option>
                                </select>
                                <has-error
                                    :form="form"
                                    field="type"
                                ></has-error>
                            </div>

                            <div class="form-group">
                                <label>Password</label>
                                <input
                                    placeholder="*************"
                                    v-model="form.password"
                                    type="password"
                                    name="passwrod"
                                    class="form-control"
                                    :class="{
                                        'is-invalid': form.errors.has(
                                            'password'
                                        )
                                    }"
                                />
                                <has-error
                                    :form="form"
                                    field="password"
                                ></has-error>
                            </div>
                        </div>

                        <div class="modal-footer">
                            <button
                                type="button"
                                class="btn btn-danger"
                                data-dismiss="modal"
                            >
                                Close
                            </button>
                            <button
                                v-show="!editmode"
                                type="submit"
                                class="btn btn-primary"
                            >
                                Add
                            </button>
                            <button
                                v-show="editmode"
                                type="submit"
                                class="btn btn-primary"
                            >
                                Save
                            </button>
                        </div>
                    </div>
                </div>
            </form>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            editmode: true,
            users: {},
            form: new Form({
                id: "",
                name: "",
                email: "",
                password: "",
                type: "",
                bio: "",
                photo: ""
            })
        };
    },
    methods: {
        newModal() {
            this.editmode = false;
            this.form.reset();
            $("#addModal").modal("show");
        },
        editModal(user) {
            this.editmode = true;
            this.form.reset();
            this.form.fill(user);
            $("#addModal").modal("show");
        },
        loadUsers() {
            if (this.$gate.isAdmin()) {
                axios.get("api/user").then(({ data }) => {
                    this.users = data.data;
                });
            }
        },
        createUser() {
            this.$Progress.start();
            this.form
                .post("api/user")
                .then(() => {
                    Fire.$emit("AfterCreate");
                    this.$Progress.finish();
                    $("#addModal").modal("hide");
                    Toast.fire({
                        icon: "success",
                        title: "User created successfully"
                    });
                })
                .catch(() => {
                    this.$Progress.finish();
                });
        },
        updateUser() {
            this.$Progress.start();
            this.form
                .put("api/user/" + this.form.id)
                .then(() => {
                    Fire.$emit("AfterCreate");
                    $("#addModal").modal("hide");
                    this.$Progress.finish();
                    Swal.fire("Updated!", "User has been deleted.", "success");
                })
                .catch(e => {
                    this.$Progress.fail();
                });
        },
        deleteUser(id) {
            Swal.fire({
                title: "Are you sure?",
                text: "You won't be able to revert this!",
                icon: "warning",
                showCancelButton: true,
                confirmButtonColor: "#3085d6",
                cancelButtonColor: "#d33",
                confirmButtonText: "Yes, delete it!"
            }).then(result => {
                this.$Progress.start();
                this.form
                    .delete("api/user/" + id)
                    .then(() => {
                        Fire.$emit("AfterCreate");
                        this.$Progress.finish();
                        if (result.value) {
                            Swal.fire(
                                "Deleted!",
                                "Your file has been deleted.",
                                "success"
                            );
                        }
                    })
                    .catch(() => {
                        Swal.fire("Error", "Something went wrong", "warning");
                    });
            });
        }
    },
    created() {
        this.loadUsers();
        Fire.$on("AfterCreate", () => {
            this.loadUsers();
            console.log("loading users...");
        });
    }
};
</script>
