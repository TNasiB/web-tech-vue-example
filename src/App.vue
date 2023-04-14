<template>
  <div class="students-app">
    <NCard class="students-app__form-card">
      <span class="students-app__form-title">Student</span>
      <NForm class="students-app__form">
        <NFormItem label="Surname">
          <NInput v-model:value="newStudentForm.surname" />
        </NFormItem>
        <NFormItem label="Name">
          <NInput v-model:value="newStudentForm.name" />
        </NFormItem>
        <NFormItem label="Patronymic">
          <NInput v-model:value="newStudentForm.patronymic" />
        </NFormItem>
        <NButton type="info" class="students-app__add-trigger" @click="createStudent"
          >Add student</NButton
        >
      </NForm>
    </NCard>
    <NCard>
      <span class="students-app__form-title">Students list</span>
      <div class="students-app__list">
        <StudentCard
          v-for="student in students"
          :key="student.id"
          :name="student.name"
          :id="student.id"
          @delete-student="deleteStudent"
          @edit-student="editStudent"
        />
      </div>
    </NCard>
  </div>
</template>

<script>
import axios from "axios";
import { NCard, NDataTable, NButton, NInput, NForm, NFormItem } from "naive-ui";
import StudentCard from "./components/StudentCard.vue";

export default {
  components: {
    NCard,
    NDataTable,
    NButton,
    StudentCard,
    NInput,
    NForm,
    NFormItem,
  },
  data() {
    return {
      students: [],
      newStudentForm: {
        name: "",
        surname: "",
        patronymic: "",
      },
    };
  },
  async created() {
    // Это хук жизненного цикла компонента created, он срабатывает каждый раз, когда компонент инициализируется
    // В этом участке кода мы можем написать код, который будет выполнять каждый раз при инициализации компонента
    // Мы сделали GET запрос на наш Backend, который вернул нам данные, после чего мы записали их в модели нашего компонента
    const { data: students } = await axios.get("http://localhost:8080/students");
    this.students = students;
  },
  methods: {
    async createStudent() {
      // Для создания студента мы определяем последний счетчик идентификатора, если студентов нет, берем за значение 0
      // Метод массива at() с аргументом -1 возвращает нам последний элемент массива
      const lastId = this.students.at(-1)?.id ?? 0;
      const sendingData = {
        name: Object.values(this.newStudentForm).join(" "),
        id: lastId + 1,
      };

      // Здесь осуществляется POST запрос на создание студента на нашем Backend'е
      const { data: createdStudent } = await axios.post(
        "http://localhost:8080/students",
        sendingData
      );

      // Результат, который нам возвращается мы добавляем к остальным моделям студентов компонента
      this.students.push(createdStudent);

      // Обнуление состояния формы
      this.newStudentForm.name = "";
      this.newStudentForm.surname = "";
      this.newStudentForm.patronymic = "";
    },
    async deleteStudent(id) {
      // Мы прослушали событие, которое было активировано внутри карточки одного из студентов
      // В этом событии мы так же передали идентификатор студента, которого мы хотим удалить
      // По этому идентификатору мы отправляем DELETE запрос, а так же чистим нашу модель в компоненте
      await axios.delete(`http://localhost:8080/students/${id}`);
      this.students = this.students.filter((student) => student.id !== id);
    },
    async editStudent(id) {
      // Мы прослушали событие, которое было активировано внутри карточки одного из студентов
      // С помощью метода prompt мы запрашиваем новое имя студента
      // Отправляем запрос на редактирование

      // Обратите внимание, так как в теле запроса мы отправляем не JSON, а только строку, нам необходимо вручную
      // указать заголовки запроса, в частности content type

      const newName = prompt("Input new name");
      await axios.put(`http://localhost:8080/students/${id}`, newName, {
        headers: {
          "Content-Type": "text/plain",
        },
      });

      // Находим студента, которого мы только что редактировали
      const editedStudent = this.students.find((student) => student.id === id);
      // Если каким то образом мы его не нашли, мы прекращаем работу метода, чтобы избежать ошибок
      if (!editedStudent) return;
      // Присваеваем ему новое имя
      editedStudent.name = newName;
    },
  },
};
</script>

<style scoped>
.students-app {
  display: flex;
  gap: 10px;
  padding: 40px;
  max-width: 1000px;
  margin: 0 auto;
}
.students-app__list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.students-app__add-trigger {
  justify-self: flex-end;
}
.students-app__form {
  margin-top: 20px;
}
.students-app__form-title {
  font-weight: 700;
  font-size: 36px;
}
.students-app__form-card {
  align-self: flex-start;
}
</style>
