<template>
    <div>
        <button
            type="button" class="btn btn-primary m-2 float-end"
            data-bs-toggle="modal"
            data-bs-target="#exampleModal"
            @click="addClick()"
        >
            Add Department
        </button>
        <table class="table table-striped">
            <thead>
                <tr>
                    <th>
                        <div class="d-flex flex-row">
                            <input type="text" class="form-control m-2" 
                                v-model="DepartmentIdFilter"
                                @keyup="FilterFn()"
                                placeholder="Filter"
                            />
                            <button @click="sortResult('DepartmentId', true)" type="button" class="btn btn-light">
                                <i class="fa-solid fa-arrow-down"></i>
                            </button> &nbsp;
                            <button @click="sortResult('DepartmentId', false)" type="button" class="btn btn-light">
                                <i class="fa-solid fa-arrow-up"></i>
                            </button>
                        </div>
                        DepartmentId
                    </th>
                    <th>
                        <div class="d-flex flex-row">
                            <input type="text" class="form-control m-2" 
                                v-model="DepartmentNameFilter"
                                @keyup="FilterFn()"
                                placeholder="Filter"
                            />
                            <button @click="sortResult('DepartmentName', true)" type="button" class="btn btn-light">
                                <i class="fa-solid fa-arrow-down"></i>
                            </button> &nbsp;
                            <button @click="sortResult('DepartmentName', false)" type="button" class="btn btn-light">
                                <i class="fa-solid fa-arrow-up"></i>
                            </button>
                        </div>
                        DepartmentName
                    </th>
                    <th>Options</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="department in departments" :key="department.DepartmentId">
                    <td >{{department.DepartmentId}}</td>
                    <td>{{department.DepartmentName}}</td>
                    <td>
                        <button 
                            type="button" class="btn btn-light mr-1"
                            data-bs-toggle="modal"
                            data-bs-target="#exampleModal"
                            @click="editClick(department)"
                        >
                            <i class="fa-solid fa-edit"></i>
                        </button> &nbsp;
                        <button 
                            type="button" class="btn btn-light mr-1"
                             @click="deleteClick(department.DepartmentId)"
                        >
                            <i class="fa-solid fa-trash"></i>
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>

        <div class="modal fade" id="exampleModal" tabindex="-1"
            aria-labelledby="exampleModalLabel" aria-hidden="true"
        >
            <div class="modal-dialog modal-lg modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel">{{modalTitle}}</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <div class="input-group mb-3">
                            <span class="input-group-text">Department Name</span>
                            <input type="text" class="form-control" v-model="DepartmentName">
                            <button 
                                v-if="DepartmentId == 0" class="btn btn-primary" 
                                type="button" @click="createClick()"
                            >
                                Create
                            </button>
                            <button 
                                v-if="DepartmentId != 0" class="btn btn-primary" 
                                type="button" @click="updateClick()"
                            >
                                Update
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
import axios from 'axios'
export default {
    data(){
        return {
            departments: [],
            modalTitle: '',
            DepartmentName: '',
            DepartmentId: 0,
            DepartmentIdFilter: '',
            DepartmentNameFilter: '',
            departmentsWithoutFilter: []
        }
    },
    methods: {
        refreshData(){
            axios.get('http://127.0.0.1:8000/'+'department')
            .then((res) => {
                this.departments = res.data
                this.departmentsWithoutFilter = res.data
            })
        },
        addClick(){
            this.modalTitle = 'Add Department'
            this.DepartmentId=0;
            this.DepartmentName=''
        },
        editClick(department){
            this.modalTitle = 'Edit Department'
            this.DepartmentId= department.DepartmentId;
            this.DepartmentName= department.DepartmentName
        },
        createClick(){
            axios.post('http://127.0.0.1:8000/'+'department', {
                DepartmentName: this.DepartmentName,
            }).then((res) => {
                this.refreshData();
                alert(res.data)
            })
        },
        updateClick(){
            axios.put('http://127.0.0.1:8000/'+'department', {
                DepartmentId: this.DepartmentId,
                DepartmentName: this.DepartmentName
            }).then((res) => {
                this.refreshData();
                alert(res.data)
            })
        },
        deleteClick(departmentId){
            if(!confirm('Are you sure?')){
                return;
            }
            axios.delete('http://127.0.0.1:8000/'+'department/'+departmentId).then((res) => {
                this.refreshData();
                alert(res.data)
            })
        },
        FilterFn(){
            var DepartmentIdFilter = this.DepartmentIdFilter
            var DepartmentNameFilter = this.DepartmentNameFilter
            this.departments = this.departmentsWithoutFilter.filter((item) => {
                return item.DepartmentId.toString().toLowerCase().includes(
                    DepartmentIdFilter.toString().trim().toLowerCase()
                ) &&
                item.DepartmentName.toString().toLowerCase().includes(
                    DepartmentNameFilter.toString().trim().toLowerCase()
                )
            })
        },
        sortResult(prop, asc){
            this.departments=this.departmentsWithoutFilter.sort((a,b) => {
                if(asc){
                    return (a[prop]>b[prop]) ? 1 : ((a[prop]<b[prop]) ? -1 : 0)
                }else {
                    return (b[prop]>a[prop]) ? 1 : ((b[prop]<a[prop]) ? -1 : 0)
                }
            })
        }
    },
    mounted(){
        this.refreshData()
    }
}
</script>

<style>

</style>