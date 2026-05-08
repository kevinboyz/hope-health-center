<template>
  <div class="main-container">
    <div class="registration-container">
      <h2>{{ t('patientRegistration') }}</h2>
      
      <form @submit.prevent="savePatient" class="patient-form">
        <div class="form-grid">
          <!-- Personal Information Section -->
          <div class="form-section">
            <h4>{{ t('personalInfo') || 'Personal Information' }}</h4>
            <div class="form-row">
              <div class="form-group">
                <label>{{ t('fullName') }}:</label>
                <input type="text" v-model="patient.fullName" placeholder="Enter full name" required>
              </div>
              
              <div class="form-group">
                <label>{{ t('age') }}:</label>
                <input type="number" v-model="patient.age" placeholder="Enter age" required>
              </div>
            </div>
            
            <div class="form-row">
              <div class="form-group">
                <label>{{ t('gender') }}:</label>
                <select v-model="patient.gender" required>
                  <option value="">{{ t('selectGender') }}</option>
                  <option value="male">{{ t('male') }}</option>
                  <option value="female">{{ t('female') }}</option>
                </select>
              </div>
              
              <div class="form-group">
                <label>{{ t('email') }}:</label>
                <input type="email" v-model="patient.email" placeholder="Enter email" required>
              </div>
            </div>
          </div>

          <!-- Contact Information Section -->
          <div class="form-section">
            <h4>{{ t('contactInfo') || 'Contact Information' }}</h4>
            <div class="form-row">
              <div class="form-group full-width">
                <label>{{ t('phoneNumber') }}:</label>
                <div class="phone-input-group">
                  <select v-model="patient.phoneCountry" required>
                    <option value="">{{ t('selectCountry') }}</option>
                    <option value="+250">Rwanda (+250)</option>
                    <option value="+254">Kenya (+254)</option>
                    <option value="+255">Tanzania (+255)</option>
                    <option value="+256">Uganda (+256)</option>
                    <option value="+243">DRC (+243)</option>
                    <option value="+257">Burundi (+257)</option>
                    <option value="+237">Cameroon (+237)</option>
                    <option value="+233">Ghana (+233)</option>
                    <option value="+234">Nigeria (+234)</option>
                    <option value="+27">South Africa (+27)</option>
                    <option value="+44">UK (+44)</option>
                    <option value="+1">USA (+1)</option>
                    <option value="+33">France (+33)</option>
                    <option value="+49">Germany (+49)</option>
                    <option value="+86">China (+86)</option>
                    <option value="+91">India (+91)</option>
                    <option value="+81">Japan (+81)</option>
                    <option value="+82">South Korea (+82)</option>
                    <option value="+61">Australia (+61)</option>
                    <option value="+55">Brazil (+55)</option>
                    <option value="+52">Mexico (+52)</option>
                    <option value="+7">Russia (+7)</option>
                  </select>
                  <input type="tel" v-model="patient.phoneNumber" placeholder="Phone number" required>
                </div>
              </div>
            </div>
            
            <div class="form-row">
              <div class="form-group full-width">
                <label>{{ t('nationalId') }}:</label>
                <div class="id-input-group">
                  <select v-model="patient.idCountry" required>
                    <option value="">{{ t('selectCountry') }}</option>
                    <option value="RW">Rwanda</option>
                    <option value="KE">Kenya</option>
                    <option value="TZ">Tanzania</option>
                    <option value="UG">Uganda</option>
                    <option value="CD">DRC</option>
                    <option value="BI">Burundi</option>
                    <option value="CM">Cameroon</option>
                    <option value="GH">Ghana</option>
                    <option value="NG">Nigeria</option>
                    <option value="ZA">South Africa</option>
                    <option value="GB">UK</option>
                    <option value="US">USA</option>
                    <option value="FR">France</option>
                    <option value="DE">Germany</option>
                    <option value="CN">China</option>
                    <option value="IN">India</option>
                    <option value="JP">Japan</option>
                    <option value="KR">South Korea</option>
                    <option value="AU">Australia</option>
                    <option value="BR">Brazil</option>
                    <option value="MX">Mexico</option>
                    <option value="RU">Russia</option>
                  </select>
                  <input type="text" v-model="patient.nationalId" placeholder="National ID number" required>
                </div>
              </div>
            </div>
            
            <div class="form-row">
              <div class="form-group full-width">
                <label>{{ t('address') }}:</label>
                <input type="text" v-model="patient.address" placeholder="Enter address" required>
              </div>
            </div>
            
            <div class="form-row">
              <div class="form-group full-width">
                <label>{{ t('emergencyContact') }}:</label>
                <input type="tel" v-model="patient.emergencyContact" placeholder="Emergency contact number" required>
              </div>
            </div>
          </div>

          <!-- Medical Information Section -->
          <div class="form-section">
            <h4>{{ t('medicalInfo') || 'Medical Information' }}</h4>
            <div class="form-row">
              <div class="form-group full-width">
                <label>{{ t('medicalHistory') }}:</label>
                <textarea v-model="patient.medicalHistory" placeholder="Enter medical history" rows="4" required></textarea>
              </div>
            </div>
          </div>
        </div>

        <div class="form-buttons">
          <button type="submit" class="btn-save">{{ t('save') }}</button>
          <button type="button" @click="clearForm" class="btn-clear">{{ t('clear') }}</button>
          <button type="button" @click="window.print()" class="btn-print">{{ t('printCard') }}</button>
        </div>
      </form>
    </div>

    <div class="status-footer">
      <button @click="savePatient" class="btn-register">
        {{ t('readyToRegisterPatient') }}
      </button>
    </div>
  </div>
</template>

<script>
import { t, getCurrentLanguage } from '../translations.js';

export default {
  name: 'Registration',
  data() {
    return {
      t: t,
      currentLang: getCurrentLanguage(),
      patient: {
        fullName: '',
        age: '',
        gender: '',
        phoneCountry: '',
        phoneNumber: '',
        idCountry: '',
        nationalId: '',
        address: '',
        email: '',
        emergencyContact: '',
        medicalHistory: ''
      }
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
    savePatient() {
      const patients = JSON.parse(localStorage.getItem('patients')) || [];
      patients.push({
        ...this.patient,
        id: Date.now(),
        registeredAt: new Date().toISOString()
      });
      localStorage.setItem('patients', JSON.stringify(patients));
      
      alert('Patient registered successfully!');
      this.clearForm();
    },
    clearForm() {
      this.patient = {
        fullName: '',
        age: '',
        gender: '',
        phoneNumber: '',
        phoneCountry: '',
        nationalId: '',
        idCountry: '',
        address: ''
      };
    }
  }
}
</script>

<style scoped>
.main-container {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.form-card {
  background-color: #a6a6a6;
  padding: 30px;
  border: 1px solid #000;
  width: 100%;
  max-width: 800px;
}

.form-grid {
  display: flex;
  flex-direction: column;
  gap: 30px;
}

.form-section {
  background-color: #f8f9fa;
  border: 1px solid #dee2e6;
  border-radius: 8px;
  padding: 20px;
  margin-bottom: 20px;
}

.form-section h4 {
  color: #495057;
  font-size: 1.1em;
  font-weight: bold;
  margin-bottom: 15px;
  padding-bottom: 8px;
  border-bottom: 2px solid #007bff;
}

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  margin-bottom: 20px;
}

.form-group {
  display: flex;
  flex-direction: column;
}

.form-group.full-width {
  grid-column: span 2;
}

.form-group label {
  font-weight: bold;
  margin-bottom: 5px;
  color: #495057;
}

.form-group input,
.form-group select,
.form-group textarea {
  padding: 10px;
  border: 1px solid #ced4da;
  border-radius: 4px;
  font-size: 14px;
  transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
}

.form-group input:focus,
.form-group select:focus,
.form-group textarea:focus {
  border-color: #007bff;
  outline: 0;
  box-shadow: 0 0 0 0.2rem rgba(0, 123, 255, 0.25);
}

.phone-input-group, .id-input-group {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
}

.phone-input-group select, .id-input-group select {
  flex: 0 0 auto;
  width: 180px;
}

.phone-input-group input, .id-input-group input {
  flex: 1;
}

.full-width {
  margin-top: 10px;
}

.full-width textarea {
  width: 100%;
}

.form-buttons {
  display: flex;
  gap: 20px;
  margin-top: 25px;
}

.btn-save {
  background-color: #1a5276;
  color: #ffff00;
  padding: 10px 25px;
  border-radius: 8px;
  cursor: pointer;
}

.btn-clear {
  background-color: #000;
  color: #00ff00;
  padding: 10px 25px;
  border-radius: 8px;
  cursor: pointer;
}

.btn-print {
  background-color: #4b6239;
  color: #000;
  padding: 10px 25px;
  border-radius: 8px;
  cursor: pointer;
}

.status-footer {
  background-color: #a6a6a6;
  padding: 20px;
  margin-top: 20px;
  border: 1px solid #000;
  font-weight: bold;
  font-size: 1.1em;
  text-align: center;
  width: 100%;
  max-width: 800px;
}

.btn-register {
  background-color: #28a745;
  color: white;
  padding: 15px 30px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1.1em;
  font-weight: bold;
  transition: background-color 0.3s ease;
  width: 100%;
}

.btn-register:hover {
  background-color: #218838;
}

.btn-register:active {
  background-color: #1e7e34;
  transform: translateY(1px);
}
</style>
