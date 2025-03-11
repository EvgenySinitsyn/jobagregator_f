<template>
  <div id="app">
    <header>
      <img src="@/assets/JobAgregator_logo.svg" alt="Jobagregator Logo" class="logo" />
      <nav>
          <span v-if="logged" @click="changeComponent('resume')" :class="{ active: currentComponentName == 'resume' }">Резюме</span>
          <span v-if="logged" @click="changeComponent('vacancies')"
            :class="{ active: currentComponentName == 'vacancies' }">Вакансии</span>
          <span v-if="logged" @click="changeComponent('stat')" :class="{ active: currentComponentName == 'stat' }">Статистика</span>

        <span v-if="!logged" @click="changeComponent('login')" :class="{ active: currentComponentName == 'login' }">Вход
          / Регистрация</span>
        <span v-if="logged" @click="logout()" :class="{ active: currentComponentName == 'login' }">{{ logged.username }}
          / Выход</span>
        <a href="#">О проекте</a>
      </nav>
    </header>


    <component :is="currentComponent" @unauthorized="handleUnauthorized" @authorized="handleAuthorized"
      @selectPhone="setSelectedPhone" />
    <div class="fab" @click="toggleFabWindow" v-if="logged && shosWhatsappButton">
      <div class="fab-button">
        <img src="./assets/whatsapp.png" alt="Button" />
      </div>
    </div>

    <div v-if="logged" v-show="showFabWindow" class="fab-window">
      <button @click="toggleFabWindow" class="close-button">
        &#10006;
      </button>
      <WhatsappPage ref="WhatsappPage" />
    </div>
  </div>
</template>

<script>
import ResumePage from './components/ResumePage.vue';
import VacancyPage from './components/VacancyPage.vue';
import StatPage from './components/StatPage.vue';
import LoginPage from './components/LoginRegister.vue';
import WhatsappPage from './components/WhatsappPage.vue';
import axiosInstance from './axiosInstance';

export default {
  components: {
    ResumePage,
    VacancyPage,
    StatPage,
    LoginPage,
    WhatsappPage,
  },
  data() {
    return {
      currentComponentName: null,
      currentComponent: ResumePage,
      logged: null,
      axiosInstance,
      showFabWindow: false,
      isFabWindowLoaded: false,
      shosWhatsappButton: true,
      messageSubscriberPhone: null,
    };
  },
  methods: {
    toggleFabWindow() {
      if (!this.isFabWindowLoaded) {
        this.isFabWindowLoaded = true;
      }
      this.showFabWindow = !this.showFabWindow;
      this.shosWhatsappButton = !this.shosWhatsappButton;
    },
    changeComponent(component) {
      switch (component) {
        case 'resume':
          this.currentComponentName = 'resume';
          this.currentComponent = ResumePage;
          break;
        case 'vacancies':
          this.currentComponentName = 'vacancies';
          this.currentComponent = VacancyPage;
          break;
        case 'stat':
          this.currentComponentName = 'stat';
          this.currentComponent = StatPage;
          break;
        case 'login':
          this.currentComponentName = 'login';
          this.currentComponent = LoginPage;
          break;
        case 'whatsapp':
          this.currentComponentName = 'whatsapp';
          this.currentComponent = WhatsappPage;
          break;
        default:
          this.currentComponent = null;
      }
    },
    async checkUserStatus() {
      const url = '/users/me';
      try {
        const response = await axiosInstance.get(url);
        this.logged = response.data;
      } catch (error) {
        if (error.response && error.response.status === 401) {
          this.logged = null;
          this.changeComponent('login');
        } else {
          console.error('Ошибка при получении данных пользователя:', error);
        }
      }
    },

    async logout_api() {
      const url = '/logout';
      try {
        await axiosInstance.get(url);
        this.logged = null;
        localStorage.removeItem('access_token');
      } catch (error) {
        if (error.response && error.response.status === 401) {
          this.logged = null;
        } else {
          console.error('Ошибка при получении данных пользователя:', error);
        }
      }
    },
    logout() {
      localStorage.removeItem('wa-connect');
      localStorage.removeItem('wa-logged');
      this.logout_api();
      this.changeComponent('login');

      console.log('logout')
    },
    handleUnauthorized() {
      this.changeComponent('login');
      this.logged = false;
    },
    handleAuthorized() {
      this.changeComponent('resume');
      this.checkUserStatus();
    },
    setSelectedPhone(phone) {
      console.log(phone);
      if (!this.showFabWindow) {
        this.toggleFabWindow()
      }
      this.$refs.WhatsappPage.setPhoneNumber(phone);
    },
  },
  mounted() {
    this.checkUserStatus();
  },

};
</script>

<style>
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  padding-top: 60px;
  min-height: 720px;
  background: linear-gradient(to bottom, #1b1b2c, #843021);
  color: #ffffff;
}

header {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: rgba(30, 30, 30, 0.8);
  color: white;
  padding: 10px 20px;
  border-bottom: 2px solid #444;
  z-index: 1000;
}



.main_logo {
  padding-left: 10%;
  height: 30px;
  width: auto;
}

header h1 {
  margin: 0;
}

nav a {
  color: white;
  text-decoration: none;
  margin-left: 15px;
  padding: 5px 10px;
  border-radius: 4px;
  transition: background-color 0.3s;
}

nav a:hover {
  background-color: #333;
}

nav a:active {
  background-color: #333;
}

nav span {
  color: white;
  text-decoration: none;
  margin-left: 15px;
  padding: 5px 10px;
  border-radius: 4px;
  transition: background-color 0.3s;
  cursor: pointer;
}

nav span:hover {
  background-color: #333;
}

nav span.active {
  background-color: #333;
}

.fab {
  position: fixed;
  top: 580px;
  right: 20px;
  z-index: 1000;
}

.fab-button {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background-color: green;
  display: flex;
  justify-content: center;
  align-items: center;
  color: white;
  cursor: pointer;
}

.fab-window {
  position: fixed;
  top: 325px;
  right: 5px;
  background-color: #0a0a0ac6;
  box-shadow: 0 2px 20px rgba(56, 211, 71, 0.175);
  padding: 5px;
  border-radius: 5px;
  max-width: 300px;
}

.fab-button i {
  font-size: 24px;
}

.close-button {
  position: absolute;
  top: -30px;
  right: 0px;
  z-index: 1000;
  background-color: red;
  color: white;
  border: none;
  padding: 1px 3px;
  cursor: pointer;
  font-size: 14px;
  border-radius: 4px;
}

.fab-button img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

body {
  margin: 0;
  font-family: Arial, sans-serif;
}

::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-thumb {
  background: rgba(0, 0, 0, 0.5);
  border-radius: 10px;
}

::-webkit-scrollbar-track {
  background: rgba(0, 0, 0, 0.1);
  border-radius: 10px;
}
</style>
