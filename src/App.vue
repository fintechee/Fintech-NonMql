<template>
<v-app>
    <v-app-bar app color="#FFFFFF" flat style="height:60px;border-bottom:1px solid #EEEEEE">
        <div class="d-flex align-center">
            <v-img alt="Vuetify Logo" class="shrink mr-2" contain src="./assets/nonmql_logo.png" transition="scale-transition" width="40" />
        </div>
        <v-btn href="https://www.nonmql.com" target="_blank" text>
            <span class="mr-2" style="font-size:20px;font-weight:600">Non MQL</span>
        </v-btn>

        <v-spacer></v-spacer>

        <v-btn href="https://github.com/fintechee/nonmql" target="_blank" text>
            <span class="mr-2" style="font-size:12px;font-weight:600">
                Github
            </span>
            <v-icon style="font-size:12px">mdi-open-in-new</v-icon>
        </v-btn>
    </v-app-bar>

    <v-content>
        <HelloWorld />
    </v-content>

    <v-footer padless>
        <v-col class="text-center" cols="12" style="font-size:12px">
            © 2013 - 2021 — <strong>NON MQL</strong> Fintechee Expert Advisor Studio
        </v-col>
    </v-footer>
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

<style>
.v-application--wrap {
    background: #FFFFFF;
}
</style>
