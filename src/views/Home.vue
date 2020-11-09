<template>
<div>
    <div class="row justify-content-md-center">
        <div class="col-4 p-5">
            <div class="row justify-content-between align-items-center">
                <h3 class="mb-0">Todo List</h3>
                <b-button variant="success" @click="onClickAdd">Add</b-button>
            </div>
        </div>
    </div>
    <div class="row">
        <b-card v-for="(todo) in todos" :key="todo.id" :title="todo.title" class="col-12 mb-3">
            {{ todo.description }}
            <div>
                <b-btn variant="danger" @click="onHandleClickDelete(todo.id)">Delete</b-btn>
                <b-btn variant="info" @click="onHandleClickUpdate(todo)">Update</b-btn>
            </div>
        </b-card>
    </div>

    <b-modal id="modal-add-todo" title="Add Todo" @hidden="onHandleCancel">
        <b-form>
            <b-form-group label="Title:">
                <b-form-input v-model="form.title" placeholder="Add Title" />
            </b-form-group>

            <b-form-group label="Description:">
                <b-form-textarea id="textarea" v-model="form.description" placeholder="Enter todo description..." rows="3" max-rows="6"></b-form-textarea>
            </b-form-group>
        </b-form>

        <template v-slot:modal-footer>
            <div class="w-100">
                <b-button @click="onHandleCancel">Cancel</b-button>

                <b-button variant="primary" class="float-right" @click="onHandleUpdate" v-if="form.id">
                    Update
                </b-button>

                <b-button variant="primary" class="float-right" @click="onHandleConfirm" v-else>
                    Confirm
                </b-button>
            </div>
        </template>

    </b-modal>
</div>
</template>

<script>
export default {
    name: "Home",
    data() {
        return {
            todos: [],
            form: {
                title: '',
                description: ''
            }
        }
    },
    mounted() {
        this.getTodos()
    },
    methods: {
        getTodos() {
            this.$http({
                    method: 'get',
                    url: '/todos'
                })
                .then(res => {
                    this.todos = res.data
                })
        },
        onClickAdd() {
            this.$bvModal.show('modal-add-todo')
        },
        onHandleConfirm() {
            this.$http.post(
                    '/todos',
                    this.form
                )
                .then(() => {
                    this.form = {
                        title: '',
                        description: ''
                    }
                    this.$bvModal.hide('modal-add-todo')
                    this.getTodos()
                })
        },
        onHandleUpdate() {
            this.$http.patch(
                    `/todos/${this.form.id}`,
                    this.form
                )
                .then(() => {
                    this.form = {
                        title: '',
                        description: ''
                    }
                    this.$bvModal.hide('modal-add-todo')
                    this.getTodos()
                })
        },
        onHandleCancel() {
            this.form = {
                title: '',
                description: ''
            }
            this.$bvModal.hide('modal-add-todo')
        },
        onHandleClickDelete(id) {
            this.$http.delete(`/todos/${id}`)
                .then(() => {
                    this.form = {
                        title: '',
                        description: ''
                    }
                    this.$bvModal.hide('modal-add-todo')
                    this.getTodos()
                })
        },
        onHandleClickUpdate(todo) {
            this.form = todo
            this.$bvModal.show('modal-add-todo')
        }
    },
};
</script>
