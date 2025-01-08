<template>

  <body>
    <header>
      <h1>Jobagregator</h1>
      <nav>
        <a href="#">Вход</a>
        <a href="#">Регистрация</a>
        <a href="#">О проекте</a>
      </nav>
    </header>

    <div class="container">
      <aside class="sidebar">
        <h2>Фильтры</h2>
        <label for="city">Город:</label>
        <input type="text" id="city" v-model="city" placeholder="Введите город">

        <label for="gender">Пол:</label>
        <select id="gender" v-model="gender">
          <option value="">Выберите пол</option>
          <option value="male">Мужской</option>
          <option value="female">Женский</option>
        </select>

        <label for="date">Дата:</label>
        <input type="date" id="date" v-model="create_tm">

        <label for="experienceFrom">Опыт (от):</label>
        <input type="number" id="experienceFrom" v-model="experienceFrom" min="0" max="120" placeholder="От (мес)">

        <label for="experienceTo">Опыт (до):</label>
        <input type="number" id="experienceTo" v-model="experienceTo" min="0" max="120" placeholder="До (мес)">

        <label for="position">Должность:</label>
        <input type="text" id="position" v-model="text" placeholder="Введите должность">

        <label for="education">Образование:</label>
        <select id="education" v-model="education">
          <option value="">Выберите образование</option>
          <option value="secondary">Среднее</option>
          <option value="higher">Высшее</option>
          <option value="not_important">Не имеет значения</option>
        </select>

        <label for="ageFrom">Возраст (от):</label>
        <input type="number" id="ageFrom" v-model="ageFrom" min="18" max="100" placeholder="От (лет)">

        <label for="ageTo">Возраст (до):</label>
        <input type="number" id="ageTo" v-model="ageTo" min="18" max="100" placeholder="До (лет)">

        <button class="button" @click="fetchResumes()">Найти</button>
        <div v-if="loading" class="loading-message">Загрузка...</div>
        <div v-if="error" class="text-danger">{{ error }}</div>
      </aside>

      <main class="main">
        
        <div class="resume" v-for="resume in resumes" :key="resume.id">

          <h3>{{ resume.profession.__data__.name }}</h3>
          <p><strong>Загружено:</strong> {{ formatDate(resume.platform_resume_tm_create) }}</p>
          <p><strong>Обновлено:</strong> {{ formatDate(resume.platform_resume_tm_update) }}</p>
          <p><strong>Город:</strong> {{ resume.city.__data__.name }}</p>
          <p v-if="resume.age"><strong>Возраст:</strong> {{ resume.age }}</p>
          <p><strong>Пол:</strong> {{ gender_dict[resume.sex] }}</p>
          <p v-if="resume.salary_from"><strong>Зарплатные ожидания от:</strong> {{ resume.salary_from }} {{
            resume.currency }}</p>
          <p><strong>Опыт работы (в месяцах):</strong> {{ resume.experience_months }}</p>
          <!-- <p><strong><a :href="resume.link" target="_blank">Ссылка на резюме</a></strong></p> -->
          <a :href="resume.link" target="_blank" class="platform-button">
            <img :src="platform_img_dict[resume.platform.__data__.name]" alt=""><span class="arrow-right"></span>
          </a>
        </div>
      </main>
    </div>

  </body>
</template>

<script>
import axios from 'axios';
import logo_superjob from '@/assets/logo_superjob.svg';
import logo_hh from '@/assets/logo_hh.svg';
import logo_sinitsa from '@/assets/logo_sinitsa.png';

export default {
  data() {
    return {
      gender_dict: {
        'male': 'Мужской',
        'female': 'Женский',
        null: 'Не указано'
      },
      platform_img_dict: {
        'Superjob': logo_superjob,
        'HH': logo_hh
      },
      logo_sinitsa_img: logo_sinitsa,
      city: null,
      gender: null,
      create_tm: null,
      experienceFrom: null,
      experienceTo: null,
      text: null,
      education: null,
      ageFrom: null,
      ageTo: null,
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

      const params = {
        city: this.city,
        gender: this.gender,
        create_tm: this.create_tm,
        experience_from: this.experienceFrom,
        experience_to: this.experienceTo,
        position: this.text,
        education: this.education,
        age_from: this.ageFrom,
        age_to: this.ageTo,
      };

      try {
        const response = await axios.get('http://localhost:8080/resumes', { params });
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

<style>
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  background: linear-gradient(to bottom, #000000, #ffffff);
  /* Черно-белый градиент */
  color: #ffffff;
  /* Белый текст */
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: rgba(30, 30, 30, 0.8);
  /* Полупрозрачный темно-серый фон для заголовка */
  color: white;
  padding: 10px 20px;
  border-bottom: 2px solid #444;
  /* Разделительная линия */
}

.main_logo {
    padding-left: 10%;
    height: 30px; /* Устанавливаем высоту изображения на 100% */
    width: auto; /* Сохраняем пропорции изображения */
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
  /* Темнее при наведении */
}

.container {
  display: flex;
  margin: 20px;
  padding-left: 12%;
  padding-right: 12%;
}

.sidebar {
  width: 250px;
  background-color: rgba(30, 30, 30, 0.9);
  /* Полупрозрачный темно-серый фон для бокового меню */
  padding: 20px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.5);
  border-radius: 5px;
  /* Скругленные углы для бокового меню */
}

.sidebar h2 {
  margin-top: 0;
  color: #ffffff;
  /* Белый цвет для заголовка бокового меню */
}

input,
select {
  width: calc(100% - 10px);
  /* Ширина полей ввода */
  padding: 8px;
  margin-bottom: 15px;
  border: 1px solid #444;
  /* Темная рамка */
  background-color: #333;
  /* Темный фон для полей ввода и селектов */
  color: #ffffff;
  /* Белый текст */
  box-sizing: border-box;
  /* Учитывать отступы и границы в ширине */
}

.button {
  background-color: #444;
  /* Темно-серая кнопка */
  color: white;
  border: none;
  padding: 10px;
  border-radius: 4px;
  /* Скругленные углы для кнопки */
  cursor: pointer;
  transition: background-color 0.3s;
  width: 100%;
}

.button:hover {
  background-color: #555;
  /* Темнее при наведении на кнопку */
}

.main {
  flex-grow: 1;
  padding: 20px;
  background-color: rgba(30, 30, 30, 0.9);
  /* Полупрозрачный темно-серый фон для основного блока */
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.5);
  margin-left: 20px;
  border-radius: 5px;
  /* Скругленные углы для основного блока */
}

.resume {
  background-color: #333;
  /* Темный фон для резюме */
  border-radius: 5px;
  /* Скругленные углы для резюме */
  padding: 15px;
  margin-bottom: 15px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.5);
  position: relative;
}

.platform-button {
  position: absolute;
  /* Абсолютное позиционирование для кнопки */
  bottom: 10px;
  /* Отступ сверху */
  right: 10px;
  /* Отступ справа */
  display: inline-flex;
  /* Используем flex для выравнивания */
  align-items: center;
  /* Центрируем элементы по вертикали */
  background-color: rgba(3, 94, 4, 0.514);
  /* Серый фон для кнопки */
  color: white;
  /* Белый цвет текста */
  padding: 10px 15px;
  /* Отступы внутри кнопки */
  border-radius: 5px;
  /* Скругленные углы для кнопки */
  text-decoration: none;
  /* Убираем подчеркивание у ссылки */
}

.arrow-right {
  display: inline-block;
  width: 0;
  height: 0;
  border-top: 5px solid transparent;
  /* Верхняя часть стрелки */
  border-bottom: 5px solid transparent;
  /* Нижняя часть стрелки */
  border-left: 8px solid rgb(82, 82, 82);
  /* Правая часть стрелки (зеленая) */
  margin-left: 5px;
  /* Отступ между текстом и стрелкой */
}

.platform-button:hover {
  transition: background-color 0.3s;
  background-color: rgba(3, 94, 4, 0.7); /* Светлее при наведении */
}
</style>