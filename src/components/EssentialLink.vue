<template>
  <q-container>
    <div>
      <div class="fit row no-wrap justify-center items-center content-center">
        <div style="overflow: auto;">
          <q-chat-message name="me" avatar="https://cdn.quasar.dev/img/avatar4.jpg" :text="['hey, how are you?']" sent
            stamp="7 minutes ago" />
          <q-chat-message name="Jane" avatar="https://cdn.quasar.dev/img/avatar3.jpg" :text="[`doing fine, how r you?`]"
            stamp="4 minutes ago" />
        </div>
      </div>
    </div>
  </q-container>
</template>

<script setup>
defineOptions({
  name: 'EssentialLink'
})

const props = defineProps({
  title: {
    type: String,
    required: true
  },

  caption: {
    type: String,
    default: ''
  },

  link: {
    type: String,
    default: '#'
  },

  icon: {
    type: String,
    default: ''
  }
})
</script>
