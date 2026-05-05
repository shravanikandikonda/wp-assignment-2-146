<template>
  <div class="container mt-4">

    <!-- Page Title -->
    <h2 class="mb-4 text-center">Employee Management System</h2>

    <!-- ADD / EDIT FORM -->
    <div class="card mb-4 p-4 shadow-sm">
      <h5>{{ editMode ? 'Edit Employee' : 'Add Employee' }}</h5>
      <div class="row g-2">
        <div class="col-md-3">
          <input v-model="form.name" class="form-control"
            placeholder="Name" />
        </div>
        <div class="col-md-3">
          <input v-model="form.designation" class="form-control"
            placeholder="Designation" />
        </div>
        <div class="col-md-2">
          <input v-model="form.department" class="form-control"
            placeholder="Department" />
        </div>
        <div class="col-md-2">
          <input v-model.number="form.salary" class="form-control"
            placeholder="Salary" type="number" />
        </div>
        <div class="col-md-2 d-flex gap-2">
          <button class="btn btn-primary w-100"
            @click="saveEmployee">
            {{ editMode ? 'Update' : 'Add' }}
          </button>
          <button v-if="editMode" class="btn btn-secondary"
            @click="cancelEdit">✕</button>
        </div>
      </div>
    </div>

    <!-- EMPLOYEES TABLE -->
    <div class="card shadow-sm">
      <table class="table table-striped table-hover mb-0">
        <thead class="table-dark">
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Designation</th>
            <th>Department</th>
            <th>Salary</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="emp in employees" :key="emp.id">
            <td>{{ emp.id }}</td>
            <td>{{ emp.name }}</td>
            <td>{{ emp.designation }}</td>
            <td>{{ emp.department }}</td>
            <td>₹{{ emp.salary }}</td>
            <td>
              <button class="btn btn-warning btn-sm me-2"
                @click="editEmployee(emp)">Edit</button>
              <button class="btn btn-danger btn-sm"
                @click="deleteEmployee(emp.id)">Delete</button>
            </td>
          </tr>
          <tr v-if="employees.length === 0">
            <td colspan="6" class="text-center text-muted">
              No employees yet. Add one above!
            </td>
          </tr>
        </tbody>
      </table>
    </div>

  </div>
</template>
<script>
import axios from 'axios'

const API_URL = 'https://69eee9f99163f839f892fbaa.mockapi.io/api/Employees'

export default {
  name: 'EmployeeManager',

  data() {
    return {
      employees: [],
      editMode: false,
      editId: null,
      form: {
        name: '',
        designation: '',
        department: '',
        salary: ''
      }
    }
  },

  mounted() {
    this.fetchEmployees()
  },

  methods: {

    async fetchEmployees() {
      const response = await axios.get(API_URL)
      this.employees = response.data
    },

    async saveEmployee() {
  if (!this.form.name) {
    alert('Please enter a name!')
    return
  }
  try {
    const payload = {
      name: String(this.form.name),
      designation: String(this.form.designation),
      department: String(this.form.department),
      salary: Number(this.form.salary)
    }
    if (this.editMode) {
      // find and update locally
      const index = this.employees.findIndex(e => e.id === this.editId)
      if (index !== -1) {
        this.employees[index] = { ...this.employees[index], ...payload }
      }
      this.resetForm()
    } else {
      await axios.post(API_URL, payload)
      await this.fetchEmployees()
      this.resetForm()
    }
  } catch (error) {
    alert('Error: ' + JSON.stringify(error.response.data))
  }
},

    editEmployee(emp) {
      this.editMode = true
      this.editId = emp.id
      this.form = {
        name: emp.name,
        designation: emp.designation,
        department: emp.department,
        salary: emp.salary
      }
    },

    async deleteEmployee(id) {
      if (confirm('Delete this employee?')) {
        await axios.delete(`${API_URL}/${id}`)
        await this.fetchEmployees()
      }
    },

    cancelEdit() {
      this.editMode = false
      this.resetForm()
    },

    resetForm() {
      this.editMode = false
      this.editId = null
      this.form = { name: '', designation: '', department: '', salary: '' }
    }
  }
}
</script>