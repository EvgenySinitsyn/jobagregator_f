<template>
  <div id="app">
    <header>
      <h1>Jobagregator</h1>
      <nav>
        <span @click="changeComponent('resume')" :class="{ active: currentComponentName == 'resume' }">Резюме</span>
        <span @click="changeComponent('vacancies')" :class="{ active: currentComponentName == 'vacancies' }">Вакансии</span>
        <span @click="changeComponent('stat')" :class="{ active: currentComponentName == 'stat' }">Статистика</span>
        <a href="#">Вход</a>
        <a href="#">Регистрация</a>
        <a href="#">О проекте</a>
      </nav>
    </header>

    <main>
      <component :is="currentComponent"></component>
    </main>
  </div>
</template>

<script>
import ResumePage from './components/ResumePage.vue';
import VacancyPage from './components/VacancyPage.vue';
import StatPage from './components/StatPage.vue';

export default {
  data() {
  return {
    currentComponentName: 'resume',
    currentComponent: ResumePage // или любой другой компонент по умолчанию
  };
},
methods: {
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
      default:
        this.currentComponent = null;
    }
  }
}
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
  position: fixed; /* Фиксируем заголовок */
  top: 0; /* Устанавливаем его в верхней части экрана */
  left: 0; /* Устанавливаем его в левом углу */
  right: 0; /* Устанавливаем его в правом углу */
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: rgba(30, 30, 30, 0.8);
  color: white;
  padding: 10px 20px;
  border-bottom: 2px solid #444;
  z-index: 1000; /* Устанавливаем высокий z-index, чтобы заголовок был поверх других элементов */
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
</style>

