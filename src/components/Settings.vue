<template>
  <div class="settings">
    <h1>Настройки</h1>
    <div class="language">
      <span>Основной язык</span>
      <select v-model="primaryLanguage">
        <option value="ru">Русский</option>
        <option value="ee">Estonian</option>
        <option value="en">English</option>
      </select>
    </div>

    <div class="actions">
      <button @click="selectAll">Выбр. все</button>
      <button @click="deselectAll">Убр. все</button>
      <button class="flag"
              @click="selectEnglish"
      >
        <EnglishFlag />
      </button>
      <button class="flag"
              @click="selectEstonian"
      >
        <EstonianFlag />
      </button>
    </div>
    <div class="lessons">
      <Checkbox
          v-for="lesson in allLessons"
          :key="lesson.lesson"
          :lesson="lesson"
          :label="lesson.lesson"
          v-model="selectedLessonsObj[lesson.lesson]"
      />
    </div>
    <div class="actions">
      <button @click="apply">Сохранить</button>
    </div>
  </div>
</template>

<script>
import Checkbox from './Checkbox.vue'
import EnglishFlag from "./icons/EnglishFlag.vue";
import EstonianFlag from "./icons/EstonianFlag.vue";
import {mapActions, mapState} from "pinia";
import {useIndexStore} from "../store/index.js";

export default {
  name: 'Settings',
  components: {
    EstonianFlag,
    EnglishFlag,
    Checkbox,
  },
  // props: ['lessons'],
  data() {
    return {
      selectedLessonsObj: {},
      primaryLanguage: null
    };
  },
  computed: {
    ...mapState(useIndexStore, {
      allLessons: 'allLessons',
      selectedLessons: 'selectedLessons',
      language: 'language',
    })
  },
  mounted() {
    this.primaryLanguage = this.language
    this.allLessons.forEach(lesson => {
      this.selectedLessonsObj[lesson.lesson] = this.selectedLessons.includes(lesson.lesson);
    });
  },
  methods: {
    ...mapActions(useIndexStore, {
      updateSettings: 'updateSettings',
    }),
    selectAll() {
      // Set all lessons to selected
      for (let lesson in this.selectedLessonsObj) {
        this.selectedLessonsObj[lesson] = true;
      }
    },
    deselectAll() {
      // Set all lessons to deselected
      for (let lesson in this.selectedLessonsObj) {
        this.selectedLessonsObj[lesson] = false;
      }
    },
    selectEnglish() {
      this.allLessons.forEach(lesson => {
        this.selectedLessonsObj[lesson.lesson] = lesson.language === 'en'
      })
    },
    selectEstonian() {
      this.allLessons.forEach(lesson => {
        this.selectedLessonsObj[lesson.lesson] = lesson.language === 'ee'
      })
    },
    apply() {
      const lessonsToSave = this.allLessons.filter(lesson => this.selectedLessonsObj[lesson.lesson])
      this.updateSettings(lessonsToSave, this.primaryLanguage)
      this.$emit('close')
    }
  }
}
</script>

<style scoped>
/* Здесь могут быть стили для вашего приложения */
.settings {
  display: flex;
  flex-direction: column;
  padding: 20px;
  flex-grow: 1;
}
h1 {
  margin: 0 0 24px;
  color: #4caf50;
  font-size: 24px;
}
.language {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 24px;
}
.language span {
  font-weight: 500;
}
.language select {
  display: flex;
  height: 36px;
  flex-basis: 50%;
  padding-left: 8px;
}
.actions {
  display: flex;
  gap: 8px;
}
button {
  background: #4caf50; /* Светло-зеленый */
  border: none;
  color: white;
  padding: 0 20px;
  height: 36px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  cursor: pointer;
}
button.flag {
  padding: 0 12px;
  display: flex;
  align-items: center;
}
button.flag svg {
  height: 24px;
}
.lessons {
  display: flex;
  flex-direction: column;
  flex-grow: 1;
  margin: 12px 0;
  overflow-y: auto;
  height: 0;
}
</style>
