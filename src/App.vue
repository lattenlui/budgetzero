<template>
  <v-app id="inspire">
    <!-- Modal to input reconcile amount  -->
    <BaseDialogModalComponent v-model="isModalVisibleCreateBudget">
      <template #title>
        Let's get started!
      </template>
      <template #body>
        <v-text-field
          v-model="budgetName"
          data-cy="budget-name"
          label="Enter a name for your budget"
          required
        />
      </template>
      <template #actions>
        <v-spacer />
        <v-btn
          id="btn-createBudget"
          color="accent"
          @click="createBudget()"
        >
          Create Budget
        </v-btn>
      </template>
    </BaseDialogModalComponent>

    <sidebar />

    <v-main>
      <router-view class="animated" />
    </v-main>
    <v-snackbar
      v-model="snackbar"
      color="accent"
    >
      {{ error_msg }}

      <template #action="{ attrs }">
        <v-btn
          color="white"
          text
          v-bind="attrs"
          @click="snackbar = false"
        >
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </v-app>
</template>

<script>
import Sidebar from "./components/Sidebar.vue";
import BaseDialogModalComponent from "./components/Modals/BaseDialogModalComponent.vue";

export default {
  name: "App",
  components: {
    Sidebar,
    BaseDialogModalComponent
  },
  data() {
    return {
      drawer: null,
      mini: false,
      budgetName: null
    };
  },
  computed: {
    isModalVisibleCreateBudget() {
      return !this.$store.getters.budgetExists;
    },
    error_msg() {
      return this.$store.getters.error_msg;
    },
    snackbar: {
      get() {
        return this.$store.getters.snackbar;
      },
      set(value) {
        this.$store.dispatch("setSnackBarBoolean", value);
      }
    }
  },
  mounted() {
    this.$store.dispatch("AUTH_CHECK");
  },
  methods: {
    createBudget() {
      this.$swal({
        title: "Budget Created",
        text: `A budget named ${this.budgetName} has been created.`,
        icon: "success",
        confirmButtonText: "Ok"
      });

      this.$store.dispatch("loadLocalBudgetRoot");
      this.$store.dispatch("createBudget", this.budgetName);
      this.$router.push({ path: `/budget` });
    }
  }
};
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.swal2-title {
  font-family: SofiaPro, Roboto !important;
  font-size: 2.125rem !important;
}

.transaction-table-header {
  background-color: var(--v-header_background-base) !important;
  color: var(--v-primary-base) !important;
  font-weight: 500;
  padding-right: 5px !important;
}
</style>
