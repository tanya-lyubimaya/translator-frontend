<template>
  <q-layout view="hHh lpR fFf">
    <q-header reveal elevated class="bg-primary text-white">
      <q-toolbar class="bg-green">
        <q-toolbar-title>
          <q-avatar>
            <img src="../../images/earth-americas-solid.svg" />
          </q-avatar>
          Translator
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
      <div class="row justify-center">
        <div class="col col-4">
          <div class="row wrap content-start justify-end">
            <q-select
              v-model="inputLanguage"
              autofocus="true"
              borderless
              clearable
              use-input
              input-debounce="0"
              label="Language"
              :options="languages"
              @filter="filterFn"
              color="orange"
              behavior="menu"
            />
          </div>
          <q-input
            autogrow
            outlined
            clearable
            use-input
            input-debounce="0"
            color="orange"
            v-model="input"
            type="textarea"
            rows="10"
            label="Text"
            class="col-4"
            style="overflow: auto"
            clear-icon="close"
          />
        </div>
        <div
          class="row col-1 justify-center items-center"
          style="min-height: 4em; max-height: 4em"
        >
          <q-btn
            push
            color="green"
            round
            icon="autorenew"
            @click="switchLanguage"
          />
        </div>
        <div class="col col-4">
          <q-select
            v-model="outputLanguage"
            borderless
            clearable
            use-input
            input-debounce="0"
            label="Language"
            :options="languages"
            @filter="filterFn"
            color="orange"
            style="width: 250px"
            behavior="menu"
            class="col-4 offset-1"
          />
          <q-input
            autogrow
            outlined
            disabled
            color="orange"
            v-model="output"
            type="textarea"
            label="Translation"
            class="col-4 offset-1"
            style="overflow: auto"
          />
        </div>
      </div>
      <p></p>
      <div class="fit row wrap justify-center content-start">
        <q-btn
          push
          color="white"
          text-color="orange"
          label="Translate"
          @click="sendForTranslation"
        />
      </div>
    </q-page-container>
  </q-layout>
</template>

<script>
import { ref } from "vue";

export default {
  setup() {
    return {
      output: ref(null),
      inputLanguage: ref("Russian"),
      outputLanguage: ref("English"),
      languagesAll: [],
      model: ref("one"),
    };
  },
  data() {
    return { input: "", languages: [], minRows: 10 };
  },
  created() {
    const path = "http://localhost:8080/get-languages";
    this.$axios
      .get(path)
      .then((res) => {
        this.languages = res.data;
        this.languagesAll = res.data;
      })
      .catch((err) => {
        this.$q.notify({
          position: this.notificationsPos,
          icon: "warning",
          type: "negative",
          multiLine: true,
          message: "An error occurred: " + err,
        });
      });
  },
  methods: {
    sendForTranslation() {
      const url = "http://localhost:8080/get-translation";

      if (this.inputLanguage == this.outputLanguage) {
        this.$q.notify({
          position: this.notificationsPos,
          icon: "warning",
          type: "negative",
          multiLine: true,
          message:
            "You're trying to translate from " +
            this.inputLanguage +
            " to " +
            this.outputLanguage +
            "!",
        });
      }
      this.$axios
        .post(url, {
          source_lang: this.inputLanguage,
          target_lang: this.outputLanguage,
          source_text: this.input,
        })
        .then((res) => {
          this.output = res.data;
        })
        .catch((err) => {
          this.$q.notify({
            position: this.notificationsPos,
            icon: "warning",
            type: "negative",
            multiLine: true,
            message: "An error occurred: " + err,
          });
        });
    },

    switchLanguage() {
      let tmp = this.inputLanguage;
      this.inputLanguage = this.outputLanguage;
      this.outputLanguage = tmp;
    },

    filterFn(val, update, abort) {
      update(() => {
        const needle = val.toLowerCase();
        this.languages = this.languagesAll.filter(
          (v) => v.toLowerCase().indexOf(needle) > -1
        );
      });
    },
  },
};
</script>
