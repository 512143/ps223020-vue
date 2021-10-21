<template>
  <div class="container">
    <div class="row">
      <div class="col-sm-10">
        <h1>Заметки</h1>
        <hr />
        <br /><br />
        <alert :message="message" v-if="showMessage"></alert>
        <button
          type="button"
          class="btn btn-success btn-sm"
          v-b-modal.note-modal
        >
          Добавить заметку
        </button>
        <br /><br />

        <table class="table table-hover">
          <thead>
            <tr>
              <th scope="col">Заголовок</th>
              <th scope="col">Заметка</th>
              <th scope="col">Дата создания</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(note, index) in notes" :key="index">
              <td>{{ note.title }}</td>
              <td>{{ note.body }}</td>
              <td>{{ note.createTime }}</td>
              <td>
                <button
                  type="button"
                  class="btn btn-warning btn-sm"
                  v-b-modal.book-update-modal
                  @click="editNote(note)"
                >
                  > Изменить
                </button>
                <button type="button" class="btn btn-danger btn-sm">
                  Удалить
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <b-modal
      ref="addNoteModal"
      id="note-modal"
      title="добавление новой заметки"
      hide-footer
    >
      <b-form @submit="onSubmit" @reset="onReset" class="w-100">
        <b-form-group
          id="form-title-group"
          label="Заголовок:"
          label-for="form-title-input"
        >
          <b-form-input
            id="form-title-input"
            type="text"
            v-model="addNoteForm.title"
            required
            placeholder="Введите заголовк"
          >
          </b-form-input>
        </b-form-group>

        <b-form-group
          id="form-body-group"
          label="Текст"
          label-for="form-body-input"
        >
          <b-form-input
            id="form-author-input"
            type="text"
            v-model="addNoteForm.body"
            required
            placeholder="Введи подробности заметки"
          >
          </b-form-input>
        </b-form-group>

        <b-form-group
          id="form-createTime-group"
          label="Текст"
          label-for="form-createTime-input"
        >
          <b-form-input
            id="form-createTime-input"
            type="date"
            v-model="addNoteForm.createTime"
            required
          >
          </b-form-input>
        </b-form-group>

        <b-button type="submit" variant="primary">Сохранить</b-button>
        <b-button type="reset" variant="danger">Сбросить</b-button>
      </b-form>
    </b-modal>
    <b-modal
      ref="editNoteModal"
      id="note-update-modal"
      title="Изменить"
      hide-footer
    >
      <b-form @submit="onSubmitUpdate" @reset="onResetUpdate" class="w-100">
        <b-form-group
          id="form-title-edit-group"
          label="ЗАголовок:"
          label-for="form-title-edit-input"
        >
          <b-form-input
            id="form-title-edit-input"
            type="text"
            v-model="editForm.title"
            required
            placeholder="vvedi nazvanie"
          >
          </b-form-input>
        </b-form-group>

        <b-form-group
          id="form-body-edit-group"
          label="содержание:"
          label-for="form-body-edit-input"
        >
          <b-form-input
            id="form-body-edit-input"
            type="text"
            v-model="editForm.body"
            required
            placeholder="введи содержание"
          >
          </b-form-input>
        </b-form-group>

        <b-form-group
          id="form-createDate-edit-group"
          label="дата создания:"
          label-for="form-createDate-edit-input"
        >
          <b-form-input
            id="form-createDate-edit-input"
            type="date"
            v-model="editForm.createTime"
            required
          >
          </b-form-input>
        </b-form-group>
        <b-button type="submit" variant="primary">Обновить</b-button>
        <b-button type="reset" variant="danger">Закрыть</b-button>
      </b-form>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
import Alert from "../components/Alert";

export default {
  data() {
    return {
      editForm: {
       
        id: "",
        title: "",
        body: "",
        createTime: "",
      },
      notes: [],
      addNoteForm: {
        title: "",
        body: "",
        createTime: "",
      },
      message: "",
      showMessage: false,
    };
  },

  components: {
    alert: Alert,
  },

  methods: {
     editNote(note) {
          this.editForm = note;
        },
        
    onSubmitUpdate(evt) {
      evt.preventDefault();
      this.$refs.editNoteModal.hide();
      let read = false;
      if (this.editForm.read[0]) read = true;
      const payload = {
        title: this.addNoteForm.title,
        body: this.addNoteForm.body,
      };
      this.updateBook(payload, this.editForm.id);
    },
    getNotes() {
      const path = "http://localhost:56538/api/Notes";
      axios
        .get(path)
        .then((res) => {
          this.notes = res.data;
        })
        .catch((err) => {
          // eslint-отключение следующей строки
          console.error(err);
        });
    },
    addNote(payload) {
      const path = "http://localhost:56538/api/Notes";
      const headers = {
        "Content-Type": "application/json",
      };
      axios
        .post(path, payload, { headers })
        .then(() => {
          this.getNotes();
          this.message = "Book added!";
          this.showMessage = true;
          console.log("POST был выполнен");
        })
        .catch((error) => {
          // eslint-отключение следующей строки
          console.log(error);
          this.getNotes();
        });
    },
    initForm() {
      this.addNoteForm.title = "";
      this.addNoteForm.body = "";
      this.addNoteForm.createTime = "";
    },
    onSubmit(evt) {
      evt.preventDefault();
      this.$refs.addNoteModal.hide();

      const payload = {
        title: this.addNoteForm.title,
        body: this.addNoteForm.body,
        // createTime:this.addNoteForm.createTime // сокращённое свойство
      };
      this.addNote(payload);
      this.initForm();
    },
    onReset(evt) {
      evt.preventDefault();
      this.$refs.addNoteModal.hide();
      this.initForm();
    },
  },
  created() {
    this.getNotes();
  },
};
</script>

<style>
</style>