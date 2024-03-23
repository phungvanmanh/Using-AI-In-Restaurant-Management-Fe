<template>
<div class="row">
    <div class="col-xl-12 col-lg-12 col-md-12 col-sm-12">
        <CardComponent :required="false">
            <template #card-header>
                <div class="row d-flex justify-content-between align-items-center">
                    <div class="col">
                        <span><b>List of Admin</b></span>
                    </div>
                    <div class="col text-end">
                        <ModalComponent color="primary" label="Add news" titleId="add_news" sizeModal="modal-lg">
                            <template #modal-header>
                                <span><b>Add news admin</b></span>
                            </template>
                            <template #modal-body>
                                <div class="row d-flex justify-content-between align-items-center">
                                    <div class="col">
                                        <InputComponent v-model="
                                                    addAmin.first_last_name
                                                " label="First and last name" placeholder="Enter your first and last name..." />
                                    </div>
                                    <div class="col">
                                        <InputComponent v-model="addAmin.email" label="Email" type="email" placeholder="example@gmail.com" />
                                    </div>
                                </div>
                                <div class="row d-flex justify-content-between align-items-center">
                                    <div class="col">
                                        <InputComponent v-model="addAmin.phone_number" label="Phone number" placeholder="(999) 999-9999" />
                                    </div>
                                    <div class="col">
                                        <InputComponent v-model="addAmin.date_birth" label="Date of birth" type="date" />
                                    </div>
                                </div>
                                <InputComponent v-model="addAmin.password" label="Password" type="text" placeholder="Enter your password..." />
                                <div class="row d-flex justify-content-between align-items-center">
                                    <div class="col">
                                        <SelectComponent label="Permission" :options="dataQuyen" v-model="addAmin.id_permission" />
                                    </div>
                                    <div class="col">
                                        <SelectComponent label="Status" :options="status" v-model="addAmin.status" />
                                    </div>
                                </div>
                            </template>
                            <template #modal-footer>
                                <button class="btn btn-primary" @click="addNew()">
                                    Add new
                                </button>
                                <button class="btn btn-secondary" data-bs-dismiss="modal">
                                    Close
                                </button>
                            </template>
                        </ModalComponent>
                    </div>
                </div>
            </template>
            <template #card-body>
                <TableComponent>
                    <template #thead>
                        <tr>
                            <th class="text-center align-middle text-nowrap">
                                #
                            </th>
                            <th class="text-center align-middle text-nowrap">
                                First And Last Name
                            </th>
                            <th class="text-center align-middle text-nowrap">
                                Email
                            </th>
                            <th class="text-center align-middle text-nowrap">
                                Phone Number
                            </th>
                            <th class="text-center align-middle text-nowrap">
                                Date Birth
                            </th>
                            <th class="text-center align-middle text-nowrap">
                                Status
                            </th>
                            <th class="text-center align-middle text-nowrap">
                                Permission
                            </th>
                            <th class="text-center align-middle text-nowrap">
                                Action
                            </th>
                        </tr>
                    </template>
                    <template #tbody>
                        <template v-for="(value, key) in dataAdmin" :key="key">
                            <tr>
                                <th class="text-center align-middle text-nowrap">
                                    {{ key + 1 }}
                                </th>
                                <td class="align-middle text-nowrap">
                                    {{ value.first_last_name }}
                                </td>
                                <td class="align-middle text-nowrap">
                                    {{ value.email }}
                                </td>
                                <td class="text-center align-middle text-nowrap">
                                    {{ value.phone_number }}
                                </td>
                                <td class="text-center align-middle text-nowrap">
                                    {{ value.date_birth }}
                                </td>
                                <td class="align-middle text-nowrap">
                                    {{ value.name_permission }}
                                </td>
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
                                    Action
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
import TableComponent from "@/components/TableComponent.vue";
import ModalComponent from "@/components/ModalComponent.vue";
import {
    computed,
    onMounted,
    ref
} from "vue";
import {
    useStore
} from "vuex";
import axios from "@/axiosConfig";
import Toast from "@/toastConfig";
import $ from "jquery";
export default {
    name: "quan-ly-admin",
    components: {
        CardComponent,
        InputComponent,
        TableComponent,
        SelectComponent,
        ModalComponent,
    },
    setup() {
        const store = useStore();
        const addAmin = ref({});
        const status = computed(() => store.state.tinh_trang);
        const dataQuyen = computed(() =>
            store.state.dataQuyen
            .filter((item) => item.status == 1)
            .map((item) => ({
                text: item.name_permission,
                value: item.id,
                tinh_trang: item.status,
            }))
        );
        const dataAdmin = computed(() => store.state.dataAdmin.data);

        const DateFormat = (value) => {
            const dateString = value;

            // Tách các thành phần của ngày từ chuỗi
            const dateParts = dateString.split("-");
            const day = dateParts[0];
            const month = dateParts[1];
            const year = dateParts[2];

            // Tạo chuỗi định dạng "yyyy-mm-dd"
            const formattedDate = `${year}-${month}-${day}`;

            return formattedDate;
        }

        const addNew = () => {
            axios
                .post('admin/create', addAmin.value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast('success', res.data.message);
                        $("#add_news").modal("hide");
                        store.dispatch("onFetchQuyen");
                        store.dispatch("onFetchAdmin");
                        addAmin.value = {}
                    }
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast('error', v[0]);
                    });
                });
        }

        const changeStatus = (value) => {
            axios
                .post("admin/change-status", value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                        addAmin.value = {};
                        store.dispatch("onFetchQuyen");
                        store.dispatch("onFetchAdmin");
                    }
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast("error", v[0]);
                    });
                });
        };

        onMounted(() => {
            store.dispatch("onFetchQuyen");
            store.dispatch("onFetchAdmin");
        })

        return {
            status,
            dataQuyen,
            dataAdmin,
            addAmin,
            DateFormat,
            addNew,
            changeStatus
        };
    },
};
</script>

<style></style>
