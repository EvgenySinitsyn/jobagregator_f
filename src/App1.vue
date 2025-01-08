<template>
<header>
    <h1>Логотип</h1>
    <nav>
        <a href="#">Вход</a>
        <a href="#">Регистрация</a>
        <a href="#">О проекте</a>
    </nav>
</header>



  <div class="container">
        <div class="sidebar">
            <button class="btn" @click="fetchResumes()">Получить резюме</button>
            <div v-if="loading" class="loading-message">Загрузка...</div>
            <div v-if="error" class="text-danger">{{ error }}</div>
        </div>
        <div class="content" ref="content">
            <h1>Резюме</h1>
            <div class="resume" v-for="resume in resumes">
                <h2>{{ resume.profession.__data__.name }}</h2>
                <p><strong>Загружено:</strong> {{ formatDate(resume.platform_resume_tm_create) }}</p>
                <p><strong>Обновлено:</strong> {{ formatDate(resume.platform_resume_tm_update) }}</p>
                <p><strong>Платформа:</strong> {{ resume.platform.__data__.name }}</p>
                <p><strong>Город:</strong> {{ resume.city.__data__.name }}</p>
                <p v-if="resume.age"><strong>Возраст:</strong> {{ resume.age }}</p>
                <p><strong>Пол:</strong> {{ resume.sex }}</p>
                <p v-if="resume.salary_from"><strong>Зарплатные ожидания от:</strong> {{ resume.salary_from }} {{ resume.currency }}</p>
                <p><strong>Опыт работы (в месяцах):</strong> {{ resume.experience_months }}</p>
                <p><strong><a :href="resume.link" target="_blank">Ссылка на резюме</a></strong></p>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      resumes: [],
      loading: false,
      error: null,
    };
  },
  methods: {
  formatDate(date) {
    if (!date) return '';
    return new Date(date).toLocaleString('ru-RU', {
      year: 'numeric',
      month: 'long',
      day: 'numeric',
      hour: '2-digit',
      minute: '2-digit',
      second: '2-digit'
    });
  },

  async fetchResumes() {
    this.loading = true;
    this.error = null;

    try {
      const response = await axios.get('http://localhost:8080/resumes');
      this.resumes = response.data;
    } catch (err) {
      console.error('Ошибка при получении данных:', err);
      if (err.response) {
        this.error = 'Ошибка: ' + err.response.status + ' ' + err.response.data;
      } else if (err.request) {
        this.error = 'Нет ответа от сервера. Проверьте соединение.';
      } else {
        this.error = 'Ошибка: ' + err.message;
      }
    } finally {
      this.loading = false;
    }
  },
  },
};

</script>

<style scoped>
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
}

.container {
    display: flex;
    height: 100vh;
}

.sidebar {
    width: 250px;
    background-color: #333;
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
}

.btn {
    padding: 15px 20px;
    background-color: #007bff;
    border: none;
    color: white;
    font-size: 16px;
    cursor: pointer;
    border-radius: 5px;
}

.btn:hover {
    background-color: #0056b3;
}

.content {
    flex-grow: 1;
    padding: 20px;
    background-color: #fff;
}

h1 {
    margin-bottom: 20px;
}

.resume {
    background-color: #f9f9f9;
    border: 1px solid #ddd;
    border-radius: 5px;
    padding: 15px;
    margin-bottom: 20px;
}

.resume h2 {
    margin-bottom: 10px;
}

.loading-message {
    margin-top: 10px;
    color: #007bff;
}
</style>