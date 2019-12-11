<template>
<v-container style="background:white">
    <v-layout text-center wrap>
        <v-flex xs12>
            <v-img :src="require('../assets/newscreenshot1.png')" class="my-3" contain height="200"></v-img>
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

        <v-flex mb-5 xs12>
            <h2 class="headline font-weight-bold mb-3">Generate your algo trading source code in ONE minute!!</h2>

            <v-layout justify-center>
                <v-card class="ma-5" min-width="400">
                    <v-form class="pa-5" ref="form">
                        <v-text-field label="Broker" placeholder="Broker" v-model="broker" :rules="brokerRules"></v-text-field>
                        <v-text-field label="Account" placeholder="Account" v-model="account" :rules="accountRules"></v-text-field>
                        <v-text-field label="Instrument" placeholder="Instrument" v-model="instrument" :rules="instrumentRules"></v-text-field>
                        <v-text-field label="Indicator" placeholder="Indicator" v-model="indicator" :rules="indicatorRules"></v-text-field>

                        <v-btn text @click="clearForm()">Clear</v-btn>
                        <v-btn color="warning" dark @click="generateSourceCode()">Generate</v-btn>
                    </v-form>
                </v-card>
            </v-layout>

            <v-layout justify-center>
                <editor v-model="sourceCode" @init="editorInit" lang="javascript" theme="chrome" width="1000" height="1000"></editor>
            </v-layout>

            <h2 class="headline font-weight-bold mb-3">To be Continued!!</h2>
        </v-flex>

    </v-layout>
</v-container>
</template>

<script>
export default {
    name: 'HelloWorld',

    data: () => ({
            broker: null,
            account: null,
            indicator: null,
            brokerRules: [
                v => !!v || 'Broker is required',
                v => (!!v && v.length <= 20) || 'Too many characters.'
            ],
            accountRules: [
                v => !!v || 'Account is required',
                v => (!!v && v.length <= 20) || 'Too many characters.'
            ],
            instrumentRules: [
                v => !!v || 'Instrument is required',
                v => (!!v && v.length <= 20) || 'Too many characters.'
            ],
            indicatorRules: [
                v => !!v || 'Indicator is required',
                v => (!!v && v.length <= 20) || 'Too many characters.'
            ],

            sourceCode: '',

            template1:
    'registerEA(\n' +
    '	"sample_using_sma",\n' +
    '	"A test EA based on sma",\n' +
    '	[{ // parameters\n' +
    '		name: "period",\n' +
    '		value: 20,\n' +
    '		required: true,\n' +
    '		type: PARAMETER_TYPE.INTEGER,\n' +
    '		range: [1, 100]\n' +
    '	}],\n' +
    '	function (context) { // Init()\n' +
    '		var account = getAccount(context, 0)\n' +
    '		var brokerName = getBrokerNameOfAccount(account)\n' +
    '		var accountId = getAccountIdOfAccount(account)\n' +
    '		var symbolName = "EUR/USD"\n' +
    '       getQuotes(context, brokerName, accountId, symbolName)\n' +
    '       window.chartHandle = getChartHandle(context, brokerName, accountId, symbolName, TIME_FRAME.M1)\n' +
    '       var period = getEAParameter(context, "period")\n' +
    '       window.indiHandle = getIndicatorHandle(context, brokerName, accountId, symbolName, TIME_FRAME.M1, "sma", [{\n' +
    '           name: "period",\n' +
    '           value: period\n' +
    '       }])\n' +
    '    },\n' +
    '    function(context) { // Deinit()\n' +
    '       delete window.currTime\n' +
    '    },\n' +
    '    function(context) { // OnTick()\n' +
    '       var arrTime = getData(context, window.chartHandle, DATA_NAME.TIME)\n' +
    '       if (typeof window.currTime == "undefined") {\n' +
    '           window.currTime = arrTime[arrTime.length - 1]\n' +
    '       } else if (window.currTime != arrTime[arrTime.length - 1]) {\n' +
    '           window.currTime = arrTime[arrTime.length - 1]\n' +
    '       } else {\n' +
    '           return\n' +
    '       }\n' +
    '       var account = getAccount(context, 0)\n' +
    '       var brokerName = getBrokerNameOfAccount(account)\n' +
    '       var accountId = getAccountIdOfAccount(account)\n' +
    '       var symbolName = "EUR/USD"\n' +
    '       var arrClose = getData(context, window.chartHandle, DATA_NAME.CLOSE)\n' +
    '       var arrSma = getData(context, window.indiHandle, "sma")\n' +
    '       var ask = getAsk(context, brokerName, accountId, symbolName)\n' +
    '       var bid = getBid(context, brokerName, accountId, symbolName)\n' +
    '       var limitPrice = 0.0003\n' +
    '       var stopPrice = 0.0003\n' +
    '       var volume = 0.01\n' +
    '       if (arrClose[arrClose.length - 3] < arrSma[arrSma.length - 3] && arrClose[arrClose.length - 2] > arrSma[arrSma.length - 2]) {\n' +
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_BUYLIMIT, ask - limitPrice, 0, volume, ask + limitPrice, bid - 3 * stopPrice, "")\n' +
    '       } else if (arrClose[arrClose.length - 3] > arrSma[arrSma.length - 3] && arrClose[arrClose.length - 2] < arrSma[arrSma.length - 2]) {\n' +
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_SELLLIMIT, bid + limitPrice, 0, volume, bid - limitPrice, ask + 3 * stopPrice, "")\n' +
    '       }\n' +
    '    }\n' +
    ')\n'
}),

components: {
        editor: require('vue2-ace-editor'),
    },

    methods: {
        clearForm() {
            this.broker = null;
            this.account = null;
            this.indicator = null;
            this.$refs.form.reset()
        },
        editorInit() {
            require('brace/ext/language_tools') //language extension prerequsite...
            require('brace/mode/html')
            require('brace/mode/javascript') //language
            require('brace/mode/less')
            require('brace/theme/chrome')
            require('brace/snippets/javascript') //snippet
            this.sourceCode = this.template1;
        },
        generateSourceCode() {
            this.sourceCode = this.template1;
        }
    },
};
</script>
