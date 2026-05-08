<template>
  <div class="main-container">
    <div class="settings-card">
      <h2>{{ t('configurationPanel') }}</h2>
      
      <div class="settings-grid">
        <div class="full-row">
          <label>{{ t('clinicName') }}</label>
          <input type="text" v-model="settings.clinicName">
        </div>

        <div class="setting-item">
          <label>{{ t('theme') }}</label>
          <div class="theme-toggle">
            <button @click="toggleTheme" class="theme-btn" :class="settings.darkMode ? 'dark-active' : 'light-active'">
              {{ settings.darkMode ? '🌙 Dark' : '☀️ Light' }}
            </button>
          </div>
        </div>

        <div class="setting-item">
          <label>{{ t('language') }}</label>
          <select v-model="settings.language" @change="changeLanguage">
            <option value="en">English</option>
            <option value="rw">Kinyarwanda</option>
            <option value="fr">French</option>
            <option value="sw">Swahili</option>
            <option value="es">Spanish</option>
            <option value="pt">Portuguese</option>
            <option value="zh">Chinese</option>
            <option value="ar">Arabic</option>
          </select>
        </div>
        
        <div class="setting-item">
          <label>{{ t('printer') }}</label>
          <select v-model="settings.printer">
            <option value="default">Default Printer</option>
            <option value="hp">HP LaserJet 400</option>
            <option value="canon">Canon PIXMA</option>
            <option value="epson">Epson EcoTank</option>
            <option value="brother">Brother HL-L2350DW</option>
          </select>
        </div>

        <div class="setting-item">
          <label>{{ t('autoBackup') }}</label>
          <select v-model="settings.autoBackup">
            <option value="enable">{{ t('enable') }}</option>
            <option value="disable">{{ t('disable') }}</option>
          </select>
        </div>
        
        <div class="setting-item">
          <label>{{ t('offlineMode') }}</label>
          <select v-model="settings.offlineMode">
            <option value="active">{{ t('active') }}</option>
            <option value="inactive">{{ t('inactive') }}</option>
          </select>
        </div>

        <div class="setting-item">
          <label>{{ t('notifications') }}</label>
          <select v-model="settings.notifications">
            <option value="all">{{ t('all') }}</option>
            <option value="important">{{ t('important') }}</option>
            <option value="none">{{ t('none') }}</option>
          </select>
        </div>

        <div class="setting-item">
          <label>{{ t('dataSync') }}</label>
          <select v-model="settings.dataSync">
            <option value="realtime">{{ t('realtime') }}</option>
            <option value="hourly">{{ t('hourly') }}</option>
            <option value="daily">{{ t('daily') }}</option>
            <option value="manual">{{ t('manual') }}</option>
          </select>
        </div>

        <div class="setting-item">
          <label>{{ t('securityLevel') }}</label>
          <select v-model="settings.security">
            <option value="high">{{ t('high') }}</option>
            <option value="medium">{{ t('medium') }}</option>
            <option value="low">{{ t('low') }}</option>
          </select>
        </div>
      </div>

      <div class="action-buttons">
        <button @click="saveSettings" class="blue-btn">{{ t('saveSettings') }}</button>
        <button @click="resetSettings" class="blue-btn">{{ t('resetToDefault') }}</button>
        <button @click="exportSettings" class="blue-btn">{{ t('exportConfig') }}</button>
        <button @click="importSettings" class="blue-btn">{{ t('importConfig') }}</button>
      </div>
    </div>

    <div class="settings-footer">
      {{ t('systemReady') }}
    </div>
  </div>
</template>

<script>
import { t, getCurrentLanguage, setLanguage } from '../translations.js';

export default {
  name: 'Settings',
  data() {
    return {
      t: t,
      currentLang: getCurrentLanguage(),
      settings: {
        clinicName: 'Hope Health Center',
        language: 'en',
        printer: 'default',
        autoBackup: 'enable',
        offlineMode: 'active',
        darkMode: false,
        notifications: 'all',
        dataSync: 'realtime',
        security: 'medium'
      }
    }
  },
  mounted() {
    this.loadSettings();
    this.applyTheme();
    this.settings.language = this.currentLang;
  },
  methods: {
    loadSettings() {
      const savedSettings = localStorage.getItem('clinicSettings');
      if (savedSettings) {
        this.settings = JSON.parse(savedSettings);
      }
    },
    saveSettings() {
      localStorage.setItem('clinicSettings', JSON.stringify(this.settings));
      alert('Settings saved successfully!');
    },
    resetSettings() {
      this.settings = {
        clinicName: 'Hope Health Center',
        language: 'en',
        printer: 'default',
        autoBackup: 'enable',
        offlineMode: 'active'
      };
      alert('Settings reset to defaults!');
    },
    exportSettings() {
      const dataStr = JSON.stringify(this.settings, null, 2);
      const dataUri = 'data:application/json;charset=utf-8,'+ encodeURIComponent(dataStr);
      const exportFileDefaultName = 'clinic_settings.json';
      
      const linkElement = document.createElement('a');
      linkElement.setAttribute('href', dataUri);
      linkElement.setAttribute('download', exportFileDefaultName);
      linkElement.click();
    },
    importSettings() {
      const input = document.createElement('input');
      input.type = 'file';
      input.accept = '.json';
      input.onchange = (e) => {
        const file = e.target.files[0];
        const reader = new FileReader();
        reader.onload = (event) => {
          try {
            const importedSettings = JSON.parse(event.target.result);
            this.settings = { ...this.settings, ...importedSettings };
            this.saveSettings();
            this.applyTheme();
            alert('Settings imported successfully!');
          } catch (error) {
            alert('Invalid settings file!');
          }
        };
        reader.readAsText(file);
      };
      input.click();
    },
    toggleTheme() {
      this.settings.darkMode = !this.settings.darkMode;
      this.applyTheme();
      this.saveSettings();
    },
    applyTheme() {
      if (this.settings.darkMode) {
        document.body.classList.add('dark-mode');
      } else {
        document.body.classList.remove('dark-mode');
      }
    },
    changeLanguage() {
      // Apply language changes to all programs
      setLanguage(this.settings.language);
      this.currentLang = this.settings.language;
      this.updateUILanguage();
      this.saveSettings();
      
      // Emit event to notify other components
      this.$root.$emit('language-changed', this.settings.language);
      
      // Force re-render of all components
      this.$forceUpdate();
    },
    updateUILanguage() {
      // Update translation function reference
      this.t = t;
      
      // Update button texts and other UI elements
      console.log('Language changed to:', this.settings.language);
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

.settings-card {
  background-color: #a6a6a6;
  padding: 30px;
  border: 1px solid #000;
  width: 100%;
  max-width: 800px;
}

.settings-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  margin-top: 20px;
}

.full-row {
  grid-column: span 2;
}

.setting-item label, .full-row label {
  display: block;
  font-weight: bold;
  margin-bottom: 8px;
}

.setting-item select, .full-row input {
  width: 100%;
  padding: 12px;
  border: 1px solid #ccc;
  background: #fff;
  font-size: 16px;
}

.action-buttons {
  display: flex;
  gap: 30px;
  margin-top: 40px;
}

.blue-btn {
  width: 120px;
  height: 45px;
  background-color: #5b9bd5;
  border: 1px solid #000;
  border-radius: 8px;
  cursor: pointer;
  color: #000;
  font-weight: bold;
  font-size: 14px;
}

.blue-btn:hover {
  background-color: #2c5282;
}

.settings-footer {
  margin-top: 30px;
  background-color: #fff;
  padding: 15px;
  border: 1px solid #000;
  font-weight: bold;
  font-size: 1.2em;
  text-align: center;
  width: 100%;
  max-width: 800px;
}

/* Theme Toggle Styles */
.theme-toggle {
  display: flex;
  align-items: center;
}

.theme-btn {
  padding: 8px 16px;
  border: 2px solid #000;
  border-radius: 20px;
  cursor: pointer;
  font-weight: bold;
  transition: all 0.3s ease;
  background: linear-gradient(135deg, #f5f5f5, #e0e0e0);
}

.theme-btn:hover {
  transform: scale(1.05);
}

.dark-active {
  background: linear-gradient(135deg, #2c3e50, #1a1a1a);
  color: white;
}

.light-active {
  background: linear-gradient(135deg, #ffffff, #f0f0f0);
  color: black;
}

/* Dark Mode Styles */
:global(.dark-mode) {
  background-color: #1a1a1a;
  color: #ffffff;
}

:global(.dark-mode) .settings-card {
  background-color: #2c3e50;
  border-color: #444;
}

:global(.dark-mode) .setting-item select,
:global(.dark-mode) .full-row input {
  background-color: #3a4a5c;
  color: #ffffff;
  border-color: #555;
}

:global(.dark-mode) .settings-footer {
  background-color: #2c3e50;
  border-color: #444;
}
</style>
