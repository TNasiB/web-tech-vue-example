<template>
  <NCard class="student-card">
    <div class="student-card__inner">
      <span>{{ name }}</span>
      <div class="student-card__triggers-wrapper">
        <NButton type="primary" @click="editStudent">Edit</NButton>
        <NButton type="error" @click="deleteStudent">Delete</NButton>
      </div>
    </div>
  </NCard>
</template>

<script>
import { NCard, NButton } from "naive-ui";

export default {
  components: { NCard, NButton },
  // props это входные данные компонента, в <template> компонента App.vue мы "прокинули"
  // id и name внутрь каждой карточки, здесь мы явно указываем, что они существуют в компоненте
  // После этого мы можем использовать их в коде, обращаясь к ним через контекст вызова этого компонента (this)
  // Внутри <template> писать (this) не нужно.
  props: ["id", "name"],
  methods: {
    // Для того чтобы удалить или изменить данные, нам нужно отправить событие (this.$emit('event-name', event-params)) родительскому компоненту и
    // "уведомить его" о том, что он должен что то сделать, так же мы отдаем идентификатор данного студента, чтобы мы могли
    // понять, с каким конкретно студентом мы сейчас будем взаимодействовать
    deleteStudent() {
      this.$emit("delete-student", this.id);
    },
    async editStudent() {
      this.$emit("edit-student", this.id);
    },
  },
};
</script>

<style scoped>
.student-card__inner {
  display: flex;
  gap: 10px;
  justify-content: space-between;
}
.student-card__triggers-wrapper {
  display: flex;
  gap: 10px;
  align-items: center;
}
</style>
