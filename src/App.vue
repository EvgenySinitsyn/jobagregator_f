<template>
  <div id="app">
    <header>
      <img src="@/assets/JobAgregator_logo.svg" alt="Jobagregator Logo" class="logo" />
      <nav>
        <span @click="changeComponent('resume')" :class="{ active: currentComponentName == 'resume' }">Резюме</span>
        <span @click="changeComponent('vacancies')"
          :class="{ active: currentComponentName == 'vacancies' }">Вакансии</span>
        <span @click="changeComponent('stat')" :class="{ active: currentComponentName == 'stat' }">Статистика</span>
        <span v-if="!logged" @click="changeComponent('login')" :class="{ active: currentComponentName == 'login' }">Вход
          / Регистрация</span>
        <span v-if="logged" @click="logout()" :class="{ active: currentComponentName == 'login' }">{{ logged.username }}
          / Выход</span>
        <a href="#">О проекте</a>
      </nav>
    </header>


    <component :is="currentComponent" @unauthorized="handleUnauthorized" @authorized="handleAuthorized" />
    <div class="fab" @click="toggleFabWindow" v-if="logged">
      <div class="fab-button">
        <i class="fas fa-comment"></i>
      </div>
    </div>

    <div v-if="logged" v-show="showFabWindow" class="fab-window">
      <WhatsappPage />
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
      currentComponentName: 'resume',
      currentComponent: ResumePage, // или любой другой компонент по умолчанию
      logged: null,
      axiosInstance,
      showFabWindow: false,
      isFabWindowLoaded: false,
    };
  },
  methods: {
    toggleFabWindow() {
      if (!this.isFabWindowLoaded) {
        this.isFabWindowLoaded = true;
      }
      this.showFabWindow = !this.showFabWindow;
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
        // Если запрос успешен и данные пользователя возвращены
        this.logged = response.data; // Сохраняем данные пользователя
      } catch (error) {
        if (error.response && error.response.status === 401) {
          // Если получен статус 401, устанавливаем logged в null
          this.logged = null;
        } else {
          // Обработка других ошибок (например, сетевых)
          console.error('Ошибка при получении данных пользователя:', error);
        }
      }
    },


    async checkUserStatus() {
      const url = '/users/me';
      try {
        const response = await axiosInstance.get(url);
        // Если запрос успешен и данные пользователя возвращены
        this.logged = response.data; // Сохраняем данные пользователя
      } catch (error) {
        if (error.response && error.response.status === 401) {
          // Если получен статус 401, устанавливаем logged в null
          this.logged = null;
        } else {
          // Обработка других ошибок (например, сетевых)
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
      this.logout_api();
      this.changeComponent('login');
    },
    handleUnauthorized() {
      this.changeComponent('login'); // Компонент, который отображается при 401
      this.logged = false;
    },
    handleAuthorized() {
      this.changeComponent('resume');
      this.checkUserStatus();
    }
  },
  mounted() {
    // Выполнение запроса при загрузке компонента
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
  /* Фиксируем заголовок */
  top: 0;
  /* Устанавливаем его в верхней части экрана */
  left: 0;
  /* Устанавливаем его в левом углу */
  right: 0;
  /* Устанавливаем его в правом углу */
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: rgba(30, 30, 30, 0.8);
  color: white;
  padding: 10px 20px;
  border-bottom: 2px solid #444;
  z-index: 1000;
  /* Устанавливаем высокий z-index, чтобы заголовок был поверх других элементов */
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
  bottom: 20px;
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
  bottom: 80px;
  right: 20px;
  background-color: rgb(51, 62, 53);
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
  padding: 20px;
  border-radius: 5px;
  max-width: 300px;
}

.fab-button i {
  font-size: 24px;
}
</style>
