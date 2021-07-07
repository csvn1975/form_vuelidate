<template>
  <div class="register">
    <div class="register__form-wrapper">
      <div class="row">
        <div class="col-12">
          <h1 class="register__heading">Register</h1>
          <form>
            <div class="form-group">
              <!-- username  -->
              <input
                type="text"
                class="form-control"
                id="username"
                v-model="state.username"
                placeholder="Benutzername"
              />
              <span class="register--message" v-if="v$.username.$error">
                {{ errorMessage("username") }}
              </span>
            </div>
            <!--  end username -->

            <!-- email  -->
            <div class="form-group">
              <input
                type="email"
                class="form-control"
                id="email"
                v-model="state.email"
                placeholder="Email"
              />
              <span class="register--message" v-if="v$.email.$error">
                {{ errorMessage("email") }}
              </span>
            </div>
            <!-- end email -->

            <!-- password and confirm password -->
            <div class="form-group">
              <div class="row">
                <!-- password -->
                <div class="col-6">
                  <input
                    type="password"
                    class="form-control"
                    id="password"
                    v-model="state.password.password"
                    placeholder="Passwort"
                  />

                  <span
                    class="register--message"
                    v-if="v$.password.password.$error"
                  >
                    {{ errorMessage("password.password") }}
                  </span>
                </div>

                <!-- confirm password -->
                <div class="col-6">
                  <input
                    type="password"
                    class="form-control"
                    id="conf-password"
                    v-model="state.password.confirm"
                    placeholder="Passwort bestätigen"
                  />
                  <span
                    class="register--message"
                    v-if="v$.password.confirm.$error"
                  >
                    {{ errorMessage("password.confirm") }}
                  </span>
                </div>
              </div>
            </div>
            <!-- end password/ confirm -->

            <!-- gender -->
            <div class="form-group">
              <label class="form-check-label" for="">Geschlect</label>
              <div class="row">
                <div class="col-3">
                  <input
                    type="radio"
                    v-model="state.gender"
                    value="1"
                    class="form-check-input"
                    id="male"
                  />
                  <label class="form-check-label mx-1" for="male"
                    >männlich</label
                  >
                </div>

                <div class="col-3">
                  <input
                    type="radio"
                    v-model="state.gender"
                    value="2"
                    class="form-check-input"
                    id="female"
                  />
                  <label class="form-check-label mx-1" for="fermale"
                    >weiblich</label
                  >
                </div>
                <span class="register--message" v-if="v$.gender.$error">
                  {{ errorMessage("gender") }}
                </span>
              </div>
            </div>
            <!-- end gender -->

            <!-- Datenschutz -->
            <div class="form-group">
              <div class="row">
                <div class="col-12">
                  <input
                    type="checkbox"
                    v-model="state.privacy"
                    class="form-check-input"
                    id="privacy"
                  />
                  <label class="form-check-label mx-2" for="privacy"
                    >Ich akzeptiere
                    <span class="text-primary">
                      die Datenschutzbestimmungen</span
                    ></label
                  >
                </div>
                <span class="register--message" v-if="v$.privacy.$error">
                  Bitte bestätigen Sie die Datenschutzbestimmungen
                </span>
              </div>
              <!-- end gender -->
            </div>
            <button
              type="submit"
              @click.prevent="submitForm"
              class="btn btn-primary float-right"
            >
              Submit
            </button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import useVuelidate from "@vuelidate/core";
import { required, email, minLength, helpers } from "@vuelidate/validators";

import { reactive, computed } from "vue";

export default {
  setup() {
    /* fields of validate */

    const state = reactive({
      username: "",
      email: "",
      gender: "",
      privacy: false,

      password: {
        password: "",
        confirm: "",
      },
    });

    /* rules validate */
    const rules = computed(() => {
      return {
        username: {
          required: helpers.withMessage(
            "Bitte geben Sie einen Benutzername ein.",
            required
          ),
          minLength: helpers.withMessage(
            "Bitte geben Sie mindesten 3 Zeichen ein!",
            minLength(3)
          ),
        },

        gender: {
          required: helpers.withMessage(
            "Bitte wählen Sie Ihr Geschlecht aus!",
            required
          ),
        },

        privacy: {
          checked(val) {
            return val;
          },
        },

        password: {
          password: {
            required: helpers.withMessage(
              "Bitte geben Sie ein Password ein.",
              required
            ),
            minLength: helpers.withMessage(
              "Bitte geben Sie mindesten 4 Zeichen ein!",
              minLength(4)
            ),
          },

          confirm: {
            required: helpers.withMessage(
              "Bitte bestätigen Sie das Password.",
              required
            ),
            /*  someAs: sameAs(state.password.password),  */
            confirmPassword: helpers.withMessage(
              "Das Password stimmt nicht überein.",
              () => state.password.confirm === state.password.password
            ),
          },
        },

        email: {
          required: helpers.withMessage(
            "Bitte geben Sie eine Email ein.",
            required
          ),
          email: helpers.withMessage(
            "Bitte geben Sie eine gültige Email ein.",
            email
          ),
        },
      };
    });

    const v$ = useVuelidate(rules, state);

    return { v$, state };
  },

  methods: {
    submitForm() {
      /*  this.v$.$validate();
      console.log([this.v$.username]); */

      console.log("Formular abgeschickt...");
      this.v$.$touch();
      if (this.v$.$invalid) {
        console.log("Fehlgeschlagen :(");
      } else {
        console.log("Erfolgreich :)");
      }
    },

    errorMessage(field) {
      const keys = field.split(".");
      return keys.reduce((acc, item) => acc[item], this.v$).$errors[0].$message;
    },

    
  },
};
</script>

<style lang="scss">
$bg-color-gradient-from: #6610f2;
$bg-color-gradient-to: #6f42c1;
$bg-form-color: #f8f9fa;

$error-text-color: #dc3545;

.register {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100vh;
  font-size: 16px;

  background: linear-gradient(
    to bottom,
    $bg-color-gradient-from 0%,
    $bg-color-gradient-to 100%
  );

  &__form-wrapper {
    background-color: $bg-form-color;
    padding: 20px 30px;
    width: 50%;
    border-radius: 5px;
    box-shadow: 1px 1px 5px rgba(0, 0, 0, 0.5);
  }

  &__heading {
    color: #6610f2;
    margin-bottom: 5px;
    text-align: center;
  }

  &--message {
    color: $error-text-color;
    font-size: 0.75rem;
  }


  .form-group {
    margin-bottom: 1rem;
  }
  
  .form-control,
  .btn,
  .form-check-input {
    box-shadow: unset !important;
  }
}
</style>
