<template>
  <div style="position: absolute;bottom: 200px;right: 0;width: 100%;padding-left: 10px">
    <div style="border: 1px solid black" v-if="showSuggestions">
      <div v-for="person in filteredPeople" :key="person.id" @click="click_person_handler(person.id)">
        <span>avatar </span><span>status</span>
        {{ person.fullName }}
      </div>
    </div>
    <button v-if="!show" @click="show=true">
      Comment
    </button>
    <div v-else style="display: flex">
      <div contenteditable="true" ref="contentEditable" style="border:1px solid black ; flex:1" @input="input_handler">

      </div>
      <button @click="send_handler">Send</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: "Comment",
  data: function () {
    return {
      show: false,
      showSuggestions: false,
      mentionName: '',
      mentionPosition: -1,
      mentionedPeople: [],
      value: '',
      people: [
        {
          fullName: 'James',
          id: 'James',

        },
        {
          fullName: 'Adam',
          id: 'Adam',

        },
        {
          fullName: 'Maria',
          id: 'Maria',

        },
        {
          fullName: 'Elena',
          id: 'Elena',

        },
        {
          fullName: 'Phebe',
          id: 'Phebe',

        },
        {
          fullName: 'Aleksandre',
          id: 'Aleksandre',

        },
      ]
    }
  },
  computed: {
    filteredPeople: function () {
      return this.people.filter((name) => name.fullName.toLowerCase().includes(this.mentionName.toLowerCase()))
    }
  },
  mounted() {
    axios.get('https://b0c4-223-27-246-123.ap.ngrok.io/get-initial-suggestions?userId=466', (res) => {
      console.log(res)
    })
  },
  methods: {
    input_handler: function ($e) {
      this.value = $e.target.innerHTML;
      if (!this.value) this.$refs.contentEditable.innerHTML = ''
      this.mentionPosition = this.value.indexOf('@');
      if (this.mentionPosition === -1) this.showSuggestions = false
      if (this.mentionPosition !== -1) {
        this.mentionName = this.value.substring(this.mentionPosition + 1, this.value.length)
      }
      if (this.value.substring(this.value.length - 1) === '@') {
        this.showSuggestions = true;
      }
    },
    click_person_handler: function (id) {
      const person = this.filteredPeople.find((item) => item.id === id)
      const sub = this.value.substring(0, this.mentionPosition);
      this.showSuggestions = false

      this.$refs.contentEditable.innerHTML = sub + `<span style="color: blue" class="mentionedPerson" id="${person.id}">!${person.fullName}</span>&nbsp;`
    },
    send_handler: function () {
      if (!this.value) return;

      this.show = false;

      const spans = this.$refs.contentEditable.querySelectorAll('.mentionedPerson');
      const mentionedPeople = Array.from(spans).map(span => {
        return span.getAttribute('id')
      })

      this.$emit('send-message', {
        message: this.$refs.contentEditable.innerHTML,
        author: 'aleksandre',
        mentions: mentionedPeople,
        created: new Date(Date.now()).toDateString()
      })

    }
  }
}
</script>

<style scoped>

</style>