<template>
  <div class="min-h-screen bg-gray-100 flex items-center justify-center py-12 px-4 sm:px-6 lg:px-8">
    <div class="max-w-md w-full space-y-8">
      <div>
        <h2 class="mt-6 text-center text-3xl font-extrabold text-gray-900">
          Sign up for an account
        </h2>
      </div>
      <form class="mt-8 space-y-6">
        <div class="flex flex-col space-y-3 rounded-md shadow-sm -space-y-px">
          <div>
            <label for="email-address" class="block text-sm font-medium text-gray-700">
              Email address
            </label>
            <input v-model="formData.email.value" id="email-address" name="email" type="email" autocomplete="email"
              required
              class="appearance-none rounded-md relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
              placeholder="Email address" />
            <span v-for="error in formErrors.email" for="email-address" class="block text-red-500 text-xs mt-1 ml-1"
              :class="{ hidden: !fieldsTouched.email }">
              {{ error }}
            </span>
          </div>
          <div>
            <label for="name" class="block text-sm font-medium text-gray-700">Full name</label>
            <input v-model="formData.name.value" id="name" name="name" type="text" autocomplete="name"
              class="appearance-none rounded-md relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
              placeholder="Full name" />
            <span v-for="error in formErrors.name" for="email-address" class="block text-red-500 text-xs mt-1 ml-1">
              {{ error }}
            </span>
          </div>
          <div>
            <label for="password" class="block text-sm font-medium text-gray-700">
              Password
            </label>
            <input v-model="formData.password.value" id="password" name="password" type="password"
              autocomplete="new-password" required
              class="appearance-none rounded-md relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
              placeholder="Password" />
            <span v-for="error in formErrors.password" for="email-address" class="block text-red-500 text-xs mt-1 ml-1">
              {{ error }}
            </span>
          </div>
          <div>
            <label for="password-again" class="block text-sm font-medium text-gray-700">
              Password again
            </label>
            <input v-model="formData.passwordAgain.value" id="password-again" name="password-again" type="password"
              autocomplete="new-password" required
              class="appearance-none rounded-md relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
              placeholder="Password again" />
            <span v-for="error in formErrors.passwordAgain" for="email-address"
              class="block text-red-500 text-xs mt-1 ml-1">
              {{ error }}
            </span>
          </div>
        </div>

        <div>
          <button type="submit" :disabled="!isFormValid"
            class="disabled:opacity-50 group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 enabled:hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
            Sign Up
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from "@vue/reactivity";
import { reactive, watch } from "vue";

const EMAIL_REGEX = /^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$/;

const required = (value: string): string | undefined =>
  value.length > 0 ? undefined : "Field is required";

const validEmail = (value: string): string | undefined =>
  value.length > 0 && !EMAIL_REGEX.test(value)
    ? "Invalid email address"
    : undefined;

const minLength = (value: string, minLength: number): string | undefined =>
  value.length > 0 && value.length < minLength
    ? `Must be at least ${minLength} characters`
    : undefined;

const maxLength = (value: string, maxLength: number): string | undefined =>
  value.length > 0 && value.length > maxLength
    ? `Must not exceed ${maxLength} characters`
    : undefined;

const passwordMatch = (
  password: string,
  passwordAgain: string
): string | undefined =>
  passwordAgain.length > 0 && password !== passwordAgain
    ? "Passwords do not match"
    : undefined;

const formData = {
  email: ref(""),
  name: ref(""),
  password: ref(""),
  passwordAgain: ref(""),
};

const formErrors: Record<keyof typeof formData, Array<string>> = reactive({
  email: computed(() =>
    [required(formData.email.value), validEmail(formData.email.value)].filter(
      (err): err is string => err !== undefined
    )
  ),
  name: computed(() =>
    [
      required(formData.name.value),
      minLength(formData.name.value, 5),
      maxLength(formData.name.value, 250),
    ].filter((err): err is string => err !== undefined)
  ),
  password: computed(() =>
    [
      required(formData.password.value),
      minLength(formData.password.value, 8),
    ].filter((err): err is string => err !== undefined)
  ),
  passwordAgain: computed(() =>
    [
      required(formData.passwordAgain.value),
      passwordMatch(formData.password.value, formData.passwordAgain.value),
    ].filter((err): err is string => err !== undefined)
  ),
});

const fieldsTouched: Record<keyof typeof formData, boolean> = reactive({
  name: ref(false),
  email: ref(false),
  password: ref(false),
  passwordAgain: ref(false),
});

watch(formData.name, () => {
  fieldsTouched.name = true;
});
watch(formData.email, () => {
  fieldsTouched.email = true;
});
watch(formData.password, () => {
  fieldsTouched.password = true;
});
watch(formData.passwordAgain, () => {
  fieldsTouched.passwordAgain = true;
});

const isFormValid = computed(() =>
  Object.values(formErrors).every((errors) => errors.length === 0)
);
</script>
