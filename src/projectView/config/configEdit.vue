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
                            <el-button type="primary" size="small" @click="addConfigVariable(configChoice)">添加变量
                            </el-button>
                        </el-form-item>
                    </el-form>

                    <!--<hr style="height:1px;border:none;border-top:1px solid rgb(241, 215, 215);margin-top: -5px"/>-->
                    <hr style="height:1px;border:none;border-top:1px solid rgb(241, 215, 215);margin-top: -10px"/>

                    <el-tabs v-model="configChoice" type="card" @tab-click="handleClick">
                        <el-tab-pane label="测试环境" name="first">
                            <el-table :data="configData.configTest" size="mini" stripe :show-header="false">
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
                                                   @click.native="delConfigVariable('first',scope.$index)">删除
                                        </el-button>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </el-tab-pane>
                        <el-tab-pane label="开发环境" name="second">
                            <el-table :data="configData.configDevelop" size="mini" stripe :show-header="false">
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
                                                   @click.native="delConfigVariable('second',scope.$index)">删除
                                        </el-button>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </el-tab-pane>
                        <el-tab-pane label="线上环境" name="third">
                            <el-table :data="configData.configProduction" size="mini" stripe :show-header="false">
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
                                                   @click.native="delConfigVariable('third', scope.$index)">删除
                                        </el-button>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </el-tab-pane>
                        <el-tab-pane label="备用环境" name="fourth">
                            <el-table :data="configData.configStandby" size="mini" stripe :show-header="false">
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
                                                   @click.native="delConfigVariable('fourth',scope.$index)">删除
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
                configData: {
                    funcAddress: Array(),
                    id: null,
                    num: null,
                    configTest: Array(),
                    configDevelop: Array(),
                    configProduction: Array(),
                    configStandby: Array(),
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
                this.configData.configTest = Array();
                this.configData.configDevelop = Array();
                this.configData.configProduction = Array();
                this.configData.configStandby = Array();
                this.configData.funcAddress = Array();
                this.configData.id = null;
                this.configData.num = null;
                this.configData.modelFormVisible = true;
            },
            addConfigVariable(type) {
                if (type === 'first') {
                    this.configData.configTest.push({key: null, value: null, remark: null});
                }
                if (type === 'second') {
                    this.configData.configDevelop.push({key: null, value: null, remark: null});
                }
                if (type === 'third') {
                    this.configData.configProduction.push({key: null, value: null, remark: null});
                }
                if (type === 'fourth') {
                    this.configData.configStandby.push({key: null, value: null, remark: null});
                }
            },
            delConfigVariable(type, i) {
                if (type === 'first') {
                    this.configData.configTest.splice(i, 1);
                } else if (type === 'second') {
                    this.configData.configDevelop.splice(i, 1);
                } else if (type === 'third') {
                    this.configData.configProduction.splice(i, 1);
                } else if (type === 'fourth') {
                    this.configData.configStandby.splice(i, 1);
                }
            },
            /*addTableRow(type) {
                if (type === 'test') {
                    this.configData.configTest.push({key: null, value: null, remark: null});
                } else if (type === 'develop') {
                    this.configData.configDevelop.push({key: null, value: null, remark: null});
                } else if (type === 'production') {
                    this.configData.configProduction.push({key: null, value: null, remark: null});
                } else if (type === 'standby') {
                    this.configData.configStandby.push({key: null, value: null, remark: null});
                }
            },*/
            addSceneConfig() {
                this.$axios.post(this.$api.addConfigApi, {
                    'projectName': this.configData.projectName,
                    'sceneConfigName': this.configData.name,
                    'funcAddress': this.configData.funcAddress,
                    'num': this.configData.num,
                    'id': this.configData.id,
                    'variables': JSON.stringify(this.configData.configTest),
                    'variables_two': JSON.stringify(this.configData.configDevelop),
                    'variables_three': JSON.stringify(this.configData.configProduction),
                    'variables_four': JSON.stringify(this.configData.configStandby),
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
                        this.configData.configTest = response.data['data']['variables'];
                        this.configData.configDevelop = response.data['data']['variables_two'];
                        this.configData.configProduction = response.data['data']['variables_three'];
                        this.configData.configStandby = response.data['data']['variables_four'];
                        this.configData.funcAddress = response.data['data']['func_address'];
                        this.configData.projectName = this.projectName;
                        this.configData.id = id;
                        this.configData.modelFormVisible = true;
                    }
                )
            },
            handleClick(tab, event) {
                let id = event.target.getAttribute('id');
                if (id == 'tab-first') {
                    this.configChoice = 'first';
                } else if (id == 'tab-second') {
                    this.configChoice = 'second';
                } else if (id == 'tab-third') {
                    this.configChoice = 'third';
                } else if (id == 'tab-fourth') {
                    this.configChoice = 'fourth';
                }
            },
        },
        computed: {
            monitorConfigTest() {
                return this.configData.configTest;
            },
            monitorConfigDevelop() {
                return this.configData.configDevelop;
            },
            monitorConfigProduction() {
                return this.configData.configProduction;
            },
            monitorConfigStandby() {
                return this.configData.configStandby;
            },
        },
        watch: {
            monitorConfigTest: {
                handler: function () {
                    this.configChoice = 'first'
                    if (this.configData.configTest.length === 0) {
                        this.addConfigVariable('first')
                    }
                    if (this.configData.configTest[this.configData.configTest.length - 1]['value']) {
                        this.addConfigVariable('first')
                    }
                },
                deep: true
            },
            monitorConfigDevelop: {
                handler: function () {
                    if (this.configData.configDevelop.length === 0) {
                        this.addConfigVariable('second')
                    }
                    if (this.configData.configDevelop[this.configData.configDevelop.length - 1]['value']) {
                        this.addConfigVariable('second')
                    }
                },
                deep: true
            },
            monitorConfigProduction: {
                handler: function () {
                    if (this.configData.configProduction.length === 0) {
                        this.addConfigVariable('third')
                    }
                    if (this.configData.configProduction[this.configData.configProduction.length - 1]['value']) {
                        this.addConfigVariable('third')
                    }
                },
                deep: true
            },
            monitorConfigStandby: {
                handler: function () {
                    if (this.configData.configStandby.length === 0) {
                        this.addConfigVariable('fourth')
                    }
                    if (this.configData.configStandby[this.configData.configStandby.length - 1]['value']) {
                        this.addConfigVariable('fourth')
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
