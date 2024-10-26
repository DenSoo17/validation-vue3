<template>
  <div class="container">
    <div class="field">
      <label for="name" class="label">Имя / Name</label>
      <div class="control">
        <input
          type="text"
          id="name"
          class="input"
          v-model="form.name"
          :class="{ 'is-danger': !!validate({ prop: 'name' }) }"
          @focus="resetField('name')"
          autocomplete="off"
        />
        <p v-if="!!validate({ prop: 'name' })" class="help is-danger">
          {{ validate({ prop: "name" }) }}
        </p>
      </div>
    </div>

    <div class="field">
      <label for="email" class="label">Почта / Email</label>
      <div class="control">
        <input
          type="text"
          id="email"
          class="input"
          v-model="form.email"
          :class="{ 'is-danger': !!validate({ prop: 'email' }) }"
          @focus="resetField('email')"
          autocomplete="off"
        />
        <p v-if="!!validate({ prop: 'email' })" class="help is-danger">
          {{ validate({ prop: "email" }) }}
        </p>
      </div>
    </div>

    <div class="field">
      <label for="phone" class="label">Телефон / Phone</label>
      <div class="control">
        <input
          type="text"
          id="phone"
          class="input"
          v-model="form.phone"
          v-maska
          :data-maska="'+7(###)-###-##-##'"
          :class="{ 'is-danger': !!validate({ prop: 'phone' }) }"
          @focus="resetField('phone')"
          autocomplete="off"
        />
        <p v-if="!!validate({ prop: 'phone' })" class="help is-danger">
          {{ validate({ prop: "phone" }) }}
        </p>
      </div>
    </div>

    <div class="field">
      <label for="information" class="label">
        Информация о себе/ Information about self</label
      >
      <div class="control">
        <textarea
          type="textarea"
          id="information"
          class="textarea"
          v-model="form.information"
          :class="{ 'is-danger': !!validate({ prop: 'information' }) }"
          @focus="resetField('information')"
          autocomplete="off"
        >
        </textarea>
        <p v-if="!!validate({ prop: 'information' })" class="help is-danger">
          {{ validate({ prop: "information" }) }}
        </p>
      </div>
    </div>

    <div class="field">
      <label for="password" class="label"> Пароль / Password</label>
      <div class="control">
        <input
          type="password"
          id="password"
          class="input"
          v-model="form.password"
          :class="{ 'is-danger': !!validate({ prop: 'password' }) }"
          @focus="resetField('password')"
          autocomplete="off"
        />
        <p v-if="!!validate({ prop: 'password' })" class="help is-danger">
          {{ validate({ prop: "password" }) }}
        </p>
      </div>
    </div>

    <div class="field">
      <label for="currentPassword" class="label">
        Повторите пароль / Repeat password
      </label>
      <div class="control">
        <input
          type="password"
          id="currentPassword"
          class="input"
          v-model="form.currentPassword"
          :class="{ 'is-danger': !!validate({ prop: 'currentPassword' }) }"
          @focus="resetField('currentPassword')"
          autocomplete="off"
        />
        <p
          v-if="!!validate({ prop: 'currentPassword' })"
          class="help is-danger"
        >
          {{ validate({ prop: "currentPassword" }) }}
        </p>
      </div>
    </div>

    <div class="field">
      <div class="control">
        <label
          class="label"
          :class="{ 'has-text-danger': !!validate({ prop: 'checkbox' }) }"
        >
          <input type="checkbox" v-model="form.checkbox" autocomplete="off" />
          Я соглашаюсь с <a href="#">правилами сообщества</a>
        </label>
      </div>
    </div>

    <div class="control mx-auto mt-2">
      <button
        @click="onSubmit"
        :disabled="form.pending"
        class="button is-link"
        :class="{ 'is-loading': form.pending }"
      >
        Отправить
      </button>
    </div>
  </div>
</template>

<script setup>
import { reactive, computed } from "vue";
import {
  required,
  email,
  requiredPhone,
  minLength,
  maxLength,
  sameAs,
} from "./utils/i18n-validators";

import { vMaska } from "maska/vue";
import useValidate from "@vuelidate/core";

const form = reactive({
  name: null,
  email: null,
  phone: null,
  information: null,
  password: null,
  currentPassword: null,
  checkbox: null,
  pending: null,
});

const rules = computed(() => ({
  name: {
    required,
    minLength: minLength(3),
  },
  email: {
    required,
    email,
  },
  phone: {
    required,
    requiredPhone: requiredPhone(17),
  },
  information: {
    minLength: minLength(10),
  },
  password: {
    required,
    minLength: minLength(8),
    maxLength: maxLength(15),
  },
  currentPassword: {
    required,
    minLength: minLength(8),
    maxLength: maxLength(15),
    sameAs: sameAs(form.password),
  },
  checkbox: {
    required,
    sameAs: sameAs(true),
  },
}));

const v = useValidate(rules, form);

const validate = ({ prop }) => {
  const error = v.value.$errors.find((el) => el.$property === prop);
  return error && error.$message;
};

const resetForm = () => {
  Object.keys(form).forEach((key) => {
    form[key] = null;
  });

  v.value.$reset();
};

const resetField = (fieldName) => {
  const field = v.value[fieldName];
  if (field) {
    field.$reset();
  }
};

const onSubmit = async () => {
  v.value.$touch();

  if (form.pending) return;

  if (await v.value.$validate()) {
    form.pending = true;

    try {
      const payload = {
        name: form.name,
        email: form.email,
        phone: form.phone,
        information: form.information,
        password: form.password,
        currentPassword: form.currentPassword,
        checkbox: form.checkbox,
      };

      setTimeout(() => {
        console.log(payload);
        console.log("Запрос отправлен");

        resetForm();
      }, 2500);
    } catch (error) {
      console.log(error);
    }
  }
};
</script>
