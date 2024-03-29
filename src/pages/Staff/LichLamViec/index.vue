<template>
    <CardComponent :required="false">
        <template #card-header>
            <span><b>Calendar</b></span>
        </template>
        <template #card-body>
            <TableComponent>
                <template #thead>
                    <tr>
                        <th
                            class="text-center align-middle text-nowrap"
                            style="width: 150px"
                        ></th>
                        <template v-for="(v_d, i_d) in days" :key="i_d">
                            <td
                                :class="[
                                    'text-center align-middle text-nowrap',
                                    {
                                        'bg-secondary': isToday(v_d),
                                        'text-white': isToday(v_d),
                                    },
                                ]"
                            >
                                <span v-if="i_d == 6">{{ rank[6] }}</span>
                                <span v-else>{{ rank[i_d] }}</span> -
                                {{ date_format(v_d) }}
                            </td>
                        </template>
                    </tr>
                </template>
                <template #tbody>
                    <template
                        v-for="(value_time, index_time) in time"
                        :key="index_time"
                    >
                        <tr style="height: 300px">
                            <td class="align-middle text-center">
                                {{ value_time }}
                            </td>
                            <template
                                v-for="(value, index) in data"
                                :key="index"
                            >
                                <template v-for="(v, i) in value" :key="i">
                                    <template v-if="i == index_time">
                                        <template v-if="v.id">
                                            <td
                                                class="text-white bg-secondary"
                                                @click="updateBuoiLamViec(v.id)"
                                            >
                                                <div>
                                                    <input
                                                        type="radio"
                                                        class="float-end"
                                                        style="
                                                            width: 25px;
                                                            height: 25px;
                                                        "
                                                    />
                                                </div>
                                                <div>
                                                    <template
                                                        v-for="(
                                                            v_name, i_name
                                                        ) in v.list"
                                                        :key="i_name"
                                                    >
                                                        <span>
                                                            {{ v_name }}
                                                        </span>
                                                        <br />
                                                    </template>
                                                </div>
                                            </td>
                                        </template>
                                        <template v-else>
                                            <td
                                                class="align-middle"
                                                v-on:click="
                                                    themBuoiLamViec(
                                                        index_time,
                                                        index
                                                    )
                                                "
                                            ></td>
                                        </template>
                                    </template>
                                </template>
                            </template>
                        </tr>
                    </template>
                </template>
            </TableComponent>
        </template>
    </CardComponent>
</template>
<script>
import CardComponent from "@/components/CardComponent.vue";
import TableComponent from "@/components/TableComponent.vue";
import { onMounted, ref } from "vue";
import axios from "@/axiosConfig";
import moment from "moment";
import Toast from "@/toastConfig";
import $ from "jquery";

export default {
    name: "lich-lam-viec",
    components: {
        CardComponent,
        TableComponent,
    },
    setup() {
        const time = ref(["8h00 - 16h00", "17h00 - 22h00"]);
        const days = ref([]);
        const data = ref([]);
        const type = ref(-1);
        const today = ref(new Date());
        const nv_lam = ref([]);
        const rank = ref([
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday",
            "Saturday",
            "Sunday",
        ]);

        const list = ref([1, 2, 3, 4, 5, 6, 7]);

        function date_format(now) {
            return moment(now).format("DD/MM/yyyy");
        }

        const isToday = (date) => {
            // Chuyển đổi ngày hiện tại thành chuỗi ngày đúng định dạng
            const todayString = `${today.value.getFullYear()}-${(
                today.value.getMonth() + 1
            )
                .toString()
                .padStart(2, "0")}-${today.value
                .getDate()
                .toString()
                .padStart(2, "0")}`;

            // Chuyển đổi ngày truyền vào thành chuỗi ngày đúng định dạng
            const dateString =
                date instanceof Date
                    ? `${date.getFullYear()}-${(date.getMonth() + 1)
                          .toString()
                          .padStart(2, "0")}-${date
                          .getDate()
                          .toString()
                          .padStart(2, "0")}`
                    : date;
            return todayString === dateString;
        };

        const getDays = () => {
            axios
                .get("admin/lich-lam-viec/get-days/" + type.value)
                .then((res) => {
                    days.value = res.data.days;
                    data.value = res.data.data;
                    nv_lam.value = res.data.data;
                });
        };

        const themBuoiLamViec = (buoi_lam_viec, ngay_lam_viec) => {
            const payload = ref({
                buoi_lam_viec: buoi_lam_viec,
                ngay_lam_viec: ngay_lam_viec,
            });

            axios
                .post("admin/lich-lam-viec/dang-ky/store", payload.value)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                    } else {
                        Toast("error", res.data.message);
                    }
                    getDays();
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast("error", v[0]);
                    });
                });
        };

        const updateBuoiLamViec = (id) => {
            var payload = {
                id: id,
                id_user: 1, //sau lấy user đang login
            };

            axios
                .post("admin/lich-lam-viec/dang-ky/update", payload)
                .then((res) => {
                    if (res.data.status == 1) {
                        Toast("success", res.data.message);
                    } else {
                        Toast("error", res.data.message);
                    }
                    getDays();
                })
                .catch((res) => {
                    $.each(res.response.data.errors, function (k, v) {
                        Toast("error", v[0]);
                    });
                });
        };

        onMounted(() => {
            getDays();
        });
        return {
            time,
            days,
            data,
            today,
            rank,
            nv_lam,
            list,
            isToday,
            date_format,
            themBuoiLamViec,
            updateBuoiLamViec,
        };
    },
};
</script>
