<template>
<v-container style="background:white">
    <v-layout text-center wrap>
        <v-flex xs12>
            <v-img :src="require('../assets/newscreenshot1.png')" class="my-3" contain height="200"></v-img>
        </v-flex>

        <v-flex mb-4>
            <h1 class="display-2 font-weight-bold mb-3">
                Welcome to NonMQL Expert Advisor Studio
            </h1>
            <h3 class="font-weight-bold mb-3">
                Powered by Fintechee
            </h3>
            <p class="subheading font-weight-regular">
                For help and collaboration with other Fintechee developers,
                <br>please join our online
                <a href="https://twitter.com/Fintechee1" target="_blank">Fintechee Twitter Community</a>.
                <br>You can run Expert Advisor(EA) on our WEB Trader, a Forex trading platform
                <a href="https://wwww.fintechee.com/web-trader/" target="_blank">Fintechee(the best Forex trading platform)</a>.
                <br>Please refer to our tutorials to know more details
                <a href="https://wwww.fintechee.com/tutorial-for-forex-trading/" target="_blank">Fintechee Tutorials</a>.
                <br>Or check our SDK documents
                <a href="https://wwww.fintechee.com/sdk-trading/" target="_blank">Fintechee SDK</a>.
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
                text: 'Exponential Moving Average',
                value: 'ema'
            }, {
                text: 'Smoothed Moving Average',
                value: 'smma'
            }, {
                text: 'Linear weighted Moving Average',
                value: 'lwma'
            }, {
                text: 'Relative Strength Index',
                value: 'rsi'
            }, {
                text: 'MACD',
                value: 'macd'
            }, {
                text: 'Non-Indicator',
                value: 'nonindicator'
            }],
            indicatorMap: [],
            templates: [{
                text: 'Close Price vs Indicator',
                value: '1',
            }, {
                text: 'Indicator vs Constant Number',
                value: '2'
            }, {
                text: 'Main vs Signal',
                value: '3'
            }, {
                text: 'Candlesticks with the Same Color',
                value: '4'
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

            this.indicatorMap['ema'] = {
                name: 'ema',
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

            this.indicatorMap['smma'] = {
                name: 'smma',
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

            this.indicatorMap['lwma'] = {
                name: 'lwma',
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
                '   }, {\n' +
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

            this.indicatorMap['macd'] = {
                name: 'macd',
                parameters:
                '   [{ // parameters\n' +
                '       name: "fasteEMA",\n' +
                '       value: 12,\n' +
                '       required: true,\n' +
                '       type: PARAMETER_TYPE.INTEGER,\n' +
                '       range: [1, 100]\n' +
                '   }, {\n' +
                '       name: "slowEMA",\n' +
                '       value: 26,\n' +
                '       required: true,\n' +
                '       type: PARAMETER_TYPE.INTEGER,\n' +
                '       range: [1, 100]\n' +
                '   }, {\n' +
                '       name: "signalSMA",\n' +
                '       value: 9,\n' +
                '       required: true,\n' +
                '       type: PARAMETER_TYPE.INTEGER,\n' +
                '       range: [1, 100]\n' +
                '   }],\n',
                getParameters:
                '       var fasteEMA = getEAParameter(context, "fasteEMA")\n' +
                '       var slowEMA = getEAParameter(context, "slowEMA")\n' +
                '       var signalSMA = getEAParameter(context, "signalSMA")\n',
                setParameters:
                '       [{\n' +
                '           name: "fasteEMA",\n' +
                '           value: fasteEMA\n' +
                '       },{\n' +
                '           name: "slowEMA",\n' +
                '           value: slowEMA\n' +
                '       },{\n' +
                '           name: "signalSMA",\n' +
                '           value: signalSMA\n' +
                '       }])\n',
            }

            this.indicatorMap['nonindicator'] = {
                name: 'nonindicator',
                parameters:
                '   [{ // parameters\n' +
                '       name: "period",\n' +
                '       value: 3,\n' +
                '       required: true,\n' +
                '       type: PARAMETER_TYPE.INTEGER,\n' +
                '       range: [1, 100]\n' +
                '   }],\n',
                getParameters:
                '       var period = getEAParameter(context, "period")\n',
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
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_BUYLIMIT, ask - limitPrice, 0, volume, ask + limitPrice, bid - 3 * stopPrice, "", 0, 0)\n' +
    '       } else if (arrClose[arrClose.length - 3] > arrIndi[arrIndi.length - 3] && arrClose[arrClose.length - 2] < arrIndi[arrIndi.length - 2]) {\n' +
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_SELLLIMIT, bid + limitPrice, 0, volume, bid - limitPrice, ask + 3 * stopPrice, "", 0, 0)\n' +
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
    '       var arrIndi = getData(context, window.indiHandle, "[indicator]")\n' +
            '[getParameters]' +
    '       var ask = getAsk(context, brokerName, accountId, symbolName)\n' +
    '       var bid = getBid(context, brokerName, accountId, symbolName)\n' +
    '       var limitPrice = 0.0003\n' +
    '       var stopPrice = 0.0003\n' +
    '       var volume = 0.01\n' +
    '       if (constantNum < arrIndi[arrIndi.length - 3] && constantNum > arrIndi[arrIndi.length - 2]) {\n' +
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_BUYLIMIT, ask - limitPrice, 0, volume, ask + limitPrice, bid - 3 * stopPrice, "", 0, 0)\n' +
    '       } else if ((100 - constantNum) > arrIndi[arrIndi.length - 3] && (100 - constantNum) < arrIndi[arrIndi.length - 2]) {\n' +
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_SELLLIMIT, bid + limitPrice, 0, volume, bid - limitPrice, ask + 3 * stopPrice, "", 0, 0)\n' +
    '       }\n' +
    '    }\n' +
    ')\n'
            }

            this.templateMap['3'] = {
                featuredPic: '/images/template3.png',
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
    '       var arrMain = getData(context, window.indiHandle, "main")\n' +
    '       var arrSignal = getData(context, window.indiHandle, "signal")\n' +
    '       var ask = getAsk(context, brokerName, accountId, symbolName)\n' +
    '       var bid = getBid(context, brokerName, accountId, symbolName)\n' +
    '       var limitPrice = 0.0003\n' +
    '       var stopPrice = 0.0003\n' +
    '       var volume = 0.01\n' +
    '       if (arrMain[arrMain.length - 3] < arrSignal[arrSignal.length - 3] && arrMain[arrMain.length - 2] > arrSignal[arrSignal.length - 2]) {\n' +
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_BUYLIMIT, ask - limitPrice, 0, volume, ask + limitPrice, bid - 3 * stopPrice, "", 0, 0)\n' +
    '       } else if (arrMain[arrMain.length - 3] > arrSignal[arrSignal.length - 3] && arrMain[arrMain.length - 2] < arrSignal[arrSignal.length - 2]) {\n' +
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_SELLLIMIT, bid + limitPrice, 0, volume, bid - limitPrice, ask + 3 * stopPrice, "", 0, 0)\n' +
    '       }\n' +
    '    }\n' +
    ')\n'
            }

            this.templateMap['4'] = {
                featuredPic: '/images/template4.png',
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
            '[getParameters]' +
    '       var arrClose = getData(context, window.chartHandle, DATA_NAME.CLOSE)\n' +
    '       var arrOpen = getData(context, window.chartHandle, DATA_NAME.OPEN)\n' +
    '       var ask = getAsk(context, brokerName, accountId, symbolName)\n' +
    '       var bid = getBid(context, brokerName, accountId, symbolName)\n' +
    '       var limitPrice = 0.0003\n' +
    '       var stopPrice = 0.0003\n' +
    '       var volume = 0.01\n' +
    '       var buyOrSell = "neither"\n' +
    '       for (var i = 1; i <= period; i++) {\n' +
    '           if (arrClose[arrClose.length - i] - arrOpen[arrOpen.length - i] > 0 && (buyOrSell == "buy" || i == 1)) {\n' +
    '               buyOrSell = "buy"\n' +
    '           } else if (arrClose[arrClose.length - i] - arrOpen[arrOpen.length - i] < 0 && (buyOrSell == "sell" || i == 1)) {\n' +
    '               buyOrSell = "sell"\n' +
    '           } else {\n' +
    '               buyOrSell = "neither"\n' +
    '               break\n' +
    '           }\n' +
    '       }\n' +
    '       if (buyOrSell == "buy") {\n' +
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_BUYLIMIT, ask - limitPrice, 0, volume, ask + limitPrice, bid - 3 * stopPrice, "", 0, 0)\n' +
    '       } else if (buyOrSell == "sell") {\n' +
    '           sendOrder(brokerName, accountId, symbolName, ORDER_TYPE.OP_SELLLIMIT, bid + limitPrice, 0, volume, bid - limitPrice, ask + 3 * stopPrice, "", 0, 0)\n' +
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
