<template>
  <v-container style="background:white">
    <v-layout
      text-center
      wrap
    >
      <v-flex xs12>
        <v-img
          :src="require('../assets/newscreenshot1.png')"
          class="my-3"
          contain
          height="200"
        ></v-img>
      </v-flex>

      <v-flex mb-4>
        <h1 class="display-2 font-weight-bold mb-3">
          Welcome to Fintechee Algo Trading Generator
        </h1>
        <p class="subheading font-weight-regular">
          For help and collaboration with other Fintechee developers,
          <br>please join our online
          <a href="https://twitter.com/Fintechee1" target="_blank">Fintechee Twitter Community</a>
        </p>
      </v-flex>

      <v-flex
        mb-5
        xs12
      >
        <h2 class="headline font-weight-bold mb-3">Generate your algo trading source code in ONE minute!!</h2>
        <h2 class="headline font-weight-bold mb-3">We will be coming soon!!</h2>

        <v-layout justify-center>
            <editor v-model="content" @init="editorInit" lang="javascript" theme="chrome" width="500" height="100"></editor>
        </v-layout>
      </v-flex>

    </v-layout>
  </v-container>
</template>

<script>
export default {
  name: 'HelloWorld',

  data: () => ({
  }),

  components: {
      editor: require('vue2-ace-editor'),
  },

  methods: {
      editorInit: function () {
          require('brace/ext/language_tools') //language extension prerequsite...
          require('brace/mode/html')
          require('brace/mode/javascript')    //language
          require('brace/mode/less')
          require('brace/theme/chrome')
          require('brace/snippets/javascript') //snippet
      }
  },
};
</script>
