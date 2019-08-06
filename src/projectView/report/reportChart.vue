<template>
    <div class="reportShow" style="line-height: 36px;font-family:LiSu">
        <el-row v-show="this.picStatus">
            <el-col :span="10"
                    style="border-style:solid;border-color: #ffffff rgb(234, 234, 234) #ffffff #ffffff;border-width: 1px;">
                <div style="height: 200px;float:left;">
                    <ve-pie :data="caseChartData" :settings="caseChartSettings" height="200px" width="350px"></ve-pie>
                </div>

                <ol style="margin-top:5px;font-size:14px;line-height:25px" class="remote-line">
                    <li style="font-weight:700;font-size:16px">apis result</li>
                    <li style="font-weight:600;color: rgb(146, 123, 139)">总数:{{this.reportData.stat.teststeps.total}}
                    </li>
                    <li style="color: rgb(25,212,174);font-weight:600">成功:{{this.reportData.stat.teststeps.successes}}
                    </li>
                    <li style="color: rgb(250,110,134);font-weight:600">失败:{{this.reportData.stat.teststeps.failures}}
                    </li>
                    <li style="color: #E87C25;font-weight:600">错误:{{this.reportData.stat.teststeps.errors}}</li>
                </ol>
            </el-col>
            <el-col :span="14" style="border-width: 1px;">
                <div style="height: 200px;float:left;">
                    <ve-ring :data="suiteChartData" :settings="suiteChartSettings" height="200px"
                             width="350px"></ve-ring>
                </div>
                <ol style="margin-top:5px;font-size:14px;line-height:25px" class="remote-line">
                    <li style="font-weight:700;font-size:16px">cases result</li>
                    <li style="font-weight:600;color: rgb(146, 123, 139)">总数:{{this.reportData.stat.testcases.total}}
                    </li>
                    <li style="color: rgb(25,212,174);font-weight:600">成功:{{this.reportData.stat.testcases.success}}
                    </li>
                    <li style="color: rgb(250,110,134);font-weight:600">失败:{{this.reportData.stat.testcases.fail}}</li>
                </ol>
            </el-col>
        </el-row>
    </div>
</template>

<script>

    export default {
        name: 'reportShow',
        data() {
            this.caseChartSettings = {
                radius: 80,
                avoidLabelOverlap: false,
                offsetY: 110,
                itemStyle: {
                    normal: {
                        color: function (params) {
                            let colorList = [
                                'rgb(25,212,174)', 'rgb(250,110,134)', '#FE8463', '#E87C25', '#27727B',
                                '#FE8463', '#9BCA63', '#FAD860', '#F3A43B', '#60C0DD',
                                '#D7504B', '#C6E579', '#F4E001', '#F0805A', '#26C0C0'
                            ];
                            return colorList[params.dataIndex]
                        }
                    }
                },
                label: {
                    normal: {position: 'center', show: false,},
                },
                labelLine: {
                    normal: {show: false}
                },
            };
            this.suiteChartSettings = {
                radius: [50, 80],
                avoidLabelOverlap: false,
                offsetY: 110,
                itemStyle: {
                    normal: {
                        color: function (params) {
                            let colorList = [
                                'rgb(25,212,174)', 'rgb(250,110,134)', '#fb2828', '#E87C25', '#27727B',
                                '#FE8463', '#9BCA63', '#FAD860', '#F3A43B', '#60C0DD',
                                '#D7504B', '#C6E579', '#F4E001', '#F0805A', '#26C0C0'
                            ];
                            return colorList[params.dataIndex]
                        }
                    }
                },
                label: {
                    normal: {position: 'center', show: false,},
                },
                labelLine: {
                    normal: {show: false}
                },
            };
            return {
                reportAddress: '',
                picHeight: '635px',
                picStatus: true,
                active: true,
                showScene: [true, true],
                statusShow: [true, true, true, true, true, true, true, true, true],
                showColor: [],
                attachment: '',
                meta_datas: {data:[{
                        request: {body: null, url: null, headers: null, data: null, params: null, json: null},
                        response: {content: null, json: null,status_code:null}
                    }]},
                caseChartData: {
                    columns: ['caseName', 'num'],
                    rows: [
                        {'caseName': '成功api', num: 0},
                        {"caseName": '失败api', num: 0},
                        {'caseName': '错误api', num: 0},
                    ]
                },
                suiteChartData: {
                    columns: ['name', 'num',],
                    rows: [
                        {'name': '成功case', 'num': 0},
                        {'name': '失败case', 'num': 0},
                    ]
                },
            }
        },

        methods: {
            handleNodeClick(i1, i2) {
                this.showColor = [i1, i2];
                this.meta_datas = this.reportData['details'][i1]['records'][i2]['meta_datas'];
                this.attachment = this.reportData['details'][i1]['records'][i2]['attachment']
            },
            handleCommand(command) {
                // this.showData(command);
                if (command === 'error') {
                    this.showScene = [false, true]
                } else if (command === 'success') {
                    this.showScene = [true, false]
                } else {
                    this.showScene = [true, true]
                }
            },
            optimizeShow(dict) {
                if (dict) {
                    let line='';
                    for (let key in dict) {
                        line = line + key + ':' + dict[key] + '\n'
                    }
                    return line
                }
            },
            showData(state = 'None') {
                this.reportAddress = this.$route.query.reportId;
                this.$axios.post('/api/report/list', {
                    'reportId': this.reportAddress,
                    'state': state,
                }).then((response) => {
                        if (this.messageShow(this, response)) {
                            this.reportData = response['data'];
                            this.meta_datas = this.reportData['details'][0]['records'][0]['meta_datas'];
                            this.attachment = this.reportData['details'][0]['records'][0]['attachment'];
                            this.caseChartData['rows'][0]['num'] = this.reportData.stat.teststeps.successes;
                            this.caseChartData['rows'][1]['num'] = this.reportData.stat.teststeps.failures;
                            this.caseChartData['rows'][2]['num'] = this.reportData.stat.teststeps.errors;
                            this.suiteChartData['rows'][0]['num'] = this.reportData.stat.testcases.success;
                            this.suiteChartData['rows'][1]['num'] = this.reportData.stat.testcases.fail;

                        }
                    }
                )

            },
            hideShowPic(s) {
                if (s) {
                    this.picStatus = true;
                    this.picHeight = '635px';
                } else {
                    this.picStatus = false;
                    this.picHeight = '835px';
                }
            },
        },
        mounted() {
            this.showData();

        },
    }


</script>

<style scoped>
    .el-main {
        line-height: 36px;
    }

    .wire {
        border-top: 1px solid #eee;

    }

    .remote-line {
        list-style-type: none;
    }

    .active {
        background: #f7f7f7;
        font-weight: 600;
    }

    .test-name {
        display: inline-block;
        word-break: break-all;
        font-size: 16px;
        width: 100% !important;
    }

    .test-status {
        text-transform: capitalize;
        font-size: 13px;
        float: right !important;
        margin-right: 20px;
    }

    .grid-content {
        min-height: 36px;
    }

    .content {
        height: auto;
        word-wrap: break-word;
        word-break: break-all;
        overflow: hidden;
        border-bottom: 1px solid #d0d0d0;
    }

    .my-table {
        border-bottom: 1px solid #d0d0d0;
        font-weight: 600;
        color: #66b1ff
    }
</style>