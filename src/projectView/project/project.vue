<template>
    <div class="projectManage">

        <el-form :inline="true" class="demo-form-inline search-style" size="small">

            <el-form-item label="项目名称" labelWidth="110px">
                <el-input placeholder="请输入" v-model="form.projectName">
                </el-input>
            </el-form-item>
            <el-form-item>
                <el-button type="primary" icon="el-icon-search" @click.native="proHandleCurrentChange(1)">
                    搜索
                </el-button>
                <el-button type="primary" @click.native="initProjectData()">添加项目
                </el-button>
            </el-form-item>

        </el-form>
        <el-tabs value="first" style="padding-left: 10px">
            <el-tab-pane label="项目列表" name="first" class="table_padding">

                <el-table :data="tableData" stripe max-height="745">
                    <el-table-column
                            prop="id"
                            label="id"
                            width="80"
                    >
                    </el-table-column>
                    <el-table-column
                            prop="name"
                            label="项目名称"
                            width="150">
                    </el-table-column>
                    <!--<el-table-column label="当前环境">
                        <template slot-scope="scope">
                            <el-tag size="small"
                                    :type="tableData[scope.$index]['choice'] === 'first' ?
                                    'success' : tableData[scope.$index]['choice'] === 'second' ?
                                     'primary' :  tableData[scope.$index]['choice'] === 'third' ? 'warning' : 'danger'">
                                {{environmentShow(tableData[scope.$index]['choice'])}}
                            </el-tag>

                        </template>
                    </el-table-column>-->
                    <!--<el-table-column-->
                    <!--prop="host_two"-->
                    <!--label="基础url2"-->
                    <!--&gt;-->
                    <!--</el-table-column>-->
                    <el-table-column
                            prop="principal2"
                            label="负责人"
                            width="200">
                    </el-table-column>
                    <el-table-column
                            prop="createTime"
                            label="创建时间"
                            width="200">
                    </el-table-column>
                    <el-table-column
                            prop="updateTime"
                            label="更新时间"
                            width="200">
                    </el-table-column>
                    <el-table-column
                            label="操作"
                    >
                        <template slot-scope="scope">
                            <el-button type="primary" icon="el-icon-edit" size="mini"
                                       @click.native="editProject(tableData[scope.$index]['id'])">编辑
                            </el-button>
                            <el-button type="danger" icon="el-icon-delete" size="mini"
                                       @click.native="sureView(delProject,tableData[scope.$index]['id'],tableData[scope.$index]['name'])">
                                删除
                            </el-button>
                        </template>
                    </el-table-column>
                </el-table>

                <div class="pagination">
                    <el-pagination
                            @current-change="proHandleCurrentChange"
                            @size-change="proHandleSizeChange"
                            :current-page="currentPage"
                            :page-size="sizePage"
                            layout="total, sizes, prev, pager, next, jumper"
                            :total="this.total">
                    </el-pagination>
                </div>
            </el-tab-pane>
        </el-tabs>

        <el-dialog title="项目配置" :visible.sync="projectData.modelFormVisible" width="40%">
            <el-tabs>
                <el-tab-pane label="基础信息" style="margin-top: 10px">
                    <el-form :inline="true">
                        <el-form-item label="项目名称" :label-width="projectData.formLabelWidth">
                            <el-input v-model="projectData.projectName" size="mini" id="project_name"
                                      style="width: 150px">
                            </el-input>
                        </el-form-item>
                        <el-form-item label="负责人" label-width="60px">
                            <el-select v-model="form.users" multiple size="mini"
                                       style="width: 150px">
                                <el-option
                                        v-for="item in userData"
                                        :key="item.id"
                                        :label="item.label"
                                        :value="item.id">
                                </el-option>
                            </el-select>
                        </el-form-item>
<!--                        <el-form-item label="函数文件" :label-width="projectData.formLabelWidth">
                            <el-select v-model="projectData.funcFile" placeholder="请选择导入函数文件" size="mini" clearable>
                                <el-option
                                        v-for="item in funcAddress"
                                        :key="item.value"
                                        :label="item.value"
                                        :value="item.value">
                                </el-option>
                            </el-select>
                        </el-form-item>-->

                    </el-form>
                    <hr style="height:1px;border:none;border-top:1px solid rgb(241, 215, 215);margin-top: -10px"/>
                    <el-tabs v-model="environmentChoice" type="card">
                        <el-tab-pane label="测试环境" name="first">
                            <el-table :data="environment.environmentTest" size="mini" stripe :show-header="false">
                                <el-table-column property="value" label="Value" header-align="center" minWidth="200">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.value" size="mini"
                                                  placeholder="http://127.0.0.1:8010">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column property="value" label="操作" header-align="center" width="50">
                                    <template slot-scope="scope">
                                        <el-button type="danger" icon="el-icon-delete" size="mini"
                                                   @click.native="delTableRow('test',scope.$index)">
                                        </el-button>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </el-tab-pane>
                        <el-tab-pane label="开发环境" name="second">
                            <el-table :data="environment.environmentDevelop" size="mini" stripe :show-header="false">
                                <el-table-column property="value" label="Value" header-align="center" minWidth="200">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.value" size="mini"
                                                  placeholder="http://127.0.0.1:8010">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column property="value" label="操作" header-align="center" width="50">
                                    <template slot-scope="scope">
                                        <el-button type="danger" icon="el-icon-delete" size="mini"
                                                   @click.native="delTableRow('develop',scope.$index)">
                                        </el-button>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </el-tab-pane>
                        <el-tab-pane label="线上环境" name="third">
                            <el-table :data="environment.environmentProduction" size="mini" stripe :show-header="false">
                                <el-table-column property="value" label="Value" header-align="center" minWidth="200">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.value" size="mini"
                                                  placeholder="http://127.0.0.1:8010">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column property="value" label="操作" header-align="center" width="50">
                                    <template slot-scope="scope">
                                        <el-button type="danger" icon="el-icon-delete" size="mini"
                                                   @click.native="delTableRow('production',scope.$index)">
                                        </el-button>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </el-tab-pane>
                        <el-tab-pane label="备用环境" name="fourth">
                            <el-table :data="environment.environmentStandby" size="mini" stripe :show-header="false">
                                <el-table-column property="value" label="Value" header-align="center" minWidth="200">
                                    <template slot-scope="scope">
                                        <el-input v-model="scope.row.value" size="mini"
                                                  placeholder="http://127.0.0.1:8010">
                                        </el-input>
                                    </template>
                                </el-table-column>
                                <el-table-column property="value" label="操作" header-align="center" width="50">
                                    <template slot-scope="scope">
                                        <el-button type="danger" icon="el-icon-delete" size="mini"
                                                   @click.native="delTableRow('standby',scope.$index)">
                                        </el-button>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </el-tab-pane>
                    </el-tabs>
                </el-tab-pane>

                <el-tab-pane label="公用变量" style="margin-top: 10px">
                    <span style="margin-left: 10px">变量信息：</span>
                    <el-button type="primary" size="mini" @click="addProjectVariable()">
                        添加
                    </el-button>
                    <el-table :data="projectData.variable" stripe :show-header="false" size="mini">
                        <el-table-column label="Key" header-align="center" minWidth="50">
                            <template slot-scope="scope">
                                <el-input v-model="scope.row.key" size="mini" placeholder="key">
                                </el-input>
                            </template>
                        </el-table-column>
                        <el-table-column label="Value" header-align="center" minWidth="80">
                            <template slot-scope="scope">
                                <el-input v-model="scope.row.value" size="mini" placeholder="value">
                                </el-input>
                            </template>
                        </el-table-column>
                        <el-table-column label="备注" header-align="center" width="150">
                            <template slot-scope="scope">
                                <el-input v-model="scope.row.remark" size="mini" placeholder="备注">
                                </el-input>
                            </template>
                        </el-table-column>
                        <el-table-column label="操作" header-align="center" width="80">
                            <template slot-scope="scope">
                                <el-button type="danger" icon="el-icon-delete" size="mini"
                                           @click.native="delProjectVariable(scope.$index)">删除
                                </el-button>
                            </template>
                        </el-table-column>
                    </el-table>
                    <hr style="height:1px;border:none;border-top:1px solid rgb(241, 215, 215);"/>

                    <!--<div style="margin-top: 10px">-->
                    <!--<span style="margin-left: 10px">头部信息：</span>-->
                    <!--<el-button type="primary" size="mini" @click="addProjectHeader()">添加</el-button>-->
                    <!--</div>-->
                    <!--<el-table :data="projectData.header" stripe :show-header="false">-->
                    <!--<el-table-column label="Key" header-align="center" minWidth="50">-->
                    <!--<template slot-scope="scope">-->
                    <!--<el-input v-model="scope.row.key" size="mini" placeholder="key">-->
                    <!--</el-input>-->
                    <!--</template>-->
                    <!--</el-table-column>-->
                    <!--<el-table-column label="Value" header-align="center" minWidth="80">-->
                    <!--<template slot-scope="scope">-->
                    <!--<el-input v-model="scope.row.value" size="mini" placeholder="value">-->
                    <!--</el-input>-->
                    <!--</template>-->
                    <!--</el-table-column>-->
                    <!--<el-table-column label="操作" header-align="center" width="80">-->
                    <!--<template slot-scope="scope">-->
                    <!--<el-button type="danger" icon="el-icon-delete" size="mini"-->
                    <!--@click.native="delProjectHeader(scope.$index)">删除-->
                    <!--</el-button>-->
                    <!--</template>-->
                    <!--</el-table-column>-->
                    <!--</el-table>-->
                </el-tab-pane>

            </el-tabs>

            <div slot="footer" class="dialog-footer">
                <el-button @click="projectData.modelFormVisible = false" size="small">取 消</el-button>
                <el-button type="primary" id="sure_btn"
                           @click.native="addProjectBtn()" size="small">确 定
                </el-button>
            </div>
        </el-dialog>

    </div>
</template>

<script>

    export default {
        name: 'projectManage',
        data() {
            return {
                environmentChoice: 'first',
                environment: {
                    environmentTest: [{value: ''}],
                    environmentDevelop: [{value: ''}],
                    environmentProduction: [{value: ''}],
                    environmentStandby: [{value: ''}],
                },
                tableData: Array(),
                total: 1,
                funcAddress: '',
                userData: [],
                currentPage: 1,
                sizePage: 20,
                form: {
                    projectName: null,
                    /*user: {
                        user_name: null,
                        user_id: null,
                    },*/
                    users: Array(),
                },
                projectData: {
                    host: null,
                    hostTwo: null,
                    hostThree: null,
                    hostFour: null,
                    id: null,
                    modelFormVisible: false,
                    projectName: null,
                    principal: null,
                    formLabelWidth: '80px',
                    funcFile: '',
                    header: Array(),
                    variable: Array(),
                },
            }
        },
        methods: {
            proHandleCurrentChange(val) {
                this.currentPage = val;
                this.findProject()
            },
            proHandleSizeChange(val) {
                this.sizePage = val;
                this.findProject()
            },

            findProject() {
                this.$axios.post(this.$api.findProApi, {
                    'projectName': this.form.projectName,
                    'page': this.currentPage,
                    'sizePage': this.sizePage,
                }).then((response) => {
                        if (this.messageShow(this, response)) {
                            this.tableData = response.data['data'];
                            this.total = response.data['total'];
                            this.userData = response.data['userData'];
                        }
                    }
                )
            },
            findFuncAddress() {
                this.$axios.post(this.$api.getFuncAddressApi).then((response) => {
                        this.funcAddress = response['data']['data'];
                    }
                )
            },
            initProjectData() {
                this.projectData.projectName = null;
                this.environment.environmentTest = Array();
                this.environment.environmentDevelop = Array();
                this.environment.environmentProduction = Array();
                this.environment.environmentStandby = Array();
                this.form.users = Array();
                this.projectData.principal = null;
                this.projectData.funcFile = null;
                this.projectData.header = Array();
                this.projectData.variable = Array();
                this.projectData.id = null;
                this.projectData.modelFormVisible = true;
            },
            dealHostList(data) {
                // 把[{value:xxx1},{value:xxx2}] 转为 [xxx1,xxx2]111
                let host = Array();
                for (let i = 0; i < data.length; i++) {
                    if (data[i].value) {
                        host.push(data[i].value);
                    }
                }
                return host
            },
            dealHostDict(data) {
                // 把[xxx1,xxx2] 转为 [{value:xxx1},{value:xxx2}]
                let host = Array();
                if (!data) {
                    return host
                }
                for (let i = 0; i < data.length; i++) {
                    host.push({value: data[i]});
                }
                return host
            },

            addProjectBtn() {
                let test_length = this.environment.environmentTest.length;
                let currentEnvironment;
                if (this.environmentChoice === 'second') {
                    currentEnvironment = this.environment.environmentDevelop.length;
                } else if (this.environmentChoice === 'third') {
                    currentEnvironment = this.environment.environmentProduction.length;
                } else if (this.environmentChoice === 'fourth') {
                    currentEnvironment = this.environment.environmentStandby.length;
                } else {
                    currentEnvironment = test_length
                }

                if (currentEnvironment !== test_length) {
                    this.$message({
                        showClose: true,
                        message: '当前所选环境的url数量和测试环境不一致,会影响到接口测试',
                        type: 'warning',
                    });
                } else {
                    this.addProject()
                }
            },
            addProject() {
                    this.$axios.post(this.$api.addProApi, {
                        'projectName': this.projectData.projectName,
                        'principal': this.projectData.principal,
                        'funcFile': this.projectData.funcFile,
                        'environmentChoice': this.environmentChoice,
                        'host': this.dealHostList(this.environment.environmentTest),
                        'hostTwo': this.dealHostList(this.environment.environmentDevelop),
                        'hostThree': this.dealHostList(this.environment.environmentProduction),
                        'hostFour': this.dealHostList(this.environment.environmentStandby),
                        'id': this.projectData.id,
                        'header': JSON.stringify(this.projectData.header),
                        //'users': JSON.stringify(this.form.users),
                        'users': this.form.users,
                        'variable': JSON.stringify(this.projectData.variable),
                    }).then((response) => {
                            if (this.messageShow(this, response)) {
                                this.projectData.modelFormVisible = false;
                                this.findProject();
                            }
                        }
                    )
            },
            editProject(id) {
                this.$axios.post(this.$api.editProApi, {'id': id}).then((response) => {
                        this.form.users = JSON.parse(response.data['data']['user_id']);
                        this.projectData.projectName = response.data['data']['pro_name'];
                        this.projectData.principal = response.data['data']['principal'];
                        this.environmentChoice = response.data['data']['environment_choice'];
                        this.environment.environmentTest = this.dealHostDict(response.data['data']['host']);
                        this.environment.environmentDevelop = this.dealHostDict(response.data['data']['host_two']);
                        this.environment.environmentProduction = this.dealHostDict(response.data['data']['host_three']);
                        this.environment.environmentStandby = this.dealHostDict(response.data['data']['host_four']);
                        this.projectData.header = response.data['data']['headers'];
                        this.projectData.variable = response.data['data']['variables'];
                        this.projectData.id = id;
                        this.projectData.funcFile = response.data['data']['func_file'];
                        this.projectData.modelFormVisible = true;
                    }
                )
            },
            delProject(id) {
                this.$axios.post(this.$api.delProApi, {'id': id}).then((response) => {
                        this.messageShow(this, response);

                        // 分页数量判断，当删除了某一页的最后一条数据后，分页数量-1
                        if ((this.currentPage - 1) * this.sizePage + 1 === this.total) {
                            this.currentPage = this.currentPage - 1
                        }
                        this.findProject();
                    }
                )
            },
            addProjectVariable() {
                this.projectData.variable.push({key: null, value: null, remark: null});
            },
            delProjectVariable(i) {
                this.projectData.variable.splice(i, 1);
            },
            addProjectHeader() {
                this.projectData.header.push({key: null, value: null});
            },
            delProjectHeader(i) {
                this.projectData.header.splice(i, 1);
            },
            environmentShow(choice) {
                if (choice === 'first') {
                    return '测试环境'
                } else if (choice === 'second') {
                    return '开发环境'
                } else if (choice === 'third') {
                    return '线上环境'
                } else if (choice === 'fourth') {
                    return '备用环境'
                }
            },
            delTableRow(type, i) {
                this.$confirm('删除url会影响到整体排序,接口那边的引用是依据url的顺序来的,请认真考虑一下?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    if (type === 'test') {
                        this.environment.environmentTest.splice(i, 1);
                    } else if (type === 'develop') {
                        this.environment.environmentDevelop.splice(i, 1);
                    } else if (type === 'production') {
                        this.environment.environmentProduction.splice(i, 1);
                    } else if (type === 'standby') {
                        this.environment.environmentStandby.splice(i, 1);
                    }
                }).catch(() => {
                });
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
        },
        computed: {
            monitorEnvironmentTest() {
                return this.environment.environmentTest;
            },
            monitorEnvironmentDevelop() {
                return this.environment.environmentDevelop;
            },
            monitorEnvironmentProduction() {
                return this.environment.environmentProduction;
            },
            monitorEnvironmentStandby() {
                return this.environment.environmentStandby;
            },
        },
        watch: {
            monitorEnvironmentTest: {
                handler: function () {
                    if (this.environment.environmentTest.length === 0) {
                        this.addTableRow('test')
                    }
                    if (this.environment.environmentTest[this.environment.environmentTest.length - 1]['value']) {
                        this.addTableRow('test')
                    }
                },
                deep: true
            },
            monitorEnvironmentDevelop: {
                handler: function () {
                    if (this.environment.environmentDevelop.length === 0) {
                        this.addTableRow('develop')
                    }
                    if (this.environment.environmentDevelop[this.environment.environmentDevelop.length - 1]['value']) {
                        this.addTableRow('develop')
                    }
                },
                deep: true
            },
            monitorEnvironmentProduction: {
                handler: function () {
                    if (this.environment.environmentProduction.length === 0) {
                        this.addTableRow('production')
                    }
                    if (this.environment.environmentProduction[this.environment.environmentProduction.length - 1]['value']) {
                        this.addTableRow('production')
                    }
                },
                deep: true
            },
            monitorEnvironmentStandby: {
                handler: function () {
                    if (this.environment.environmentStandby.length === 0) {
                        this.addTableRow('standby')
                    }
                    if (this.environment.environmentStandby[this.environment.environmentStandby.length - 1]['value']) {
                        this.addTableRow('standby')
                    }
                },
                deep: true
            },
        },
        mounted() {
            this.findProject();
            this.findFuncAddress();
        },
    }
</script>
<style>
</style>
