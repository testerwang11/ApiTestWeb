<template>
    <div class="moveApi">
        <el-dialog title="接口移动" :visible.sync="moveData.modelFormVisible" width="30%">
            <el-tabs value="second">
                <el-tab-pane label="基础信息" name="second" style="margin-top: 10px">
                    <el-form :inline="true" size="small">
                        <!--<el-form-item label="项目名称">
                            <el-select v-model="moveData.projectName" placeholder="请选择项目" size="small">
                                <el-option value="1">无车承运</el-option>
                            </el-select>
                        </el-form-item>-->
                        <el-form-item label="模块名称">
                            <el-select id="moduleId" v-model="moveData.module_id" placeholder="请选择模块" size="small"
                                       v-on:="changeModule()">
                                <el-option v-for="item in moveData.module_ids"
                                           :label="item.name"
                                           :value="item.moduleId"
                                           :key="item.moduleId">{{ item.name }}
                                </el-option>
                            </el-select>
                        </el-form-item>
                    </el-form>
                </el-tab-pane>
            </el-tabs>
            <div slot="footer" class="dialog-footer">
                <el-button size="small" @click="moveData.modelFormVisible = false">取 消</el-button>
                <el-button type="primary" size="small" @click.native="move()">确 定</el-button>
            </div>
        </el-dialog>
    </div>
</template>
<script>
    export default {
        name: 'moveApi',
        data() {
            return {
                ApiMsgTableData: Array(),//  接口表单数据
                apiMsgPage: {
                    total: 1,
                    currentPage: 1,
                    sizePage: 20,
                },
                moveData: {
                    case_ids: [],
                    TwoData: '模块',
                    projectName: null,
                    module_ids: null,
                    module_id: null,
                    modelFormVisible: false,
                    formLabelWidth: '80px',
                },
            }
        },
        methods: {
            initData2() {
                this.moveData.modelFormVisible = true;
                this.$axios.post(this.$api.findModuleApi, {
                    'projectName': this.projectName,
                    'page': 1,
                    'sizePage': 30,
                }).then((response) => {
                        if (this.messageShow(this, response)) {
                            this.moveData.module_ids = response.data['data']
                        }
                    }
                )
            },
            moduleSelect(prjectName, ids) {
                this.moveData.modelFormVisible = true;
                this.moveData.case_ids = ids;

                this.$axios.post(this.$api.findModuleApi, {
                    'projectName': prjectName,
                    'page': 1,
                    'sizePage': 30,
                }).then((response) => {
                        if (this.messageShow(this, response)) {
                            this.moveData.module_ids = response.data['data']
                        }
                    }
                )
            },
            changeModule() {
                this.moveData.module_id = this.moveData.module_id; //获取模块ID，即option对应的ID值
                //this.moveData.module_id = document.getElementById('moduleId').value
            },
            move() {
                this.$axios.post(this.$api.moveApiApi, {
                    'apiMsgIds': this.moveData.case_ids,
                    'module_id': this.moveData.module_id,
                }).then((response) => {
                        if (this.messageShow(this, response)) {
                            this.ApiMsgTableData = response.data['msg'];
                            this.apiMsgPage.total = response.data['total'];
                        }
                    }
                )
            }
        },
        mounted() {
        },
    }
</script>

<style>
</style>
