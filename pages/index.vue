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
import { SupabaseClient, createClient } from '@supabase/supabase-js'

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
  async mounted() {
    await this.getNames()
    this.getCleanScheduleMessage();
  },
  watch: {
    week() {
      this.getCleanScheduleMessage();
    },
  },
  methods: {
    async getNames() {
      if (!process.env.SUPABASE_URL || !process.env.SUPABASE_ANON_KEY) {
        throw new Error('Database not configured')
      }

      const supabase = createClient(
        process.env.SUPABASE_URL ?? '',
        process.env.SUPABASE_ANON_KEY ?? ''
      )

      const names = await supabase.from('roommates').select("name").order('id')
      console.log(names.data)
      this.namesDownstairs = []
      for (let i = 0; i < 5; i++) {
        this.namesDownstairs.push(names.data[i].name);
      }
      this.namesUpstairs = []
      for (let i = 5; i < 10; i++) {
        this.namesUpstairs.push(names.data[i].name);
      }
      console.log(this.namesUpstairs)
      console.log(this.namesDownstairs)
      this.namesDownstairswc = [this.namesDownstairs[1], this.namesDownstairs[4], this.namesDownstairs[0], this.namesDownstairs[2], this.namesDownstairs[3]]
      this.namesUpstairswc = [this.namesUpstairs[4], this.namesUpstairs[1], this.namesUpstairs[3], this.namesUpstairs[0], this.namesUpstairs[2]]
    },
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
