<template>
  <button
    v-for="(p, index) in participants"
    :key="index"
    @click="selectedParticipant = p"
  >
    {{ p }}
  </button>

  <input v-model="minimumReactions" type="number" />

  <button
    @click="openPlaylist"
  >
    Open playlist
  </button>

  <p>message count: {{ filteredMessages.length }} / {{ participantCount }} ( {{
  percentage }}% )</p>
  <table>
    <tr>
      <th>sender_name</th>
      <th>content</th>
    </tr>

    <tr v-for="(m, index) in filteredMessages" :key="index">
      <td>{{ m.sender_name }}</td>
      <td><a :href="m.url" target="_blank">{{ m.id }}</a></td>
    </tr>
  </table>
</template>

<script>

import message1 from './messages/message_1.json';
import message2 from './messages/message_2.json';

const messageFiles = [message1, message2];

const participants =
  new Set(messageFiles.map(mf => mf.participants).flat().map(p => p.name));

const messages = messageFiles.map(mf => mf.messages).flat();

console.log(participants);
console.log(messages);

export default {
  name: 'App',
  data() {
    return {
      participants,
      messages,
      selectedParticipant: null,
      minimumReactions: 0
    }
  },
  computed: {
    participantCount() {
      return this.messages.filter(
        m => this.selectedParticipant ? m.sender_name === this.selectedParticipant : true,
      ).length;
    },
    filteredMessages() {
      const filters = [
        m => this.selectedParticipant ? m.sender_name === this.selectedParticipant : true,
        m => this.minimumReactions > 0 ? m.reactions?.length >= this.minimumReactions : true
      ];

      return this.messages
        .filter(m => filters.reduce((acc, f) => acc && f(m), true))
        .map((m) => {
          const regex = /http(?:s?):\/\/(?:www\.)?youtu(?:be\.com\/watch\?v=|\.be\/)([\w\-_]*)(&(amp;)?[\w?=]*)?/;
          if (m.content?.match(regex)) {
            const matches = m.content?.match(regex);
            m.url = matches[0];
            m.id = matches[1];
          } else if (m.share?.link.match(regex)) {
            const matches = m.share?.link.match(regex);
            m.url = matches[0];
            m.id = matches[1];
          } else {
            m = undefined;
          }

          return m;
        })
        .filter(e => e !== undefined);
    },
    percentage() {
      return this.filteredMessages.length / this.participantCount * 100;
    },
    playlistUrl() {
      const urlStart = 'https://www.youtube.com/watch_videos?video_ids=';
      const ids = this.filteredMessages.slice(0, 50).map(m => m.id).join(',');

      return urlStart + ids;
    }
  },
  methods: {
    openPlaylist() {
      window.open(this.playlistUrl, '_blank');
    }
  }
}
</script>
