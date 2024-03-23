<template>
<div class="container-fulid">
    <div class="row mb-3">
        <div class="col-12 text-end">
            <button class="btn btn-outline-primary px-5 radius-30" data-bs-toggle="modal" data-bs-target="#themMoiModal"><b>Thêm Mới</b></button>
        </div>
    </div>
    <div class="row">
        <div class="card border-top border-0 border-4 border-primary">
            <div class="card-header">
                <h5>Danh Sách Nhà Cung Cấp</h5>
            </div>
            <div class="card-body">
                <div class="table-responsive">
                    <table class="table table-bordered">
                        <thead>
                            <tr>
                                <th colspan="100%">
                                    <div class="input-group mb-3"><input type="text" class="form-control" placeholder="Nhập thông tin cần tìm"><button class="btn btn-primary"><i class="fa-solid fa-magnifying-glass"></i></button></div>
                                </th>
                            </tr>
                            <tr>
                                <th class="text-center align-middle text-nowrap"> # </th>
                                <th class="text-center align-middle text-nowrap"> Mã Số Thuế </th>
                                <th class="text-center align-middle text-nowrap"> Tên Công Ty </th>
                                <th class="text-center align-middle text-nowrap"> Người Đại Diện </th>
                                <th class="text-center align-middle text-nowrap"> Số Điện Thoại </th>
                                <th class="text-center align-middle text-nowrap"> Email </th>
                                <th class="text-center align-middle text-nowrap"> Địa Chỉ </th>
                                <th class="text-center align-middle text-nowrap"> Tên Giợi Nhớ </th>
                                <th class="text-center align-middle text-nowrap"> Tình Trạng </th>
                                <th class="text-center align-middle text-nowrap"> Action </th>
                            </tr>
                        </thead>
                        <tbody>
                            <template v-for="(value,key) in dataNhaCungCap" :key="key">
                                <tr>
                                    <th class="text-center align-middle text-nowrap">{{key+1}}</th>
                                    <td class="text-center align-middle text-nowrap">{{value.ma_so_thue}}</td>
                                    <td class="text-center align-middle text-nowrap">{{value.ten_cong_ty}}</td>
                                    <td class="text-center align-middle text-nowrap">{{value.ten_nguoi_dai_dien}}</td>
                                    <td class="text-center align-middle text-nowrap">{{value.so_dien_thoai}}</td>
                                    <td class="text-center align-middle text-nowrap">{{value.email}}</td>
                                    <td class="text-center align-middle text-nowrap"><button v-on:click="dia_chi = value" type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#diaChiModal"><i class="fa-solid fa-circle-info"></i></button></td>
                                    <td class="text-center align-middle text-nowrap">{{value.ten_goi_nho}}</td>
                                    <td class="text-center align-middle text-nowrap">
                                    
                                        <button @:click="changeStatus(value)" v-if="value.tinh_trang == 1" class="btn btn-success">Hoạt Động</button>
                                        <button @:click="changeStatus(value)" v-else class="btn btn-warning">Tạm Dừng</button>
                                    </td>
                                    <td class="text-center align-middle text-nowrap"><button v-on:click="Object.assign(editNhacungcap,value)" class="btn btn-info" data-bs-toggle="modal" data-bs-target="#capNhatModal" style="width: 100px; margin-right: 4px;">Cập Nhật</button><button v-on:click="Object.assign(delete_nhacungcap,value)" class="btn btn-danger" data-bs-toggle="modal" data-bs-target="#xoaModal" style="width: 100px;">Xóa</button></td>
                                </tr>
                            </template>

                        </tbody>
                    </table>
                </div>
                <div class="modal fade" id="capNhatModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
                    <div class="modal-dialog modal-xl">
                        <div class="modal-content">
                            <div class="modal-header">
                                <h1 class="modal-title fs-5" id="exampleModalLabel">Cập Nhật Nhà Cung Cấp</h1><button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                            </div>
                            <div class="modal-body">
                                <div class="row">
                                    <div class="row">
                                        <div class="col-4"><label class="form-label">Mã Số Thuế</label><input v-model="editNhacungcap.ma_so_thue" type="text" class="form-control" placeholder="Nhập Mã Số Thuế"></div>
                                        <div class="col-4"><label class="form-label">Tên Công Ty</label><input  v-model="editNhacungcap.ten_cong_ty" type="text" class="form-control" placeholder="Nhập Tên Công Ty"></div>
                                        <div class="col-4"><label class="form-label">Tên Người Đại Diện</label><input  v-model="editNhacungcap.ten_nguoi_dai_dien" type="text" class="form-control" placeholder="Nhập Tên Người Đại Diện"></div>
                                    </div>
                                    <div class="row">
                                        <div class="col-4"><label class="form-label mt-3">Số Điện Thoại</label><input  v-model="editNhacungcap.so_dien_thoai" type="number" class="form-control" placeholder="Nhập Số Điện Thoại"></div>
                                        <div class="col-4"><label class="form-label mt-3">Email</label><input  v-model="editNhacungcap.email" type="email" class="form-control" placeholder="Nhập Email"></div>
                                        <div class="col-4"><label class="form-label mt-3">Địa Chỉ</label><input  v-model="editNhacungcap.dia_chi" type="email" class="form-control" placeholder="Nhập Địa Chỉ"></div>
                                    </div>
                                    <div class="row">
                                        <div class="col-6"><label class="form-label mt-3">Tên Gợi Nhớ</label><input  v-model="editNhacungcap.ten_goi_nho" type="text" class="form-control" placeholder="Nhập Tên Gợi Nhớ"></div>
                                        <div class="col-6"><label class="form-label mt-3">Tình Trạng</label><select  v-model="editNhacungcap.tinh_trang" class="form-control">
                                                <option value="0">Tạm Dừng</option>
                                                <option value="1">Hoạt Động</option>
                                            </select></div>
                                    </div>
                                </div>
                            </div>
                            <div class="modal-footer"><button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Thoát</button><button v-on:click="updateNhaCungCap()" type="button" data-bs-dismiss="modal" class="btn btn-primary">Cập Nhật</button></div>
                        </div>
                    </div>
                </div>
                <div class="modal fade" id="xoaModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
                    <div class="modal-dialog">
                        <div class="modal-content">
                            <div class="modal-header">
                                <h1 class="modal-title fs-5" id="exampleModalLabel">Xóa Nhà Cung Cấp</h1><button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                            </div>
                            <div class="modal-body">
                                <div class="alert alert-warning border-0 bg-warning alert-dismissible fade show py-2">
                                    <div class="d-flex align-items-center">
                                        <div class="font-35 text-dark"><i class="bx bx-info-circle"></i></div>
                                        <div class="ms-3">
                                            <h6 class="mb-0 text-dark">Warning</h6>
                                            <div class="text-dark">
                                                <p>Bạn có muốn xóa nhà cung cấp <b>{{ delete_nhacungcap.ten_goi_nho }}</b> này không?</p>
                                                <p><b>Lưu ý:</b> Điều này không thể hoàn tác! </p>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="modal-footer"><button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Thoát</button><button v-on:click="deleteNhaCungCap()" type="button" class="btn btn-danger" data-bs-dismiss="modal">Xóa</button></div>
                        </div>
                    </div>
                </div>
                <div class="modal fade" id="diaChiModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
                    <div class="modal-dialog">
                        <div class="modal-content">
                            <div class="modal-header">
                                <h5 class="modal-title" id="exampleModalLabel">Địa Chỉ Nhà Cung Cấp</h5><button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                            </div>
                            <div class="modal-body">
                                {{ dia_chi.dia_chi }}
                            </div>
                            <div class="modal-footer"><button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Thoát</button></div>
                        </div>
                    </div>
                </div>
                <div class="modal fade" id="themMoiModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
                    <div class="modal-dialog modal-xl">
                        <div class="modal-content">
                            <div class="modal-header">
                                <h1 class="modal-title fs-5" id="exampleModalLabel">Thêm mới nhà cung cấp</h1><button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                            </div>
                            <div class="modal-body">
                                <div class="row">
                                    <div class="row">
                                        <div class="col-4"><label class="form-label">Mã Số Thuế</label><input v-model="addNhaCungCap.ma_so_thue" type="text" class="form-control" placeholder="Nhập Mã Số Thuế"></div>
                                        <div class="col-4"><label class="form-label">Tên Công Ty</label><input v-model="addNhaCungCap.ten_cong_ty" type="text" class="form-control" placeholder="Nhập Tên Công Ty"></div>
                                        <div class="col-4"><label class="form-label">Tên Người Đại Diện</label><input v-model="addNhaCungCap.ten_nguoi_dai_dien" type="text" class="form-control" placeholder="Nhập Tên Người Đại Diện"></div>
                                    </div>
                                    <div class="row">
                                        <div class="col-4"><label class="form-label mt-3">Số Điện Thoại</label><input v-model="addNhaCungCap.so_dien_thoai" type="number" class="form-control" placeholder="Nhập Số Điện Thoại"></div>
                                        <div class="col-4"><label class="form-label mt-3">Email</label><input v-model="addNhaCungCap.email" type="email" class="form-control" placeholder="Nhập Email"></div>
                                        <div class="col-4"><label class="form-label mt-3">Địa Chỉ</label><input v-model="addNhaCungCap.dia_chi" type="email" class="form-control" placeholder="Nhập Địa Chỉ"></div>
                                    </div>
                                    <div class="row">
                                        <div class="col-6"><label class="form-label mt-3">Tên Gợi Nhớ</label><input v-model="addNhaCungCap.ten_goi_nho" type="text" class="form-control" placeholder="Nhập Tên Gợi Nhớ"></div>
                                        <div class="col-6"><label class="form-label mt-3">Tình Trạng</label><select v-model="addNhaCungCap.tinh_trang" class="form-control">
                                                <option value="0">Tạm Dừng</option>
                                                <option value="1">Hoạt Động</option>
                                            </select></div>
                                    </div>
                                </div>
                            </div>
                            <div class="modal-footer"><button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Thoát</button><button v-on:click="addNew()" type="button" data-bs-dismiss="modal" class="btn btn-primary">Thêm Mới</button></div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>

<script>
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
    name: "nha-cung-cap",
    setup() {
        const store = useStore();
        const status = computed(() => store.state.tinh_trang);
        const addNhaCungCap = ref({});
        const editNhacungcap = ref({});
        const delete_nhacungcap = ref({});
        // const editCotegory = ref({});
        // const deleteCotegory = ref({});
        const dia_chi = ref('');

        const dataNhaCungCap = computed(() => {
            return store.state.dataNhaCungCap;
        });

        const addNew = () => {
            axios
                .post("admin/nha-cung-cap/create", addNhaCungCap.value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                        addNhaCungCap.value = {};
                        store.dispatch("onFetchNhaCungCap");
                    }
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast("error", v[0]);
                    });
                });

        };
        const updateNhaCungCap = () => {
            axios
                .post("admin/nha-cung-cap/update", editNhacungcap.value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                        $("#edit_category").modal("hide");
                        editNhacungcap.value = {};
                        store.dispatch("onFetchNhaCungCap");
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
                .post("admin/nha-cung-cap/change-status", value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                        addNhaCungCap.value = {};
                        store.dispatch("onFetchNhaCungCap");
                    }
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast("error", v[0]);
                    });
                });
        };
        const deleteNhaCungCap = () => {
            axios
                .post("admin/nha-cung-cap/delete", delete_nhacungcap.value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                        $("#delete_category").modal("hide");
                        addNhaCungCap.value = {};
                        store.dispatch("onFetchNhaCungCap");
                    }
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast("error", v[0]);
                    });
                });
        };
        onMounted(() => {
            store.dispatch("onFetchNhaCungCap");
        });

        return {
            status,
            addNew,
            dataNhaCungCap,
            addNhaCungCap,
            dia_chi,
            updateNhaCungCap,
            editNhacungcap,
            changeStatus,
            delete_nhacungcap,
            deleteNhaCungCap,

        };
    },
}
</script>

<style >

</style>
