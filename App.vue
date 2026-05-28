<template>
  <div class="container">
    <div class="card">

      <!-- HEADER -->
      <div class="header">
        <h1>💼 Job Application Dashboard</h1>
        <p>Total Applications: {{ applications.length }}</p>
      </div>

      <!-- TABS -->
      <div class="tabs">
        <button @click="currentTab = 'form'" :class="{ active: currentTab === 'form' }">
          Applicant Form
        </button>

        <button @click="currentTab = 'dashboard'" :class="{ active: currentTab === 'dashboard' }">
          Admin Dashboard
        </button>
      </div>

      <!-- SUCCESS MESSAGE -->
      <div v-if="successMessage" class="success">
        {{ successMessage }}
      </div>

      <!-- FORM -->
      <div v-if="currentTab === 'form'" class="form-section">

        <form @submit.prevent="submitApplication">

          <div class="grid">

            <input v-model="form.firstname" placeholder="First Name" />
            <input v-model="form.surname" placeholder="Surname" />
            <input type="date" v-model="form.dob" />

            <textarea v-model="form.address" placeholder="Address"></textarea>

            <input type="tel" v-model="form.phone" placeholder="Phone Number" />
            <input type="text" v-model="form.nationalId" placeholder="National ID" />

            <div class="gender">
              <label><input type="radio" value="Male" v-model="form.gender" /> Male</label>
              <label><input type="radio" value="Female" v-model="form.gender" /> Female</label>
              <label><input type="radio" value="Other" v-model="form.gender" /> Other</label>
            </div>

          </div>

          <button class="btn">Submit Application</button>

        </form>

      </div>

      <!-- DASHBOARD -->
      <div v-if="currentTab === 'dashboard'">

        <div v-if="applications.length === 0" class="empty">
          No applicants yet
        </div>

        <table v-else>
          <thead>
            <tr>
              <th>#</th>
              <th>First Name</th>
              <th>Surname</th>
              <th>DOB</th>
              <th>Phone</th>
              <th>Gender</th>
              <th>National ID</th>
              <th>Actions</th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="(app, i) in applications" :key="i">
              <td>{{ i + 1 }}</td>
              <td>{{ app.firstname }}</td>
              <td>{{ app.surname }}</td>
              <td>{{ app.dob }}</td>
              <td>{{ app.phone }}</td>
              <td>{{ app.gender }}</td>
              <td>{{ app.nationalId }}</td>

              <td>
                <button @click="deleteApp(i)">🗑</button>
                <button @click="editApp(i)">✏️</button>
              </td>
            </tr>
          </tbody>

        </table>

      </div>

    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue'

const currentTab = ref('form')
const successMessage = ref('')
const applications = ref([])

const form = reactive({
  firstname: '',
  surname: '',
  dob: '',
  address: '',
  phone: '',
  gender: '',
  nationalId: ''
})

/* LOAD LOCAL STORAGE */
onMounted(() => {
  const saved = localStorage.getItem('applications')
  if (saved) applications.value = JSON.parse(saved)
})

/* SAVE */
function saveData() {
  localStorage.setItem('applications', JSON.stringify(applications.value))
}

/* SUBMIT */
function submitApplication() {

  if (
    !form.firstname ||
    !form.surname ||
    !form.dob ||
    !form.address ||
    !form.phone ||
    !form.gender ||
    !form.nationalId
  ) {
    alert("Fill all fields")
    return
  }

  applications.value.push({ ...form })
  saveData()

  successMessage.value = "Application submitted successfully ✅"

  Object.keys(form).forEach(key => form[key] = '')

  setTimeout(() => successMessage.value = '', 3000)
}

/* DELETE */
function deleteApp(index) {
  applications.value.splice(index, 1)
  saveData()
}

/* EDIT */
function editApp(index) {
  const app = applications.value[index]

  Object.assign(form, app)

  currentTab.value = 'form'

  applications.value.splice(index, 1)
  saveData()
}
</script>

<style scoped>
.container{
  padding:30px;
  background:#667eea;
  min-height:100vh;
}

.card{
  background:white;
  max-width:1000px;
  margin:auto;
  border-radius:12px;
  padding:20px;
}

.header{
  background:#667eea;
  color:white;
  padding:20px;
  border-radius:10px;
}

.tabs{
  display:flex;
  margin-top:15px;
}

.tabs button{
  flex:1;
  padding:12px;
  border:none;
  cursor:pointer;
}

.tabs .active{
  background:#667eea;
  color:white;
}

.grid{
  display:grid;
  grid-template-columns:repeat(2,1fr);
  gap:10px;
  margin-top:20px;
}

input,textarea{
  padding:10px;
  border:1px solid #ccc;
}

.gender{
  grid-column:span 2;
  display:flex;
  gap:20px;
}

.btn{
  margin-top:20px;
  width:100%;
  padding:12px;
  background:#667eea;
  color:white;
  border:none;
}

.success{
  background:#d4edda;
  padding:10px;
  margin-top:10px;
}

.empty{
  padding:20px;
  text-align:center;
}

table{
  width:100%;
  margin-top:20px;
  border-collapse:collapse;
}

th,td{
  border:1px solid #ddd;
  padding:10px;
}

button{
  margin:2px;
}
</style>