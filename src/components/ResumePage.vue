<template>

    <div class="container">
      <aside class="sidebar">
        <label for="position">Должность:</label>
        <input type="text" id="position" v-model="text" placeholder="Введите должность">

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

        <button class="button" @click="clearFetchResumes()">
          <span v-if="!loading">Найти</span>
          <span v-else class="loader"></span>
        </button>

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
          <p v-if="resume.experience_months"><strong>Опыт работы (в месяцах):</strong> {{ resume.experience_months }}
          </p>
          <a :href="resume.link" target="_blank" class="platform-button">
            <img :src="platform_img_dict[resume.platform.__data__.name]" alt=""><span class="arrow-right"></span>
          </a>
        </div>
        <div v-if="error" class="text-danger">{{ error }}</div>
        <button v-if="resumes" class="button" @click="loadMore()">
                <span v-if="!loading">Найти далее</span>
                <span v-else class="loader"></span>
            </button>
      </main>
    </div>

</template>

<script>
import axiosInstance from '../axiosInstance';
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
      currentPage: 0,
      axiosInstance
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

    async clearFetchResumes() {
      this.resumes = [];
      window.scrollTo(0, 0);
      this.currentPage = 0;
      this.fetchResumes(this.currentPage);
    },

    async fetchResumes(page = 1) {
      this.loading = true;
      this.error = null;
      const url = '/resumes';
      const params = {
        ...(this.city && this.city.trim() && { city: this.city }),
        ...(this.gender && this.gender.trim() && { gender: this.gender }),
        ...(this.create_tm && this.create_tm.trim() && { create_tm: this.create_tm }),
        ...(this.experienceFrom !== null && this.experienceFrom !== '' && { experience_from: this.experienceFrom }),
        ...(this.experienceTo !== null && this.experienceTo !== '' && { experience_to: this.experienceTo }),
        ...(this.text && this.text.trim() && { position: this.text }),
        ...(this.education && this.education.trim() && { education: this.education }),
        ...(this.ageFrom !== null && this.ageFrom !== '' && { age_from: this.ageFrom }),
        ...(this.ageTo !== null && this.ageTo !== '' && { age_to: this.ageTo }),
        page,
      };

      try {
        const response = await axiosInstance.get(url, { params });
        this.resumes = [...this.resumes, ...response.data];
      } catch (err) {
        console.error('Ошибка при получении данных:', err);
        if (err.response) {
          if (err.response.status === 401) {
            this.$emit('unauthorized');
          }
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
    loadMore() {
      this.currentPage++;
      if (!this.loading) {
        this.fetchResumes(this.currentPage);
      }
    },
  },
  mounted() {
    // window.addEventListener('scroll', this.handleScroll);
    // this.fetchResumes();

  },
  beforeDestroy() {
    window.removeEventListener('scroll', this.handleScroll);
  },
};

</script>

<style scoped>
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  min-height: 720px;
  background: linear-gradient(to bottom, #1b1b2c, #843021);
  color: #ffffff;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: rgba(30, 30, 30, 0.8);
  color: white;
  padding: 10px 20px;
  border-bottom: 2px solid #444;
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

.container {
  display: flex;
  margin: 20px;
  padding-left: 12%;
  padding-right: 12%;
}

.sidebar {
  position: fixed;
  top: 79px;
  left: 10%;
  width: 250px;
  background-color: rgba(30, 30, 30, 0.9);
  padding: 20px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.5);
  border-radius: 5px;
  z-index: 1000;
}


input,
select {
  width: calc(100% - 10px);
  padding: 8px;
  margin-bottom: 7px;
  border: 1px solid #444;
  background-color: #333;
  color: #ffffff;
  box-sizing: border-box;
}

.button {
  background-color: #444;
  color: white;
  border: none;
  margin-top: 7px;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
  width: 97%;
  height: 40px;
  display: inline-block;
}

.button:hover {
  background-color: #555;
}

.main {
  flex-grow: 1;
  padding: 20px;
  background-color: rgba(30, 30, 30, 0.9);
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.5);
  margin-left: 260px;
  border-radius: 5px;
}

.resume {
  background-color: #333;
  border-radius: 5px;
  padding: 15px;
  margin-bottom: 15px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.5);
  position: relative;
}

.platform-button {
  position: absolute;
  bottom: 10px;
  right: 10px;
  display: inline-flex;
  align-items: center;
  background-color: rgba(3, 94, 4, 0.514);
  color: white;
  padding: 10px 15px;
  border-radius: 5px;
  text-decoration: none;
}

.arrow-right {
  display: inline-block;
  width: 0;
  height: 0;
  border-top: 5px solid transparent;
  border-bottom: 5px solid transparent;
  border-left: 8px solid rgb(82, 82, 82);
  margin-left: 5px;
}

.platform-button:hover {
  transition: background-color 0.3s;
  background-color: rgba(3, 94, 4, 0.7);
}

.loader {
  border: 3px solid #f3f3f3;
  border-top: 3px solid #3498db;
  border-radius: 50%;
  width: 20px;
  height: 20px;
  animation: spin 0.8s linear infinite;
  display: inline-block;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}
</style>
