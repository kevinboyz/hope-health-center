<template>
  <div class="dashboard-wrapper">
    <header class="main-header">
      <div class="logo-container">
        <!-- <img src="../../html-files/kevin.png" alt="Logo" class="logo"> -->
        <h1>HOPE HEALTH CENTER</h1>
      </div>
    </header>

    <div class="body-layout">
      <aside class="sidebar">
        <h3>Menu</h3>
        <ul>
          <li><router-link to="/" class="menu-link">{{ t('home') }}</router-link></li>
          <li><router-link to="/dashboard" class="menu-link">{{ t('dashboard') }}</router-link></li>
          <li><router-link to="/registration" class="menu-link">{{ t('patientRegistration') }}</router-link></li>
          <li><router-link to="/management" class="menu-link">{{ t('patientManagement') }}</router-link></li>
          <li><router-link to="/appointment" class="menu-link">{{ t('appointment') }}</router-link></li>
          <li><router-link to="/report" class="menu-link">{{ t('reports') }}</router-link></li>
          <li><router-link to="/settings" class="menu-link">{{ t('settings') }}</router-link></li>
        </ul>
      </aside>

      <main class="main-content">
        <router-view />
      </main>
    </div>
  </div>
</template>

<script>
import { t, getCurrentLanguage } from './translations.js';

export default {
  name: 'App',
  data() {
    return {
      t: t,
      currentLang: getCurrentLanguage()
    }
  },
  mounted() {
    // Listen for language changes
    this.$root.$on('language-changed', () => {
      this.currentLang = getCurrentLanguage();
      this.t = t;
      this.$forceUpdate();
    });
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: sans-serif;
}

body {
  background-color: #000;
  padding: 20px;
}

.dashboard-wrapper {
  border: 2px solid #333;
  display: flex;
  flex-direction: column;
}

.main-header {
  background-color: #2c5282;
  padding: 15px;
  text-align: center;
  border-bottom: 2px solid #000;
}

.logo-container {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 15px;
}

.logo {
  height: 50px;
  width: auto;
}

.body-layout {
  display: flex;
  min-height: 80vh;
}

.sidebar {
  width: 250px;
  background-color: #cccccc;
  padding: 20px;
  border-right: 2px solid #000;
}

.sidebar h3 {
  border-bottom: 2px solid #000;
  padding-bottom: 5px;
  margin-bottom: 15px;
}

.sidebar ul {
  list-style: none;
}

.sidebar li {
  padding: 12px 0;
  font-weight: bold;
  cursor: pointer;
}

.sidebar li:hover {
  background-color: #bbb;
}

.menu-link {
  text-decoration: none;
  color: #000;
  display: block;
  width: 100%;
}

.main-content {
  flex: 1;
  background-color: #f5f5f5;
  padding: 30px;
}

@media (max-width: 768px) {
  .body-layout {
    flex-direction: column;
  }
  
  .sidebar {
    width: 100%;
    border-right: none;
    border-bottom: 2px solid #000;
  }
}
</style>
