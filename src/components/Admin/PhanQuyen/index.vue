<template>
    <div class="card">
        <div class="card-header d-flex justify-content-between align-items-center">
            <div class="col-3">
                <select class="form-select" v-model="selectedRole">
                    <option value="" disabled selected>Chọn chức vụ</option>
                    <option v-for="role in listChucVu" :key="role.id" :value="role">
                        {{ role.ten_chuc_vu }}
                    </option>
                </select>
            </div>
            <button class="btn btn-primary" @click="savePermissions">Lưu phân quyền</button>
        </div>

        <div class="card-body">
            <h5 class="mb-3">Phân quyền cho:  <span class="text-danger">{{ selectedRole?.ten_chuc_vu || 'Chưa chọn quyền' }}</span></h5>
            
            <div class="mb-3">
                <input type="text" 
                       class="form-control" 
                       v-model="searchQuery"
                       placeholder="Tìm kiếm chức năng...">
            </div>

            <div class="d-flex justify-content-end mb-3">
                <button class="btn btn-link me-2" @click="selectAll">Chọn tất cả</button>
                <button class="btn btn-link" @click="deselectAll">Bỏ chọn tất cả</button>
            </div>

            <div class="row g-3">
                <div v-for="feature in filteredFeatures" 
                     :key="feature.id" 
                     class="col-lg-4">
                    <div class="form-check">
                        <input class="form-check-input" 
                               type="checkbox"
                               v-model="selectedPermissions"
                               :value="feature.id"
                               :id="`feature-${feature.id}`">
                        <label class="form-check-label" :for="`feature-${feature.id}`">
                            {{ feature.ten_chuc_nang }}
                            <small class="text-muted">ID: {{ feature.id }}</small>
                        </label>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import baseRequest from '../../../core/baseRequest';
import { createToaster } from "@meforma/vue-toaster";
const toaster = createToaster({ position: "top-right" });

export default {
    data() {
        return {
            listChucNang: [],
            listChucVu: [],
            selectedRole: null,
            searchQuery: '',
            selectedPermissions: [],
        }
    },
    computed: {
        filteredFeatures() {
            if (!this.searchQuery) return this.listChucNang;
            const query = this.searchQuery.toLowerCase();
            return this.listChucNang.filter(feature => 
                feature.ten_chuc_nang.toLowerCase().includes(query)
            );
        }
    },
    mounted() {
        this.getData();
    },
    watch: {
        selectedRole: {
            handler(newRole) {
                if (newRole) {
                    this.getPermissionsForRole(newRole.id);
                }
            },
            immediate: true
        }
    },
    methods: {
        getData() {
            baseRequest.get('admin/phan-quyen/lay-du-lieu')
                .then((res) => {
                    this.listChucNang = res.data.listChucNang;
                    this.listChucVu = res.data.listChucVu;
                })
                .catch((error) => {
                    toaster.error('Không thể tải dữ liệu');
                });
        },

        getPermissionsForRole(roleId) {
            baseRequest.post('admin/phan-quyen/get-chuc-nang', { id: roleId })
                .then((res) => {
                    this.selectedPermissions = res.data.data.map(p => p.id_chuc_nang);
                })
                .catch((error) => {
                    toaster.error('Không thể tải quyền hiện tại');
                });
        },

        selectAll() {
            this.selectedPermissions = this.listChucNang.map(f => f.id);
        },

        deselectAll() {
            this.selectedPermissions = [];
        },

        savePermissions() {
            if (!this.selectedRole) {
                toaster.error('Vui lòng chọn chức vụ');
                return;
            }

            const payload = {
                id_chuc_vu: this.selectedRole.id,
                danh_sach_quyen: this.selectedPermissions,
            };

            baseRequest.post('admin/phan-quyen/save', payload)
                .then((res) => {
                    if (res.data.status) {
                        toaster.success('Lưu phân quyền thành công');
                    } else {
                        toaster.error(res.data.message);
                    }
                })
                .catch((error) => {
                    const message = error.response?.data?.errors 
                        ? Object.values(error.response.data.errors)[0]
                        : 'Có lỗi xảy ra khi lưu phân quyền';
                    toaster.error(message);
                });
        }
    },
}
</script>

<style scoped>
.card {
    margin-bottom: 1rem;
}

.form-check {
    padding: 1rem;
    border: 1px solid #dee2e6;
    border-radius: 0.25rem;
    margin-bottom: 0.5rem;
}

.form-check:hover {
    background-color: rgba(0,123,255,0.1);
}

.form-check-label {
    display: flex;
    flex-direction: column;
}

.form-check-label small {
    font-size: 0.75rem;
}
</style>
