<template>
  <v-container>
    <v-row>
      <v-col sm="12" lg="3">
        <InfosCard v-if="minion !== null" :minion="minion"></InfosCard>
        <NetworkCard v-if="minion !== null" :minion="minion"></NetworkCard>
      </v-col>
      <v-col sm="12" lg="9">
        <MinionDetailCard v-if="minion !== null" :minion="minion"></MinionDetailCard>
      </v-col>
      <Fab v-if="fabs" :fabs="fabs" v-on:fab_action="fabAction"></Fab>
    </v-row>
  </v-container>
</template>

<script>
  import InfosCard from "../components/InfosCard"

  function addedGrains(data) {
    let grain = JSON.parse(data.grain)
    for (let key in grain) {
      data[key] = grain[key]
    }
    return data
  }

  import NetworkCard from "../components/NetworkCard"
  import MinionDetailCard from "../components/MinionDetailCard"
  import Fab from "../components/core/Fab"

  export default {
    name: "MinionDetail",
    components: { Fab, MinionDetailCard, InfosCard, NetworkCard },
    data() {
      return {
        minion: null,
        fabs: [
          {
            color: "blue",
            action: "refreshMinion",
            icon: "refresh",
            tooltip: "Refresh " + this.minion_id,
          },
          {
            color: "purple",
            action: "runMinion",
            icon: "play_arrow",
            tooltip: "Run job on " + this.minion_id,
          },
          {
            color: "orange",
            action: "highstateMinion",
            icon: "all_inclusive",
            tooltip: "Run highstate on " + this.minion_id,
          },
        ],
      }
    },
    mounted() {
      this.loadData()
    },
    methods: {
      loadData() {
        this.$http.get("api/minions/" + this.minion_id + "/").then(response => this.minion = addedGrains(response.data))
      },
      fabAction(action) {
        this[action]()
      },
      refreshMinion() {
        this.$toast("refreshing " + this.minion_id)
        let formData = new FormData
        formData.set('minion_id', this.minion_id)
        this.$http.post("/api/minions/refresh_minions/", formData).then(() => {
          this.$toast("minion refreshed")
        }).catch(function(error) {
          alert(error)
        })
      },
      runMinion() {
        this.$router.push("/run/?target=" + this.minion_id)
      },
      highstateMinion() {
        this.$router.push("/run/?target=" + this.minion_id + "&function=state.apply")
      },
    },
    props: [
      "minion_id",
    ],
  }
</script>

<style scoped>

</style>