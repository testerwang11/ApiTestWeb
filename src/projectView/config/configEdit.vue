<template>
    <div class="configEdit">

        <el-dialog title="配置管理" :visible.sync="configData.modelFormVisible" width="50%">
            <el-tabs value="second">
                <el-tab-pane label="基础信息" name="second" style="margin-top: 10px">
                    <el-form :inline="true" size="small">
                        <el-form-item label="项目名称">
                            <el-select v-model="configData.projectName" placeholder="请选择项目" size="small">
                                <el-option
                                        v-for="(item, key) in proModelData"
                                        :key="key"
                                        :value="key">
                                </el-option>
                            </el-select>
                        </el-form-item>
                        <el-form-item label="配置名称">
                            <el-input v-model="configData.name" size="small">
                            </el-input>
                        </el-form-item>
                    </el-form>
                    <el-form :inline="true" size="small" class="demo-form-inline">
                        <el-form-item label="函数文件">
                            <el-select v-model="configData.funcAddress" multiple placeholder="请选择导入函数文件" size="small">
                                <el-option
                                        v-for="item in this.funcAddress"
                                        :key="item.value"
                                        :label="item.value"
                                        :value="item.value">
                                </el-option>
                            </el-select>
                        </el-form-item>
                        <el-form-item label="配置序号"
                                      v-if="configData.id">
                            <el-input v-model.number="configData.num" size="small">
                            </el-input>
                        </el-form-item>
                        <el-form-item>
                            <el-button type="primary" size="small" @click="addConfigVariable()">添加变量</el-button>
                        </el-form-item>
                    </el-form>

                    <!--<hr style="height:1px;border:none;border-top:1px solid rgb(241, 215, 215);margin-top: -5px"/>-->
                    <hr style="height:1px;border:none;border-top:1px solid rgb(241, 215, 215);margin-top: -10px"/>

                    <el-tabs v-model="configChoice" type="card">
                        <el-tab-pane label="测试环境" name="first">
                            <el-table :data="config.configTest" size="mini" stripe :show-header="false">
                                <el-table-column property="Key" label="Key" header-align="center" minWidth="100">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.key" size="mini" placeholder="key">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column property="Value" label="Value" header-align="center" minWidth="200">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.value" size="mini" placeholder="Value">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column property="备注" label="备注" header-align="center" minWidth="80">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.remark" size="mini" placeholder="备注">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column property="操作" label="操作" header-align="center" width="80">
                                    <template slot-scope="scope">
                                        <el-button type="danger" icon="el-icon-delete" size="mini"
                                                   @click.native="delConfigVariable('test',scope.$index)">删除
                                        </el-button>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </el-tab-pane>
                        <el-tab-pane label="开发环境" name="second">
                            <el-table :data="config.configDevelop" size="mini" stripe :show-header="false">
                                <el-table-column label="Key" header-align="center" minWidth="100">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.key" size="mini" placeholder="key">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column label="Value" header-align="center" minWidth="200">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.value" size="mini" placeholder="Value">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column label="备注" header-align="center" minWidth="80">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.remark" size="mini" placeholder="备注">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column label="操作" header-align="center" width="80">
                                    <template slot-scope="scope">
                                        <el-button type="danger" icon="el-icon-delete" size="mini"
                                                   @click.native="delConfigVariable('develop',scope.$index)">删除
                                        </el-button>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </el-tab-pane>
                        <el-tab-pane label="线上环境" name="third">
                            <el-table :data="config.configDevelop" size="mini" stripe :show-header="false">
                                <el-table-column label="Key" header-align="center" minWidth="100">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.key" size="mini" placeholder="key">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column label="Value" header-align="center" minWidth="200">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.value" size="mini" placeholder="Value">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column label="备注" header-align="center" minWidth="80">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.remark" size="mini" placeholder="备注">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column label="操作" header-align="center" width="80">
                                    <template slot-scope="scope">
                                        <el-button type="danger" icon="el-icon-delete" size="mini"
                                                   @click.native="delConfigVariable('production', scope.$index)">删除
                                        </el-button>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </el-tab-pane>
                        <el-tab-pane label="备用环境" name="fourth">
                            <el-table :data="config.configDevelop" size="mini" stripe :show-header="false">
                                <el-table-column label="Key" header-align="center" minWidth="100">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.key" size="mini" placeholder="key">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column label="Value" header-align="center" minWidth="200">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.value" size="mini" placeholder="Value">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column label="备注" header-align="center" minWidth="80">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.remark" size="mini" placeholder="备注">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column label="操作" header-align="center" width="80">
                                    <template slot-scope="scope">
                                        <el-button type="danger" icon="el-icon-delete" size="mini"
                                                   @click.native="delConfigVariable('standby',scope.$index)">删除
                                        </el-button>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </el-tab-pane>
                    </el-tabs>
                </el-tab-pane>
            </el-tabs>
            <div slot="footer" class="dialog-footer">
                <el-button @click="configData.modelFormVisible = false" size="small">取 消</el-button>
                <el-button type="primary" @click.native="addSceneConfig()" size="small">确 定</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        name: 'configEdit',
        props: ['proModelData', 'funcAddress', 'projectName'],
        data() {
            return {
                configChoice: 'first',
                config: {
                    configTest: [{key: null, value: null, remark: null}],
                    configDevelop: [{key: null, value: null, remark: null}],
                    configProduction: [{key: null, value: null, remark: null}],
                    configStandby: [{key: null, value: null, remark: null}],
                },
                configData: {
                    funcAddress: Array(),
                    id: null,
                    num: null,
                    configTest: Array(),
                    configTwo: Array(),
                    configThree: Array(),
                    configFour: Array(),
                    modelFormVisible: false,
                    projectName: null,
                    name: null,
                    formLabelWidth: '80px',
                }
            }
        },
        methods: {
            initConfigData() {
                this.configData.name = null;
                this.configData.projectName = this.projectName;
                this.config.configTest = Array();
                this.config.configDevelop = Array();
                this.config.configProduction = Array();
                this.config.configStandby = Array();
                this.configData.funcAddress = Array();
                this.configData.id = null;
                this.configData.num = null;
                this.configData.modelFormVisible = true;

            },
            addConfigVariable() {
                if (this.configChoice === 'first') {
                    this.config.configTest.push({key: null, value: null, remark: null});
                }
                if (this.configChoice === 'second') {
                    this.config.configDevelop.push({key: null, value: null, remark: null});
                }
                if (this.configChoice === 'third') {
                    this.config.configProduction.push({key: null, value: null, remark: null});
                }
                if (this.configChoice === 'fourth') {
                    this.config.configStandby.push({key: null, value: null, remark: null});
                }
            },
            delConfigVariable(type, i) {
                if (type === 'test') {
                    this.config.configTest.splice(i, 1);
                } else if (type === 'develop') {
                    this.config.configDevelop.splice(i, 1);
                } else if (type === 'production') {
                    this.config.configProduction.splice(i, 1);
                } else if (type === 'standby') {
                    this.config.configStandby.splice(i, 1);
                }
            },
            dealConfigList(data) {
                // 把[{value:xxx1},{value:xxx2}] 转为 [xxx1,xxx2]111
                let config = Array();
                for (let i = 0; i < data.length; i++) {
                    if (data[i].value) {
                        config.push(data[i].value);
                    }
                }
                return config
            },

            dealConfifDict(data) {
                // 把[xxx1,xxx2] 转为 [{value:xxx1},{value:xxx2}]
                let config = Array();
                if (!data) {
                    return config
                }
                for (let i = 0; i < data.length; i++) {
                    config.push({value: data[i]});
                }
                return config
            },
            addTableRow(type) {
                if (type === 'test') {
                    this.environment.environmentTest.push({value: ''});
                } else if (type === 'develop') {
                    this.environment.environmentDevelop.push({value: ''});
                } else if (type === 'production') {
                    this.environment.environmentProduction.push({value: ''});
                } else if (type === 'standby') {
                    this.environment.environmentStandby.push({value: ''});
                }
            },
            addSceneConfig() {
                this.$axios.post(this.$api.addConfigApi, {
                    'projectName': this.configData.projectName,
                    'sceneConfigName': this.configData.name,
                    'funcAddress': this.configData.funcAddress,
                    'num': this.configData.num,
                    'id': this.configData.id,
                    'variables': JSON.stringify(this.configData.configTest),
                    'variables_two': this.dealConfigList(this.config.configDevelop),
                    'variables_three': this.dealConfigList(this.config.configProduction),
                    'variables_four': this.dealConfigList(this.config.configStandby),
                }).then((response) => {
                        if (this.messageShow(this, response)) {
                            this.configData.modelFormVisible = false;
                            if (typeof this.$parent.findConfig === "function") {
                                this.$parent.findConfig();
                            }

                            // this.findConfig();
                        }
                    }
                )
            },
            editSceneConfig(id) {
                if (!id) {
                    this.$message({
                        showClose: true,
                        message: '请选择配置',
                        type: 'warning',
                    });
                    // if(response.data['error']){
                    //     return 'error'
                    // }
                    return
                }
                this.$axios.post(this.$api.editConfigApi, {'id': id}).then((response) => {
                        this.configData.name = response.data['data']['name'];
                        this.configData.num = response.data['data']['num'];
                        this.configData.variable = response.data['data']['variables'];
                        this.config.configTest = this.dealConfifDict(response.data['data']['variables']);
                        this.config.configDevelop = this.dealConfifDict(response.data['data']['variables_two']);
                        this.config.configProduction = this.dealConfifDict(response.data['data']['variables_three']);
                        this.config.configStandby = this.dealConfifDict(response.data['data']['variables_four']);

                        this.configData.funcAddress = response.data['data']['func_address'];
                        this.configData.projectName = this.projectName;
                        this.configData.id = id;
                        this.configData.modelFormVisible = true;
                    }
                )
            },
        },
        watch: {
            monitorConfigTest: {
                handler: function () {
                    if (this.config.configTest.length === 0) {
                        this.addConfigVariable('one')
                    }
                    if (this.config.configTest[this.config.configTest.length - 1]['value']) {
                        this.addConfigVariable('one')
                    }
                },
                deep: true
            },
            monitorConfigDevelop: {
                handler: function () {
                    if (this.config.configDevelop.length === 0) {
                        this.addConfigVariable('two')
                    }
                    if (this.config.configDevelop[this.config.configDevelop.length - 1]['value']) {
                        this.addConfigVariable('two')
                    }
                },
                deep: true
            },
            monitorConfigProduction: {
                handler: function () {
                    if (this.config.configProduction.length === 0) {
                        this.addConfigVariable('three')
                    }
                    if (this.config.configProduction[this.config.configProduction.length - 1]['value']) {
                        this.addConfigVariable('three')
                    }
                },
                deep: true
            },
            monitorConfigStandby: {
                handler: function () {
                    if (this.config.configStandby.length === 0) {
                        this.addConfigVariable('four')
                    }
                    if (this.config.configStandby.length[this.config.configStandby.length - 1]['value']) {
                        this.addConfigVariable('four')
                    }
                },
                deep: true
            },
        },
        mounted() {
        },
    }
</script>
<style>
</style>
