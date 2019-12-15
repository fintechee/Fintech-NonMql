<template>
<v-app>
    <v-app-bar app color="#EEEEEE" flat>
        <div class="d-flex align-center">
            <v-img alt="Vuetify Logo" class="shrink mr-2" contain src="./assets/fintecheelogo.png" transition="scale-transition" width="40" />
        </div>
        <v-btn href="https://www.fintechee.com" target="_blank" text>
            <span class="mr-2">www.Fintechee.com</span>
        </v-btn>

        <v-spacer></v-spacer>

        <v-btn href="https://github.com/fintechee/nonmql" target="_blank" text>
            <span class="mr-2">
                Github
            </span>
            <v-icon>mdi-open-in-new</v-icon>
        </v-btn>
    </v-app-bar>

    <v-content>
        <HelloWorld />
    </v-content>
</v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld';

export default {
    name: 'App',

    components: {
        HelloWorld,
    },

    data: () => ({
        //
    }),
};
</script>
