<template>
  <div class="home">
    <Header />
    <b-row v-if="loading" align-v="center" class="text-center mt-5">
      <b-col class="mt-5">
        <b-spinner variant="primary" label="Spinning" style="width: 4rem; height: 4rem;"></b-spinner>
      </b-col>
    </b-row>
    <b-row class="home-row mt-5 text-center p-5" v-else>
      <b-col>
        <b-table
          hover
          :items="activities"
          :fields="fields"
          thead-class="thead-block"
          selectable
          @row-selected="showActivity"
        ></b-table>
      </b-col>
    </b-row>
  </div>
</template>

<script>
import Header from "@/components/common/Header";
import { API_URL } from "../config";
export default {
  name: "Home",
  components: {
    Header,
  },
  beforeMount: async function () {
    await this.getActivities();
  },
  methods: {
    async getActivities() {
      this.loading = true;
      this.token = await localStorage.getItem("token");
      await fetch(API_URL + "activities", {
        method: "GET",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json",
          Authorization: this.token,
        },
      })
        .then((response) => response.json())
        .then(async (response) => {
          this.loading = false;
          this.activities = response.activities;
          this.activities.forEach((activity) => {
            switch (activity.state.label) {
              case "Accepted":
                activity.state.label = "Accepté";
                break;
              case "Denied":
                activity.state.label = "Refusé";
                break;
              case "Pending":
                activity.state.label = "En attente";
                break;
            }
          });
        })
        .catch((error) => {
          console.error(error);
          this.loading = false;
        });
    },
    showActivity(items) {
      this.$router.replace({
        path: "/activity/" + items[0].id,
      });
    },
  },
  data() {
    return {
      loading: false,
      token: null,
      activities: [],
      fields: [
        {
          key: "name",
          label: "Nom",
          sortable: true,
        },
        {
          key: "address",
          label: "Adresse",
          sortable: false,
        },
        {
          key: "postal_code.code",
          label: "Code postal",
          sortable: true,
        },
        {
          key: "siren",
          label: "Siren",
          sortable: false,
        },
        {
          key: "phone_number",
          label: "Téléphone",
          sortable: false,
        },
        {
          key: "state.label",
          label: "État",
          sortable: false,
        },
      ],
    };
  },
};
</script>

<style>
</style>