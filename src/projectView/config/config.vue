<template>
    <div class="sceneConfig">

        <el-form :inline="true" class="demo-form-inline search-style" size="small">
            <el-form-item label="项目名称" labelWidth="110px">
                <el-select v-model="form.projectName" placeholder="请选择项目">
                    <el-option
                            v-for="(item) in proAndIdData"
                            :key="item.name"
                            :value="item.name">
                    </el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="配置名称">
                <el-input placeholder="请输入配置" v-model="form.configName">
                </el-input>
            </el-form-item>
            <el-form-item>
                <el-button type="primary" icon="el-icon-search" @click.native="configHandleCurrentChange(1)">
                    搜索
                </el-button>
                <el-button type="primary" @click.native="$refs.configEditFunc.initConfigData()">添加配置
                </el-button>
            </el-form-item>
        </el-form>

        <el-tabs value="first" class="table_padding">
            <el-tab-pane label="模块列表" name="first" style="margin: 0 0 -10px;">
                <el-table :data="tableData"
                          max-height="748"
                          stripe>
                    <el-table-column
                            prop="num"
                            label="编号"
                            min-width="40">
                    </el-table-column>
                    <el-table-column
                            prop="name"
                            label="配置名称"
                            min-width="250">
                    </el-table-column>
                    <el-table-column
                            prop="create_time"
                            label="创建时间"
                            min-width="250">
                    </el-table-column>
                    <el-table-column
                            prop="update_time"
                            label="修改时间"
                            min-width="250">
                    </el-table-column>
                    <el-table-column
                            prop="owner"
                            label="创建人"
                            min-width="150">
                    </el-table-column>
                    <el-table-column
                            label="操作"
                            min-width="250">
                        <template slot-scope="scope">
                            <el-button type="primary" icon="el-icon-edit" size="mini"
                                       @click.native="$refs.configEditFunc.editSceneConfig(tableData[scope.$index]['id'])">编辑
                            </el-button>
                            <el-button type="danger" icon="el-icon-delete" size="mini"
                                       @click.native="sureView(delConfig,tableData[scope.$index]['id'],tableData[scope.$index]['name'])">删除
                            </el-button>
                        </template>
                    </el-table-column>
                </el-table>

                <div class="pagination">
                    <el-pagination
                            @current-change="configHandleCurrentChange"
                            @size-change="configHandleSizeChange"
                            :current-page="currentPage"
                            :page-size="sizePage"
                            layout="total, sizes, prev, pager, next, jumper"
                            :total="this.total">
                    </el-pagination>
                </div>
            </el-tab-pane>
        </el-tabs>

        <configEdit
                :proModelData="proModelData"
                :projectName="form.projectName"
                :funcAddress="funcAddress"
                ref="configEditFunc">
        </configEdit>

    </div>
</template>

<script>
    import configEdit from './configEdit.vue'
    export default {
        components: {
            configEdit: configEdit,

        },
        name: 'config',
        data() {
            return {
                proModelData: null,
                funcAddress: null,
                proAndIdData:'',
                tableData: Array(),
                total: 1,
                currentPage: 1,
                sizePage: 20,
                form: {
                    projectName: null,
                    configName: null,
                },
            }
        },


        methods: {
            httpSend() {
                this.$axios.get(this.$api.baseDataApi).then((response) => {
                    if (response.data['user_pro']){
                        this.form.projectName = response.data['user_pro']['pro_name'];
                        this.findConfig();
                    }
                        this.proModelData = response.data['data'];
                    this.proAndIdData = response.data['pro_and_id'];



                    }
                );
                this.$axios.post(this.$api.getFuncAddressApi).then((response) => {
                        this.funcAddress = response['data']['data'];
                    }
                )
            },
            configHandleCurrentChange(val) {
                this.currentPage = val;
                this.findConfig()
            },
            configHandleSizeChange(val) {
                this.sizePage = val;
                this.findConfig()
            },
            findConfig() {
                this.$axios.post(this.$api.findConfigApi, {
                    'projectName': this.form.projectName,
                    'configName': this.form.configName,
                    'page': this.currentPage,
                    'sizePage': this.sizePage,
                }).then((response) => {
                        if (this.messageShow(this, response)) {
                            this.tableData = response.data['data'];
                            this.total = response.data['total'];
                        }
                    }
                )
            },
            delConfig(id) {
                this.$axios.post(this.$api.delConfigApi, {'id': id}).then((response) => {
                        this.messageShow(this, response);
                        this.form.configName = null;
                        if ((this.currentPage - 1) * this.sizePage + 1 === this.total) {
                            this.currentPage = this.currentPage - 1
                        }
                        this.findConfig();
                    }
                )
            },
        },
        mounted() {
            this.httpSend();
        },
    }
</script>
<style>
</style>
