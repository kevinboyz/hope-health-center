<template>
  <div class="container">
    <header class="header">
      <div class="logo-box">
        <!-- <img src="../../html-files/kevin.png" alt="Logo" class="logo"> -->
        <h1>Hope Health Center Dashboard – Offline System</h1>
      </div>
    </header>

    <div class="main-body">
      <main class="content">
        <div class="stats-container">
          <button @click="showTodayPatients" class="card red-card button-card">
            <h4>{{ t('todayPatients') }}</h4>
            <p class="stat-number">{{ todayPatients }}</p>
            <div class="patient-names">
              <span v-for="patient in todayPatientsList.slice(0, 3)" :key="patient.id" class="patient-name">{{ patient.name }}</span>
            </div>
          </button>
          <button @click="showAllPatients" class="card green-card button-card">
            <h4>{{ t('totalPatients') }}</h4>
            <p class="stat-number">{{ totalPatients }}</p>
            <div class="patient-names">
              <span v-for="patient in allPatientsList.slice(0, 3)" :key="patient.id" class="patient-name">{{ patient.name }}</span>
            </div>
          </button>
          <button @click="showAppointments" class="card blue-card button-card">
            <h4>{{ t('appointmentsToday') }}</h4>
            <p class="stat-number">{{ appointmentsToday }}</p>
            <div class="patient-names">
              <span v-for="apt in appointmentsList.slice(0, 3)" :key="apt.id" class="patient-name">{{ apt.patient }}</span>
            </div>
          </button>
        </div>

        <section class="recent-patients">
          <h3>{{ t('recentPatients') }}</h3>
          <ul>
            <li v-for="patient in recentPatients" :key="patient.id">{{ patient.name }} {{ patient.action }}</li>
          </ul>
        </section>

        <footer class="status-bar">
          {{ t('offlineModeActive') }}
        </footer>
      </main>
    </div>
  </div>
</template>

<script>
import { t, getCurrentLanguage } from '../translations.js';

export default {
  name: 'Dashboard',
  data() {
    return {
      t: t,
      currentLang: getCurrentLanguage(),
      todayPatients: 12,
      totalPatients: 103,
      appointmentsToday: 8,
      pendingAppointments: 3,
      todayPatientsList: [
        { id: 1, name: 'Kevin', time: '08:00' },
        { id: 2, name: 'Marie', time: '09:30' },
        { id: 3, name: 'Eric', time: '11:00' },
        { id: 4, name: 'Sarah', time: '14:00' },
        { id: 5, name: 'John', time: '15:30' }
      ],
      allPatientsList: [
        { id: 1, name: 'Kevin', age: 25 },
        { id: 2, name: 'Marie', age: 30 },
        { id: 3, name: 'Eric', age: 35 },
        { id: 4, name: 'Sarah', age: 28 },
        { id: 5, name: 'John', age: 40 }
      ],
      appointmentsList: [
        { id: 1, patient: 'Kevin', doctor: 'Dr. Mac', time: '08:00', status: 'Pending' },
        { id: 2, patient: 'Marie', doctor: 'Dr. Alice', time: '09:30', status: 'Done' },
        { id: 3, patient: 'Eric', doctor: 'Dr. Alice', time: '11:00', status: 'Pending' }
      ],
      recentPatients: [
        { id: 1, name: 'Kevin', action: 'checked in' },
        { id: 2, name: 'Bonheur', action: 'added' },
        { id: 3, name: 'Appointment', action: 'scheduled' }
      ]
    }
  },
  mounted() {
    // Listen for language changes
    this.$root.$on('language-changed', () => {
      this.currentLang = getCurrentLanguage();
      this.t = t;
      this.$forceUpdate();
    });
  },
  methods: {
    showTodayPatients() {
      const patientNames = this.todayPatientsList.map(p => `${p.name} (${p.time})`).join('\n');
      alert(`${this.t('todayPatients')}:\n${patientNames}`);
    },
    showAllPatients() {
      const patientNames = this.allPatientsList.map(p => `${p.name} (${p.age} years)`).join('\n');
      alert(`${this.t('totalPatients')}:\n${patientNames}`);
    },
    showAppointments() {
      const appointments = this.appointmentsList.map(a => `${a.patient} - ${a.doctor} (${a.time}) - ${a.status}`).join('\n');
      alert(`${this.t('appointmentsToday')}:\n${appointments}`);
    }
  }
}
</script>

<style scoped>
.container {
  border: 2px solid #444;
  min-height: 95vh;
  display: flex;
  flex-direction: column;
}

.header {
  background: #2c5282;
  padding: 15px;
  border-bottom: 2px solid #000;
}

.logo-box {
  display: flex;
  align-items: center;
  gap: 15px;
  color: #000;
}

.logo {
  height: 50px;
  width: auto;
}

.main-body {
  display: flex;
  flex: 1;
  background: #000;
}

.content {
  flex: 1;
  background: #000;
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.stats-container {
  display: flex;
  gap: 20px;
}

.card {
  flex: 1;
  padding: 20px;
  border: 1px solid #999;
  height: 120px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.button-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.3);
}

.button-card:active {
  transform: translateY(0);
}

.patient-names {
  display: flex;
  flex-direction: column;
  gap: 4px;
  margin-top: 8px;
}

.patient-name {
  font-size: 12px;
  font-weight: normal;
  opacity: 0.9;
}

.red-card {
  background: #c00000;
  color: #000;
}

.green-card {
  background: #a9d08e;
  color: #000;
}

.blue-card {
  background: #5b9bd5;
  color: #000;
}

.stat-number {
  font-size: 24px;
  font-weight: bold;
  margin-top: 10px;
}

.recent-patients {
  background: #ccc;
  padding: 20px;
  min-height: 200px;
  border: 1px solid #999;
}

.recent-patients h3 {
  text-align: center;
  margin-bottom: 15px;
}

.recent-patients ul {
  margin-left: 40px;
}

.recent-patients li {
  margin-bottom: 8px;
  font-weight: bold;
}

.status-bar {
  background: #a5a5a5;
  padding: 15px;
  font-weight: bold;
  font-size: 18px;
  border: 1px solid #000;
  margin-top: auto;
}
</style>
