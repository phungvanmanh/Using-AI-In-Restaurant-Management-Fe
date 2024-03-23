<template lang="">
<div class="row">
    <div class="col-xl-3 col-lg-3 col-md-3 col-sm-12">
        <CardComponent>
            <template #card-header>
                <span><b>Add New Table</b></span>
            </template>
            

            <template #card-body>
                <InputComponent label="Name table" placeholder="Enter the name table..." @keyup="convertToSlug(addTable)" v-model="addTable.name_table" />
                <InputComponent label="Slug table" :disabled="true" v-model="addTable.slug_table" />
                <SelectComponent label="Area" :options="area" v-model="addTable.id_area" />
                <SelectComponent label="Status" :options="status" v-model="addTable.status" />
            </template>
            <template #card-footer>
                <button class="btn btn-primary float-end" @click="addNew()">Add news</button>
            </template>
        </CardComponent>
    </div>
    
    <div class="col-xl-9 col-lg-9 col-md-9 col-sm-12">
        <CardComponent :required="false">
            <template #card-header>
                <span><b>List of table</b></span>
            </template>
            <template #card-body>
                <TableComponent>
                    <template #thead>
                        <tr>
                            <th class="text-center align-middle text-nowrap">#</th>
                            <th class="text-center align-middle text-nowrap">Name Table</th>
                            <th class="text-center align-middle text-nowrap">Slug Table</th>
                            <th class="text-center align-middle text-nowrap">Area</th>
                            <th class="text-center align-middle text-nowrap">Status</th>
                            <th class="text-center align-middle text-nowrap">Action</th>
                        </tr>
                    </template>
                    <template #tbody>
                        <template v-for="(value, key) in dataBan" :key="key">
                            <tr>
                                <th class="text-center align-middle text-nowrap">{{ key +1 }}</th>
                                <td class="align-middle text-nowrap">{{ value.name_table }}</td>
                                <td class="align-middle text-nowrap">{{ value.slug_table }}</td>
                                <td class="align-middle text-nowrap">{{ value.name_area }}</td>
                                <td class="text-center align-middle text-nowrap">
                                    <template v-if="value.status == 1">
                                        <button class="btn btn-outline-info" @click="changeStatus(value)" style="width: 120px">
                                            Hiển Thị
                                        </button>
                                    </template>
                                    <template v-else>
                                        <button class="btn btn-outline-danger" @click="changeStatus(value)" style="width: 120px">
                                            Tạm Đóng
                                        </button>
                                    </template>
                                </td>
                                <td class="text-center align-middle text-nowrap">
                                    <ModalComponent color="success" label="Edit Table" titleId="edit_table" @click="Object.assign(editTable, value)">
                                        <template #modal-header>
                                            <span><b>Edit Table</b></span>
                                        </template>
                                        <template #modal-body>
                                            <InputComponent v-model="editTable.name_table" @keyup="convertToSlug(editTable)" label="Name table" placeholder="Enter the table name..." />
                                            <InputComponent v-model="editTable.slug_table" label="Slug table" :disabled="true" />
                                            <SelectComponent label="Area" :options="area" v-model="editTable.id_area" />
                                            <SelectComponent label="Status" :options="status" v-model="editTable.status" />
                                        </template>
                                        <template #modal-footer>
                                            <button class="btn btn-primary" @click="updateBan()">
                                                Update
                                            </button>
                                            <button class="btn btn-secondary" data-bs-dismiss="modal">
                                                Close
                                            </button>
                                        </template>
                                    </ModalComponent>
                                    <ModalComponent color="danger" label="Delete Table" titleId="delete_table" @click="Object.assign(deleteTable, value)">
                                        <template #modal-header>
                                            <span><b>Delete Table</b></span>
                                        </template>
                                        <template #modal-body>
                                            Are you sure you want to delete this
                                            <span class="text-danger"><b><i>{{ deleteTable.name_table}}</i></b></span>
                                            section?
                                        </template>
                                        <template #modal-footer>
                                            <button class="btn btn-danger" @click="deleteBan()">
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
import TableComponent from '@/components/TableComponent.vue';
import ModalComponent from '@/components/ModalComponent.vue';
import {
    ref,
    computed,
    onMounted
} from "vue";
import {
    useStore
} from "vuex";
import axios from "@/axiosConfig";
import Toast from "@/toastConfig";
import $ from "jquery";
export default {
    name: "ban-component",
    components: {
        CardComponent,
        InputComponent,
        SelectComponent,
        TableComponent,
        ModalComponent
    },
    setup() {
        const store = useStore();
        const area = computed(() =>
            store.state.dataKhuVuc
            .filter((item) => item.status == 1)
            .map((item) => ({
                text: item.name_area,
                value: item.id,
                tinh_trang: item.status,
            }))
        );
        const dataBan = computed(() => store.state.dataBan);
        const status = computed(() => store.state.tinh_trang);
        const addTable = ref({});
        const editTable = ref({});
        const deleteTable = ref({});

        const convertToSlug = (obj) => {
            obj.slug_table = store.getters.toSlug(obj.name_table);
        };

        const addNew = () => {
            axios
                .post("admin/ban/create", addTable.value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                        addTable.value = {};
                        store.dispatch("onFetchBan");
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
                .post("admin/ban/change-status", value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                        addTable.value = {};
                        store.dispatch("onFetchBan");
                    }
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast("error", v[0]);
                    });
                });
        };

        const updateBan = () => {
            axios
                .post("admin/ban/update", editTable.value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                        $("#edit_table").modal("hide");
                        addTable.value = {};
                        store.dispatch("onFetchBan");
                    }
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast("error", v[0]);
                    });
                });
        };

        const deleteBan = () => {
            axios
                .post("admin/ban/delete", deleteTable.value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                        $("#delete_table").modal("hide");
                        addTable.value = {};
                        store.dispatch("onFetchBan");
                    }
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast("error", v[0]);
                    });
                });
        };

        onMounted(() => {
            store.dispatch("onFetchKhuVuc");
            store.dispatch("onFetchBan");
        });
        return {
            area,
            status,
            dataBan,
            addTable,
            editTable,
            deleteTable,
            convertToSlug,
            addNew,
            changeStatus,
            updateBan,
            deleteBan
        };
    },
};
</script>

<style></style>