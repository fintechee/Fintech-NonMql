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
                        <v-text-field label="Broker(Optional, Default: Current Broker)" placeholder="Broker" v-model="broker" :rules="brokerRules"></v-text-field>
                        <v-text-field label="Account(Optional, Default: Current Account)" placeholder="Account" v-model="account" :rules="accountRules"></v-text-field>
                        <v-select
                          :items="instrumentNames"
                          label="Instrument"
                          v-model="instrument"
                          :rules="instrumentRules"
                        ></v-select>
                        <v-select
                          :items="timeFrames"
                          label="Time Frame"
                          v-model="timeFrame"
                          :rules="timeFrameRules"
                        ></v-select>
                        <v-select
                          :items="indicatorNames"
                          label="Indicator"
                          v-model="indicator"
                          :rules="indicatorRules"
                        ></v-select>
                        <v-select
                          :items="templates"
                          label="Template"
                          v-model="template"
                          :rules="templateRules"
                          @change="changeTemplate"
                        ></v-select>
                        <v-img
                          :src="featuredPic"
                          height="195"
                          v-if="featuredPic"
                          style="margin-bottom:10px"
                        ></v-img>

                        <v-btn text @click="clearForm()">Reset</v-btn>
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
            instrument: 'EUR/USD',
            timeFrame: 'TIME_FRAME.M1',
            indicator: 'sma',
            template: '1',
            featuredPic: '/images/template1.png',
            brokerRules: [
                v => typeof v == 'undefined' || v == null || v == '' || v.length <= 20 || 'Too many characters.(Limit: 20 characters)'
            ],
            accountRules: [
                v => typeof v == 'undefined' || v == null || v == '' || v.length <= 20 || 'Too many characters.(Limit: 20 characters)'
            ],
            instrumentRules: [
                v => v != '' || 'Instrument is required'
            ],
            timeFrameRules: [
                v => v != '' || 'Time Frame is required'
            ],
            indicatorRules: [
                v => v != '' || 'Indicator is required'
            ],
            templateRules: [
                v => v != '' || 'Template is required'
            ],

            sourceCode: '',

            instrumentNames: [{
                text: 'EUR/USD',
                value: 'EUR/USD'
            }, {
                text: 'GBP/USD',
                value: 'GBP/USD'
            }, {
                text: 'USD/JPY',
                value: 'USD/JPY'
            }],

            timeFrames: [{
                text: 'M1',
                value: 'TIME_FRAME.M1'
            }, {
                text: 'M5',
                value: 'TIME_FRAME.M5'
            }, {
                text: 'M15',
                value: 'TIME_FRAME.M15'
            }, {
                text: 'M30',
                value: 'TIME_FRAME.M30'
            }, {
                text: 'H1',
                value: 'TIME_FRAME.H1'
            }, {
                text: 'H4',
                value: 'TIME_FRAME.H4'
            }],

            indicatorNames: [{
                text: 'Simple Moving Average',
                value: 'sma'
            }, {
                text: 'Relative Strength Index',
                value: 'rsi'
            }],
            indicatorMap: [],
            templates: [{
                text: 'Close Price vs Indicator',
                value: '1',
            }, {
                text: 'Indicator vs Constant Number',
                value: '2'
            }],
            templateMap: [],
}),

    components: {
        editor: require('vue2-ace-editor'),
    },
    created() {
        this.init();
    },

    methods: {
        init() {
            this.indicatorMap['sma'] = {
                name: 'sma',
                parameters:
                '   [{ // parameters\n' +
                '       name: "period",\n' +
                '       value: 20,\n' +
                '       required: true,\n' +
                '       type: PARAMETER_TYPE.INTEGER,\n' +
                '       range: [1, 100]\n' +
                '   }],\n',
                getParameters:
                '       var period = getEAParameter(context, "period")\n',
                setParameters:
                '       [{\n' +
                '           name: "period",\n' +
                '           value: period\n' +
                '       }])\n',
            }

            this.indicatorMap['rsi'] = {
                name: 'rsi',
                parameters:
                '   [{ // parameters\n' +
                '       name: "period",\n' +
                '       value: 14,\n' +
                '       required: true,\n' +
                '       type: PARAMETER_TYPE.INTEGER,\n' +
                '       range: [1, 100]\n' +
                '   }, { // parameters\n' +
                '       name: "constant",\n' +
                '       value: 20,\n' +
                '       required: true,\n' +
                '       type: PARAMETER_TYPE.INTEGER,\n' +
                '       range: [1, 100]\n' +
                '   }],\n',
                getParameters:
                '       var period = getEAParameter(context, "period")\n' +
                '       var constantNum = getEAParameter(context, "constant")\n',
                setParameters:
                '       [{\n' +
                '           name: "period",\n' +
                '           value: period\n' +
                '       }])\n',
            }

            this.templateMap['1'] = {
                featuredPic: '/images/template1.png',
                sourceCode:
    'registerEA(\n' +
    '   "sample_using_[indicator]",\n' +
    '   "A test EA based on [indicator]",\n' +
        '[parameters]' +
    '   function (context) { // Init()\n' +
    '       var account = getAccount(context, 0)\n' +
    '       var brokerName = [broker]\n' +
    '       var accountId = [account]\n' +
    '       var symbolName = [instrument]\n' +
    '       getQuotes(context, brokerName, accountId, symbolName)\n' +
    '       window.chartHandle = getChartHandle(context, brokerName, accountId, symbolName, [timeFrame])\n' +
            '[getParameters]' +
    '       window.indiHandle = getIndicatorHandle(context, brokerName, accountId, symbolName, [timeFrame], "[indicator]",\n' +
            '[setParameters]' +
    '   },\n' +
    '   function(context) { // Deinit()\n' +
    '       delete window.currTime\n' +
    '   },\n' +
    '   function(context) { // OnTick()\n' +
    '       var arrTime = getData(context, window.chartHandle, DATA_NAME.TIME)\n' +
    '       if (typeof window.currTime == "undefined") {\n' +
    '           window.currTime = arrTime[arrTime.length - 1]\n' +
    '       } else if (window.currTime != arrTime[arrTime.length - 1]) {\n' +
    '           window.currTime = arrTime[arrTime.length - 1]\n' +
    '       } else {\n' +
    '           return\n' +
    '       }\n' +
    '       var account = getAccount(context, 0)\n' +
    '       var brokerName = [account]\n' +
    '       var accountId = [account]\n' +
    '       var symbolName = [instrument]\n' +
    '       var arrClose = getData(context, window.chartHandle, DATA_NAME.CLOSE)\n' +
    '       var arrIndi = getData(context, window.indiHandle, "[indicator]")\n' +
    '       var ask = getAsk(context, brokerName, accountId, symbolName)\n' +
    '       var bid = getBid(context, brokerName, accountId, symbolName)\n' +
    '       var limitPrice = 0.0003\n' +
    '       var stopPrice = 0.0003\n' +
    '       var volume = 0.01\n' +
    '       if (arrClose[arrClose.length - 3] < arrIndi[arrIndi.length - 3] && arrClose[arrClose.length - 2] > arrIndi[arrIndi.length - 2]) {\n' +
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_BUYLIMIT, ask - limitPrice, 0, volume, ask + limitPrice, bid - 3 * stopPrice, "")\n' +
    '       } else if (arrClose[arrClose.length - 3] > arrIndi[arrIndi.length - 3] && arrClose[arrClose.length - 2] < arrIndi[arrIndi.length - 2]) {\n' +
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_SELLLIMIT, bid + limitPrice, 0, volume, bid - limitPrice, ask + 3 * stopPrice, "")\n' +
    '       }\n' +
    '    }\n' +
    ')\n'
            }

            this.templateMap['2'] = {
                featuredPic: '/images/template2.png',
                sourceCode:
    'registerEA(\n' +
    '   "sample_using_[indicator]",\n' +
    '   "A test EA based on [indicator]",\n' +
        '[parameters]' +
    '   function (context) { // Init()\n' +
    '       var account = getAccount(context, 0)\n' +
    '       var brokerName = [broker]\n' +
    '       var accountId = [account]\n' +
    '       var symbolName = [instrument]\n' +
    '       getQuotes(context, brokerName, accountId, symbolName)\n' +
    '       window.chartHandle = getChartHandle(context, brokerName, accountId, symbolName, [timeFrame])\n' +
            '[getParameters]' +
    '       window.indiHandle = getIndicatorHandle(context, brokerName, accountId, symbolName, [timeFrame], "[indicator]",\n' +
            '[setParameters]' +
    '   },\n' +
    '   function(context) { // Deinit()\n' +
    '       delete window.currTime\n' +
    '   },\n' +
    '   function(context) { // OnTick()\n' +
    '       var arrTime = getData(context, window.chartHandle, DATA_NAME.TIME)\n' +
    '       if (typeof window.currTime == "undefined") {\n' +
    '           window.currTime = arrTime[arrTime.length - 1]\n' +
    '       } else if (window.currTime != arrTime[arrTime.length - 1]) {\n' +
    '           window.currTime = arrTime[arrTime.length - 1]\n' +
    '       } else {\n' +
    '           return\n' +
    '       }\n' +
    '       var account = getAccount(context, 0)\n' +
    '       var brokerName = [account]\n' +
    '       var accountId = [account]\n' +
    '       var symbolName = [instrument]\n' +
    '       var arrClose = getData(context, window.chartHandle, DATA_NAME.CLOSE)\n' +
    '       var arrIndi = getData(context, window.indiHandle, "[indicator]")\n' +
            '[getParameters]' +
    '       var ask = getAsk(context, brokerName, accountId, symbolName)\n' +
    '       var bid = getBid(context, brokerName, accountId, symbolName)\n' +
    '       var limitPrice = 0.0003\n' +
    '       var stopPrice = 0.0003\n' +
    '       var volume = 0.01\n' +
    '       if (constantNum < arrIndi[arrIndi.length - 3] && constantNum > arrIndi[arrIndi.length - 2]) {\n' +
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_BUYLIMIT, ask - limitPrice, 0, volume, ask + limitPrice, bid - 3 * stopPrice, "")\n' +
    '       } else if ((100 - constantNum) > arrIndi[arrIndi.length - 3] && (100 - constantNum) < arrIndi[arrIndi.length - 2]) {\n' +
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_SELLLIMIT, bid + limitPrice, 0, volume, bid - limitPrice, ask + 3 * stopPrice, "")\n' +
    '       }\n' +
    '    }\n' +
    ')\n'
            }

        },
        clearForm() {
            this.broker = null;
            this.account = null;
            this.$refs.form.reset();
            this.instrument = 'EUR/USD';
            this.timeFrame = 'TIME_FRAME.M1';
            this.indicator = 'sma';
            this.template = '1';
            this.featuredPic = '/images/template1.png';
        },
        editorInit() {
            require('brace/ext/language_tools') //language extension prerequsite...
            require('brace/mode/html')
            require('brace/mode/javascript') //language
            require('brace/mode/less')
            require('brace/theme/chrome')
            require('brace/snippets/javascript') //snippet
        },
        changeTemplate(v) {
            this.featuredPic = this.templateMap[v].featuredPic;
        },
        generateSourceCode() {
            let broker = !this.broker ? 'getBrokerNameOfAccount(account)' : '"' + this.broker + '"';
            let account = !this.account ? 'getAccountIdOfAccount(account)' : '"' + this.account + '"';
            let instrument = '"' + this.instrument + '"';
            let timeFrame = this.timeFrame;
            let indicator = this.indicatorMap[this.indicator];
            let sourceCode = this.templateMap[this.template].sourceCode;

            this.sourceCode = sourceCode
                .replace(/\[broker\]/g, broker)
                .replace(/\[account\]/g, account)
                .replace(/\[instrument\]/g, instrument)
                .replace(/\[timeFrame\]/g, timeFrame)
                .replace(/\[indicator\]/g, indicator.name)
                .replace(/\[parameters\]/g, indicator.parameters)
                .replace(/\[getParameters\]/g, indicator.getParameters)
                .replace(/\[setParameters\]/g, indicator.setParameters);
        }
    },
};
</script>
