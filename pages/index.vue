<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card>
        <v-card-title class="headline"> Schoonmaakrooster VLL120 </v-card-title>
        <v-card-text>
          <div id="rooster" v-html="schoonmaakrooster">
            {{ schoonmaakrooster }}
          </div>
        </v-card-text>
        <v-card-actions>
          <v-btn color="primary" @click="week--"> &lt; </v-btn>
          <v-btn color="primary" @click="thisWeek()"> Deze week </v-btn>
          <v-btn color="primary" @click="week++"> &gt; </v-btn>
          <v-btn color="primary" @click="CopyToClipboard"> Kopiëren </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  data() {
    return {
      schoonmaakrooster: "Loading...",
      monthNames: [
        "Januari",
        "Februari",
        "Maart",
        "April",
        "Mei",
        "Juni",
        "Juli",
        "Augustus",
        "September",
        "Oktober",
        "November",
        "December",
      ],
      week: 28 + 52 * 2,
      tasks: ["Keuken", "Vloeren", "Apparaten", "Glas & papier", "Trappenhuis"],
      namesDownstairs: ["Jaime", "Caitlin", "Veerle", "Timo", "Wijnand"],
      namesDownstairswc: ["Caitlin", "Wijnand", "Jaime", "Veerle", "Timo"],
      namesUpstairs: ["Sterre", "Eveline", "Hugo", "Maaike", "Floris"],
      namesUpstairswc: ["Floris", "Eveline", "Maaike", "Sterre", "Hugo"],
    };
  },
  mounted() {
    this.getCleanScheduleMessage();
  },
  watch: {
    week() {
      this.getCleanScheduleMessage();
    },
  },
  methods: {
    CopyToClipboard() {
      const storage = document.createElement("textarea");
      const copyText = document.getElementById("rooster");
      storage.value = copyText.innerText;
      storage.select();
      storage.setSelectionRange(0, 99999);
      navigator.clipboard.writeText(storage.value);
    },
    thisWeek() {
      var originalDate = new Date("6-8-2020");
      var nextMonday = new Date();
      nextMonday.setDate(
        nextMonday.getDate() + ((1 + 7 - nextMonday.getDay()) % 7)
      );
      var weeks = Math.round((nextMonday - originalDate) / 604800000);
      this.week = weeks + 1;
    },
    getCleanScheduleMessage() {
      var tomorrow = new Date("6-8-2020");
      tomorrow.setDate(tomorrow.getDate() + (this.week - 1) * 7);
      var message = "Het is weer tijd om schoon te maken!!\n";
      message +=
        "Deadline: " +
        tomorrow.getDate() +
        " " +
        this.monthNames[tomorrow.getMonth()] +
        "\n\n";
      for (let i = 0; i < this.tasks.length; i++) {
        if (this.week % 2 == 0) {
          message +=
            this.namesDownstairs[i] +
            ": " +
            this.tasks[
            (this.tasks.length + ((i - this.week / 2) % this.tasks.length)) %
            this.tasks.length
            ] +
            "\n";
        } else {
          message +=
            this.namesUpstairs[i] +
            ": " +
            this.tasks[
            (this.tasks.length +
              ((-0.5 + i - this.week / 2) % this.tasks.length)) %
            this.tasks.length
            ] +
            "\n";
        }
      }
      message +=
        "\nwc+douche benenden: " +
        this.namesDownstairswc[parseInt(this.week) % 5] +
        "\n";
      message +=
        "wc+douche boven: " +
        this.namesUpstairswc[parseInt(this.week) % 5] +
        "\n";
      this.schoonmaakrooster = message.split("\n").join("<br>");
    },
  },
};
</script>
