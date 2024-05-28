<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card>
        <v-card-title class="headline"> Admin Schoonmaakrooster VLL120 </v-card-title>
        <v-card-text>
          <div v-for="(name, index) in names" :key="index">
            {{ names[index].id }}
            <v-text-field label="" variant="outlined" v-model="names[index].name" density="compact"></v-text-field>
            <v-btn size="small" block @click="updateName(names[index].id)">Update</v-btn>
          </div>
        </v-card-text>
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
      names: [],
    };
  },
  async mounted() {
    await this.getNames()
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

      const { data, error } = await supabase
        .from('roommates')
        .select('id, name')
        .order('id');

      if (error) {
        console.error('Error fetching names:', error);
        return;
      }

      this.names = data.map(item => ({ id: item.id, name: item.name }));
    },
    async updateName(nameId) {
      if (!process.env.SUPABASE_URL || !process.env.SUPABASE_ANON_KEY) {
        throw new Error('Database not configured');
      }

      const supabase = createClient(
        process.env.SUPABASE_URL,
        process.env.SUPABASE_ANON_KEY
      );

      const result = this.names.find(item => item.id === nameId);
      const { id, name } = result;
      console.log(id, name)
      
      const { data, error } = await supabase
        .from('roommates')
        .update({ name })
        .eq('id', id);

      if (error) {
        console.error('Error updating name:', error);
      } else {
        console.log('Name updated successfully:', data);
      }
    },
  },
};
</script>
