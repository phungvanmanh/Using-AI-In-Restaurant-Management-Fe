<template>
    <div class="row">
        <div class="col-xl-3 col-lg-3 col-md-3 col-sm-12">
            <CardComponent>
                <template #card-header>
                    <span><b>Add New Category</b></span>
                </template>
                <template #card-body>
                    <InputComponent v-model="addCotegory.name_category" @keyup="convertToSlug(addCotegory)"
                        label="Name Category" placeholder="Enter the category name..." />
                    <InputComponent v-model="addCotegory.slug_category" label="Slug Category"
                        placeholder="Enter the category slug..." :disabled="true" />
                    <SelectComponent v-model="addCotegory.status" label="Status" :options="status" />
                </template>
                <template #card-footer>
                    <button class="btn btn-primary float-end" @click="addNew()">
                        Add new
                    </button>
                </template>
            </CardComponent>
        </div>
        <div class="col-xl-9 col-lg-9 col-md-9 col-lg-12">
            <CardComponent :required="false">
                <template #card-header>
                    <span><b>List Category</b></span>
                </template>
                <template #card-body>
                    <TableComponent>
                        <template #thead>
                            <tr>
                                <th class="text-center align-middle text-nowrap">#</th>
                                <th class="text-center align-middle text-nowrap">
                                    Name Category
                                </th>
                                <th class="text-center align-middle text-nowrap">
                                    Slug Category
                                </th>
                                <th class="text-center align-middle text-nowrap">Status</th>
                                <th class="text-center align-middle text-nowrap">Action</th>
                            </tr>
                        </template>
                        <template #tbody>
                         
                                <tr v-for="(value, key) in dataDanhMuc" :key="key">
                                    <th class="text-center align-middle text-nowrap">
                                        {{ key + 1 }}
                                    </th>
                                    <td class="align-middle text-nowrap">
                                        {{ value.name_category }}
                                    </td>
                                    <td class="align-middle text-nowrap">
                                        {{ value.slug_category }}
                                    </td>
                                    <td class="text-center align-middle text-nowrap">
                                        <template v-if="value.status == 1">
                                            <button class="btn btn-outline-info" @click="changeStatus(value)"
                                                style="width: 120px">
                                                Hiển Thị
                                            </button>
                                        </template>
                                        <template v-else>
                                            <button class="btn btn-outline-danger" @click="changeStatus(value)"
                                                style="width: 120px">
                                                Tạm Đóng
                                            </button>
                                        </template>
                                    </td>
                                    <td class="text-center align-middle text-nowrap">
                                        <ModalComponent color="success" label="Edit Category" titleId="edit_category"
                                            @click="Object.assign(editCotegory, value)">
                                            <template #modal-header>
                                                <span><b>Edit Category</b></span>
                                            </template>
                                            <template #modal-body>
                                                <InputComponent v-model="editCotegory.name_category"
                                                    @keyup="convertToSlug(editCotegory)" label="Name Category"
                                                    placeholder="Enter the category name..." />
                                                <InputComponent v-model="editCotegory.slug_category" label="Slug Category"
                                                    placeholder="Enter the category slug..." :disabled="true" />
                                                <SelectComponent v-model="editCotegory.status" label="Status"
                                                    :options="status" />
                                            </template>
                                            <template #modal-footer>
                                                <button class="btn btn-primary" @click="updateDanhMuc()">
                                                    Update
                                                </button>
                                                <button class="btn btn-secondary" data-bs-dismiss="modal">
                                                    Close
                                                </button>
                                            </template>
                                        </ModalComponent>
                                        <ModalComponent color="danger" label="Delete Category" titleId="delete_category"
                                            @click="Object.assign(deleteCotegory, value)">
                                            <template #modal-header>
                                                <span><b>Delete Category</b></span>
                                            </template>
                                            <template #modal-body>
                                                Are you sure you want to delete this
                                                <span class="text-danger"><b><i>{{ deleteCotegory.name_category}}</i></b></span>
                                                section?
                                            </template>
                                            <template #modal-footer>
                                                <button class="btn btn-danger" @click="deleteDanhMuc()">
                                                    Delete
                                                </button>
                                                <button class="btn btn-secondary" data-bs-dismiss="modal">
                                                    Close
                                                </button>
                                            </template>
                                        </ModalComponent>
                                    </td>
                                </tr>
                        
                        </template>
                    </TableComponent>
                </template>
            </CardComponent>
        </div>
    </div>
</template>

<script>
import CardComponent from "@/components/CardComponent.vue";
import InputComponent from "@/components/InputComponent.vue";
import SelectComponent from "@/components/SelectComponent.vue";
import TableComponent from "@/components/TableComponent.vue";
import ModalComponent from "@/components/ModalComponent.vue";
import { ref, computed, onMounted } from "vue";
import { useStore } from "vuex";
import axios from "@/axiosConfig";
import Toast from "@/toastConfig";
import $ from "jquery";

export default {
    name: "danh-muc",
    components: {
        CardComponent,
        InputComponent,
        SelectComponent,
        TableComponent,
        ModalComponent,
    },
    setup() {
        const store = useStore();
        const status = computed(() => store.state.tinh_trang);
        const addCotegory = ref({});
        const editCotegory = ref({});
        const deleteCotegory = ref({});
        const dataDanhMuc = computed(() => {
            return store.state.dataDanhMuc;
        });
        const convertToSlug = (obj) => {
            obj.slug_category = store.getters.toSlug(obj.name_category);
        };
        const addNew = () => {
            axios
                .post("admin/danh-muc/create", addCotegory.value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                        addCotegory.value = {};
                        store.dispatch("onFetchDanhMuc");
                    }
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast("error", v[0]);
                    });
                });
        };

        const changeStatus = (value) => {
            axios
                .post("admin/danh-muc/change-status", value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                        addCotegory.value = {};
                        store.dispatch("onFetchDanhMuc");
                    }
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast("error", v[0]);
                    });
                });
        };

        const updateDanhMuc = () => {
            axios
                .post("admin/danh-muc/update", editCotegory.value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                        $("#edit_category").modal("hide");
                        addCotegory.value = {};
                        store.dispatch("onFetchDanhMuc");
                    }
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast("error", v[0]);
                    });
                });
        };

        const deleteDanhMuc = () => {
            axios
                .post("admin/danh-muc/delete", deleteCotegory.value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                        $("#delete_category").modal("hide");
                        addCotegory.value = {};
                        store.dispatch("onFetchDanhMuc");
                    }
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast("error", v[0]);
                    });
                });
        };

        onMounted(() => {
            store.dispatch("onFetchDanhMuc");
        });

        return {
            status,
            addCotegory,
            editCotegory,
            deleteCotegory,
            dataDanhMuc,
            addNew,
            convertToSlug,
            changeStatus,
            updateDanhMuc,
            deleteDanhMuc,
        };
    },
};
</script>

<style></style>
