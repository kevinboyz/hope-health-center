<template>
  <div class="main-container">
    <div class="appointment-header">
      <div class="date-selector">
        <label>Select Date:</label>
        <input type="date" v-model="selectedDate" @change="loadAppointments" class="date-input">
      </div>
      <button @click="showNewAppointmentForm = true" class="new-appointment-btn">New Appointment</button>
    </div>

    <div class="table-container">
      <table class="appointments-table">
        <thead>
          <tr>
            <th>Time</th>
            <th>Patient</th>
            <th>Doctor</th>
            <th>Status</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="appointment in appointments" :key="appointment.id">
            <td>{{ appointment.time }}</td>
            <td>{{ appointment.patient }}</td>
            <td>{{ appointment.doctor }}</td>
            <td>{{ appointment.status }}</td>
            <td>
              <button @click="editAppointment(appointment)" class="action-link edit-btn">Edit</button>
              <button @click="deleteAppointment(appointment.id)" class="action-link delete-btn">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="summary-box">
      <h3>Today's Summary</h3>
      <div class="summary-grid">
        <div class="summary-item">
          <span class="summary-label">Total:</span>
          <span class="summary-value">{{ summary.total }}</span>
        </div>
        <div class="summary-item">
          <span class="summary-label">Pending:</span>
          <span class="summary-value pending">{{ summary.pending }}</span>
        </div>
        <div class="summary-item">
          <span class="summary-label">Done:</span>
          <span class="summary-value done">{{ summary.done }}</span>
        </div>
      </div>
      <div class="summary-actions">
        <button @click="refreshAppointments" class="refresh-btn">Refresh</button>
        <button @click="exportAppointments" class="export-btn">Export</button>
      </div>
    </div>

    <div class="offline-status">
      Offline Mode ACTIVE
    </div>

    <!-- New Appointment Modal -->
    <div v-if="showNewAppointmentForm" class="modal-overlay">
      <div class="modal-content">
        <div class="modal-header">
          <h3>New Appointment</h3>
          <button @click="showNewAppointmentForm = false" class="close-btn">&times;</button>
        </div>
        <form @submit.prevent="addNewAppointment" class="appointment-form">
          <div class="form-grid">
            <div class="form-group">
              <label>Time:</label>
              <input type="time" v-model="newAppointment.time" required>
            </div>
            <div class="form-group">
              <label>Patient:</label>
              <input type="text" v-model="newAppointment.patient" placeholder="Patient name" required>
            </div>
            <div class="form-group">
              <label>Doctor:</label>
              <select v-model="newAppointment.doctor" required>
                <option value="">Select Doctor</option>
                <option value="Dr. Mac">Dr. Mac</option>
                <option value="Dr. Alice">Dr. Alice</option>
                <option value="Dr. Johnson">Dr. Johnson</option>
                <option value="Dr. Sarah">Dr. Sarah</option>
                <option value="Dr. Peter">Dr. Peter</option>
              </select>
            </div>
            <div class="form-group">
              <label>Status:</label>
              <select v-model="newAppointment.status" required>
                <option value="">Select Status</option>
                <option value="Pending">Pending</option>
                <option value="Done">Done</option>
                <option value="Cancelled">Cancelled</option>
              </select>
            </div>
          </div>
          <div class="form-actions">
            <button type="submit" class="save-btn">Save Appointment</button>
            <button type="button" @click="showNewAppointmentForm = false" class="cancel-btn">Cancel</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Appointment',
  data() {
    return {
      selectedDate: new Date().toISOString().split('T')[0],
      showNewAppointmentForm: false,
      newAppointment: {
        time: '',
        patient: '',
        doctor: '',
        status: 'Pending'
      },
      appointments: [
        { id: 1, time: '08:00', patient: 'Kevin', doctor: 'Dr.mac', status: 'Pending' },
        { id: 2, time: '09:30', patient: 'Marie', doctor: 'Dr. Alice', status: 'Done' },
        { id: 3, time: '11:00', patient: 'Eric', doctor: 'Dr.alice', status: 'Pending' }
      ]
    }
  },
  computed: {
    summary() {
      const total = this.appointments.length;
      const pending = this.appointments.filter(a => a.status === 'Pending').length;
      const done = this.appointments.filter(a => a.status === 'Done').length;
      return { total, pending, done };
    }
  },
  methods: {
    editAppointment(appointment) {
      const newTime = prompt('Edit time:', appointment.time);
      const newPatient = prompt('Edit patient:', appointment.patient);
      const newDoctor = prompt('Edit doctor:', appointment.doctor);
      const newStatus = prompt('Edit status (Pending/Done):', appointment.status);
      
      if (newTime && newPatient && newDoctor && newStatus) {
        const index = this.appointments.findIndex(a => a.id === appointment.id);
        this.appointments[index] = {
          ...appointment,
          time: newTime,
          patient: newPatient,
          doctor: newDoctor,
          status: newStatus
        };
        this.saveAppointments();
        alert('Appointment updated successfully!');
      }
    },
    deleteAppointment(appointmentId) {
      if (confirm('Are you sure you want to delete this appointment?')) {
        this.appointments = this.appointments.filter(a => a.id !== appointmentId);
        this.saveAppointments();
        alert('Appointment deleted successfully!');
      }
    },
    loadAppointments() {
      // Load appointments for selected date from localStorage
      const savedAppointments = JSON.parse(localStorage.getItem('appointments')) || [];
      const dateAppointments = savedAppointments.filter(a => a.date === this.selectedDate);
      if (dateAppointments.length > 0) {
        this.appointments = dateAppointments;
      }
    },
    saveAppointments() {
      // Save appointments to localStorage
      let allAppointments = JSON.parse(localStorage.getItem('appointments')) || [];
      
      // Remove existing appointments for this date
      allAppointments = allAppointments.filter(a => a.date !== this.selectedDate);
      
      // Add current appointments with date
      const appointmentsWithDate = this.appointments.map(a => ({ ...a, date: this.selectedDate }));
      allAppointments = [...allAppointments, ...appointmentsWithDate];
      
      localStorage.setItem('appointments', JSON.stringify(allAppointments));
    },
    refreshAppointments() {
      this.loadAppointments();
      alert('Appointments refreshed!');
    },
    exportAppointments() {
      const data = {
        date: this.selectedDate,
        appointments: this.appointments,
        summary: this.summary
      };
      const dataStr = JSON.stringify(data, null, 2);
      const dataUri = 'data:application/json;charset=utf-8,'+ encodeURIComponent(dataStr);
      const exportFileDefaultName = `appointments_${this.selectedDate}.json`;
      
      const linkElement = document.createElement('a');
      linkElement.setAttribute('href', dataUri);
      linkElement.setAttribute('download', exportFileDefaultName);
      linkElement.click();
    },
    addNewAppointment() {
      const appointment = {
        id: Date.now(),
        ...this.newAppointment,
        date: this.selectedDate
      };
      
      this.appointments.push(appointment);
      this.saveAppointments();
      this.showNewAppointmentForm = false;
      
      // Reset form
      this.newAppointment = {
        time: '',
        patient: '',
        doctor: '',
        status: 'Pending'
      };
      
      alert('New appointment added successfully!');
    }
  }
}
</script>

<style scoped>
.main-container {
  width: 100%;
}

.appointment-header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
  align-items: center;
}

.date-selector {
  background-color: #fff;
  padding: 10px 20px;
  border: 1px solid #000;
  width: 70%;
  font-weight: bold;
  display: flex;
  align-items: center;
  gap: 10px;
}

.date-selector label {
  margin-right: 10px;
}

.date-input {
  padding: 5px;
  border: 1px solid #999;
  background-color: #fff;
  font-weight: normal;
}

.new-appointment-btn {
  background-color: #5b9bd5;
  color: #000;
  padding: 10px 20px;
  border: 2px solid #000;
  font-weight: bold;
  cursor: pointer;
}

.table-container {
  background-color: #fff;
  border: 2px solid #000;
}

.appointments-table {
  width: 100%;
  border-collapse: collapse;
  background-color: #fff;
}

.appointments-table th {
  background-color: #bfbfbf;
  padding: 12px;
  border: 2px solid #000;
  text-align: left;
}

.appointments-table td {
  padding: 10px;
  border: 1px solid #000;
}

.action-link {
  background: none;
  border: none;
  text-decoration: underline;
  cursor: pointer;
  font-weight: bold;
  margin-right: 8px;
  padding: 2px 6px;
  border-radius: 3px;
}

.edit-btn {
  color: #0066cc;
  background-color: #e6f3ff;
}

.edit-btn:hover {
  background-color: #cce7ff;
}

.delete-btn {
  color: #dc3545;
  background-color: #f8d7da;
}

.delete-btn:hover {
  background-color: #f5c6cb;
}

.summary-box {
  margin-top: 30px;
  background-color: #d9d9d9;
  padding: 20px;
  border: 2px solid #000;
  width: 350px;
}

.summary-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 8px;
  margin-bottom: 15px;
}

.summary-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 5px 0;
}

.summary-label {
  font-weight: bold;
}

.summary-value {
  font-weight: bold;
  font-size: 16px;
}

.summary-value.pending {
  color: #ffc107;
}

.summary-value.done {
  color: #28a745;
}

.summary-actions {
  display: flex;
  gap: 10px;
  margin-top: 10px;
}

.refresh-btn, .export-btn {
  padding: 8px 15px;
  border: 1px solid #000;
  cursor: pointer;
  font-weight: bold;
  border-radius: 4px;
}

.refresh-btn {
  background-color: #17a2b8;
  color: white;
}

.refresh-btn:hover {
  background-color: #138496;
}

.export-btn {
  background-color: #6c757d;
  color: white;
}

.export-btn:hover {
  background-color: #5a6268;
}

.offline-status {
  margin-top: 30px;
  background-color: #a6a6a6;
  padding: 15px;
  border: 2px solid #000;
  font-weight: bold;
}

/* Modal Styles */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background-color: #fff;
  border: 2px solid #000;
  border-radius: 8px;
  width: 90%;
  max-width: 500px;
  max-height: 80vh;
  overflow-y: auto;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px 20px;
  border-bottom: 2px solid #000;
  background-color: #5b9bd5;
}

.modal-header h3 {
  margin: 0;
  color: #000;
}

.close-btn {
  background: none;
  border: none;
  font-size: 24px;
  cursor: pointer;
  color: #000;
  font-weight: bold;
}

.appointment-form {
  padding: 20px;
}

.form-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 15px;
  margin-bottom: 20px;
}

.form-group {
  display: flex;
  flex-direction: column;
}

.form-group label {
  font-weight: bold;
  margin-bottom: 5px;
  color: #000;
}

.form-group input, .form-group select {
  padding: 8px;
  border: 1px solid #000;
  background-color: #fff;
  font-size: 14px;
}

.form-actions {
  display: flex;
  gap: 10px;
  justify-content: flex-end;
}

.save-btn {
  background-color: #28a745;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
}

.save-btn:hover {
  background-color: #218838;
}

.cancel-btn {
  background-color: #6c757d;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
}

.cancel-btn:hover {
  background-color: #5a6268;
}
</style>
