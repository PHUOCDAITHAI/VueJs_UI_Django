<template>
    <div>
        <button
            type="button" class="btn btn-primary m-2 float-end"
            data-bs-toggle="modal"
            data-bs-target="#exampleModal"
            @click="addClick()"
        >
            Add Employee
        </button>
        <table class="table table-striped">
            <thead>
                <tr>
                    <th>Employee Id</th>
                    <th>Employee Name</th>
                    <th>Department</th>
                    <th>DOJ</th>
                    <th>Options</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="employee in employees" :key="employee.EmployeeId">
                    <td>{{employee.EmployeeId}}</td>
                    <td>{{employee.EmployeeName}}</td>
                    <td>{{employee.Department}}</td>
                    <td>{{employee.DateOfJoining}}</td>
                    <td>
                        <button 
                            type="button" class="btn btn-light mr-1"
                            data-bs-toggle="modal"
                            data-bs-target="#exampleModal"
                            @click="editClick(employee)"
                        >
                            <i class="fa-solid fa-edit"></i>
                        </button> &nbsp;
                        <button 
                            type="button" class="btn btn-light mr-1"
                             @click="deleteClick(employee.EmployeeId)"
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
                        <div class="d-flex flex-row bd-highlight mb-3">
                            <div class="p-2 w-50 bg-highlight">
                                <div class="input-group mb-3">
                                    <span class="input-group-text">Name</span>
                                    <input type="text" class="form-control" v-model="EmployeeName">
                                </div>
                                <div class="input-group mb-3">
                                    <span class="input-group-text">Department</span>
                                    <select class="form-select" v-model="Department">
                                        <option v-for="department in departments" :key="department.DepartmentId">
                                            {{department.DepartmentName}}
                                        </option>
                                    </select>
                                </div>
                                 <div class="input-group mb-3">
                                    <span class="input-group-text">DOJ</span>
                                    <input type="date" class="form-control" v-model="DateOfJoining">
                                </div>
                            </div>
                            <div class="p-2 w-50 bd-highlight">
                                <img 
                                    width="250px" height="250px"
                                    :src="PhotoPath+PhotoFileName" alt=""
                                >
                                <input class="m-2" type="file" @change="imageUpload">
                            </div>
                        </div>
                        <button 
                            v-if="EmployeeId == 0" class="btn btn-primary" 
                            type="button" @click="createClick()"
                        >
                            Create
                        </button>
                        <button 
                            v-if="EmployeeId != 0" class="btn btn-primary" 
                            type="button" @click="updateClick()"
                        >
                            Update
                        </button>
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
            employees: [],
            modalTitle: '',
            EmployeeId: 0,
            EmployeeName: '',
            Department: '',
            DateOfJoining: '',
            PhotoFileName: 'user.jpg',
            PhotoPath: 'http://127.0.0.1:8000/Photos/'
        }
    },
    methods: {
        refreshData(){
            axios.get('http://127.0.0.1:8000/'+'employee')
            .then((res) => {
                this.employees = res.data
            })

             axios.get('http://127.0.0.1:8000/'+'department')
            .then((res) => {
                this.departments = res.data
            })
        },
        addClick(){
            this.modalTitle = 'Add Employee'
            this.EmployeeId=0;
            this.EmployeeName='';
            this.Department='';
            this.DateOfJoining='';
            this.PhotoFileName='user.jpg'

        },
        editClick(employee){
            this.modalTitle = 'Edit Employee'
            this.EmployeeId= employee.EmployeeId;
            this.EmployeeName = employee.EmployeeName;
            this.Department = employee.Department;
            this.DateOfJoining = employee.DateOfJoining;
            this.PhotoFileName= employee.PhotoFileName
        },
        createClick(){
            axios.post('http://127.0.0.1:8000/'+'employee', {
                EmployeeName: this.EmployeeName,
                Department: this.Department,
                DateOfJoining: this.DateOfJoining,
                PhotoFileName: this.PhotoFileName
            }).then((res) => {
                this.refreshData();
                alert(res.data)
            })
        },
        updateClick(){
            axios.put('http://127.0.0.1:8000/'+'employee', {
                EmployeeId: this.EmployeeId,
                EmployeeName: this.EmployeeName,
                Department: this.Department,
                DateOfJoining: this.DateOfJoining,
                PhotoFileName: this.PhotoFileName
            }).then((res) => {
                this.refreshData();
                alert(res.data)
            })
        },
        deleteClick(employeeId){
            if(!confirm('Are you sure?')){
                return;
            }
            axios.delete('http://127.0.0.1:8000/'+'employee/'+employeeId).then((res) => {
                this.refreshData();
                alert(res.data)
            })
        },
        imageUpload(event){
            let formData = new FormData();
            formData.append('file', event.target.files[0]);
            axios.post('http://127.0.0.1:8000/'+'employee/savefile', formData)
            .then((res) => {
                this.PhotoFileName = res.data
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