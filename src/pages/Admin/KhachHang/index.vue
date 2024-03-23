<template>
  <div class="row">
    <div class="col-xl-3 col-lg-3 col-md-3 col-sm-12">
      <CardComponent>
        <template #card-header>
          <b>Thêm Mới Khách Hàng</b>
        </template>
        <template #card-body>
          <InputComponent
            v-model="addKhachHang.ten_khach_hang"
            label="Tên Khách hàng"
            placeholder="Nhập tên khách hàng..."
            type="text"
          >
          </InputComponent>
          <InputComponent
            v-model="addKhachHang.so_dien_thoai"
            label="Số điện thoại"
            placeholder="ex:0123456789"
            type="number"
          >
          </InputComponent>
          <InputComponent
            v-model="addKhachHang.email"
            type="email"
            label="Email"
            placeholder="Ex123@gmail.com"
          >
          </InputComponent>
        </template>
        <template #card-footer>
          <button class="btn btn-primary float-end" @click="addNew()">
            Add news
          </button>
        </template>
      </CardComponent>
    </div>
    <div class="col-xl col-lg-9 col-md-9 col-sm-12">
      <CardComponent :required="false">
        <template #card-header>
          <span>
            <b>Danh Sách</b>
          </span>
        </template>
        <template #card-body>
          <TableComponent>
            <template #thead>
              <tr>
                <th colspan="100%">
                  <div class="input-group mb-3">
                    <input
                      v-on:keyup.enter="searchKhachHang()"
                      v-model="key_search.keyword"
                      type="text"
                      class="form-control"
                      placeholder="Nhập thông tin cần tìm"
                    />
                    <button
                      v-on:click="searchKhachHang()"
                      class="btn btn-primary"
                    >
                      <i class="fa-solid fa-magnifying-glass"></i>
                    </button>
                  </div>
                </th>
              </tr>

              <tr>
                <th class="text-center align-middle text-nowrap">#</th>
                <th class="text-center align-middle text-nowrap">
                  Tên Khách Hàng
                </th>
                <th class="text-center align-middle text-nowrap">
                  Số Điện Thoại
                </th>
                <th class="text-center align-middle text-nowrap">Email</th>
                <th class="text-center align-middle text-nowrap">Action</th>
              </tr>
            </template>
            <template #tbody>
              <template v-for="(value, key) in dataKhachHang" :key="key">
                <tr>
                  <th class="text-center align-middle text-nowrap">
                    {{ key + 1 }}
                  </th>
                  <td class="align-middle text-nowrap">
                    {{ value.ten_khach_hang }}
                  </td>
                  <td class="align-middle text-nowrap">
                    {{ value.so_dien_thoai }}
                  </td>
                  <td class="align-middle text-nowrap">{{ value.email }}</td>
                  <td class="text-center align-middle text-nowrap">
                    <ModalComponent
                      color="success"
                      label="Edit Table"
                      titleId="edit_table"
                      @click="Object.assign(editKhachHang, value)"
                    >
                      <template #modal-header>
                        <span><b>Edit Table</b></span>
                      </template>
                      <template #modal-body>
                        <InputComponent
                          v-model="editKhachHang.ten_khach_hang"
                          label="Tên Khách hàng"
                          placeholder="Nhập tên khách hàng..."
                          type="text"
                        >
                        </InputComponent>
                        <InputComponent
                          v-model="editKhachHang.so_dien_thoai"
                          label="Số điện thoại"
                          placeholder="ex:0123456789"
                          type="number"
                        >
                        </InputComponent>
                        <InputComponent
                          v-model="editKhachHang.email"
                          type="email"
                          label="Email"
                          placeholder="Ex123@gmail.com"
                        >
                        </InputComponent>
                      </template>
                      <template #modal-footer>
                        <button
                          @click="updateKhachHang()"
                          class="btn btn-primary"
                        >
                          Update
                        </button>
                        <button
                          class="btn btn-secondary"
                          data-bs-dismiss="modal"
                        >
                          Close
                        </button>
                      </template>
                    </ModalComponent>
                    <ModalComponent
                      color="danger"
                      label="Delete Table"
                      titleId="delete_table"
                      @click="Object.assign(delete_khachhang, value)"
                    >
                      <template #modal-header>
                        <span><b>Xoá Khách Hàng</b></span>
                      </template>
                      <template #modal-body>
                        Are you sure you want to delete this
                        <span class="text-danger"
                          ><b
                            ><i>{{ delete_khachhang.ten_khach_hang }}</i></b
                          ></span
                        >
                        section?
                      </template>
                      <template #modal-footer>
                        <button
                          class="btn btn-danger"
                          @click="deleteKhachHang()"
                        >
                          Delete
                        </button>
                        <button
                          class="btn btn-secondary"
                          data-bs-dismiss="modal"
                        >
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
import CardComponent from "@/components/CardComponent";
import InputComponent from "@/components/InputComponent";
import TableComponent from "@/components/TableComponent";
import ModalComponent from "../../../components/ModalComponent.vue";
import { ref, computed, onMounted } from "vue";
import { useStore } from "vuex";
import axios from "@/axiosConfig";
import Toast from "@/toastConfig";
import $ from "jquery";

export default {
  name: "khach-hang-component",
  components: {
    CardComponent,
    InputComponent,
    TableComponent,
    ModalComponent,
  },
  setup() {
    const store = useStore();
    const khachhang = computed(() =>
      store.state.dataKhuVuc
        .filter((item) => item.status == 1)
        .map((item) => ({
          text: item.ten_khach_hang,
          value: item.id,
          tinh_trang: item.status,
        }))
    );
    const dataKhachHang = computed(() => store.state.dataKhachHang);
    const status = computed(() => store.state.tinh_trang);
    const addKhachHang = ref({});
    const editKhachHang = ref({});
    const delete_khachhang = ref({});
    const key_search = ref({});
    onMounted(() => {
      store.dispatch("onFetchKhachHang");
    });
    const addNew = () => {
      axios
        .post("admin/khach-hang/create", addKhachHang.value)
        .then((res) => {
          if (res.data.status == 1) {
            Toast("success", res.data.message);
            addKhachHang.value = {};
            store.dispatch("onFetchKhachHang");
          }
        })
        .catch((res) => {
          $.each(res.response.data.errors, function (k, v) {
            Toast("error", v[0]);
          });
        });
    };
    const updateKhachHang = () => {
      axios
        .post("admin/khach-hang/update", editKhachHang.value)
        .then((res) => {
          if (res.data.status == 1) {
            Toast("success", res.data.message);
            $("#edit_table").modal("hide");
            addNew.value = {};
            store.dispatch("onFetchKhachHang");
          }
        })
        .catch((res) => {
          $.each(res.response.data.errors, function (k, v) {
            Toast("error", v[0]);
          });
        });
    };
    const deleteKhachHang = () => {
      axios
        .post("admin/khach-hang/delete", delete_khachhang.value)
        .then((res) => {
          if (res.data.status == 1) {
            Toast("success", res.data.message);
            $("#delete_table").modal("hide");
            addNew.value = {};
            store.dispatch("onFetchKhachHang");
          }
        })
        .catch((res) => {
          $.each(res.response.data.errors, function (k, v) {
            Toast("error", v[0]);
          });
        });
    };
    const searchKhachHang = () => {
      axios.post("admin/khach-hang/search", key_search).then((res) => {
        if (res.data.status == 1) {
          store.dispatch("onFetchKhachHang");
        }
      });
    };
    console.log(key_search);

    return {
      khachhang,
      key_search,
      editKhachHang,
      dataKhachHang,
      delete_khachhang,
      status,
      addKhachHang,
      addNew,
      updateKhachHang,
      deleteKhachHang,
      searchKhachHang,
    };
  },
};
</script>

<style></style>
