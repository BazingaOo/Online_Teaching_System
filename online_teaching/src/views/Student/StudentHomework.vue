<template>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/StudentContainer' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item :to="{ path: '/StudentContainer/StudentCourse' }">已选课程</el-breadcrumb-item>
            <el-breadcrumb-item><a @click="prev">课程详情</a></el-breadcrumb-item>
            <el-breadcrumb-item>课程作业</el-breadcrumb-item>
        </el-breadcrumb>
        </br>
        <el-table :data="tableData" style="width: 100%">
            <el-table-column label="作业名称" width="180">
                <template slot-scope="scope">
                    <span>{{ scope.row.hName }}</span>
                </template>
            </el-table-column>
            <el-table-column label="作业内容" width="180">
                <template slot-scope="scope">
                    <span>{{ scope.row.hInfo }}</span>
                </template>
            </el-table-column>
            <el-table-column label="作业文件" width="250">
                <template slot-scope="scope">
                    <el-link @click='download(scope.$index, scope.row)'>{{ scope.row.hDoc}}</el-link>
                </template>
            </el-table-column>
            <el-table-column label="截止提交日期" width="180">
                <template slot-scope="scope">
                    <i class="el-icon-time"></i>
                    <span style="margin-left: 10px">{{ scope.row.hTime| dateFmt('YYYY-MM-DD HH:mm') }}</span>
                </template>
            </el-table-column>
            <el-table-column label="我的作业" width="180">
                <template slot-scope="scope">
                    <el-button size="mini" @click="selectSSHomework(scope.$index, scope.row)">查看</el-button>
                </template>
            </el-table-column>
            <!-- <el-table-column label="操作">
                <template slot-scope="scope">
                    <el-button size="mini" @click="handleEdit(scope.$index, scope.row);updateFormVisible = true">编辑
                    </el-button>
                    <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                </template>
            </el-table-column> -->
        </el-table>

        <!-- <el-dialog title="修改作业信息" :visible.sync="updateFormVisible" @close="resetForm('updateForm')">
            <el-form :model="updateForm" ref="updateForm">
                <el-form-item label="作业名称" :label-width="formLabelWidth" prop="cwName">
                    <el-input v-model="updateForm.hName" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="作业内容" :label-width="formLabelWidth" prop="cwInfo">
                    <el-input v-model="updateForm.hInfo" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="上传作业文件" :label-width="formLabelWidth">
                    <el-upload ref='upload' :before-remove="beforeRemove" :on-change="handleChange" action
                        :http-request="getFile" :file-list="fileList" multiple>
                        <el-button size="small" type="primary">浏览</el-button>
                    </el-upload>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="updateFormVisible = false;resetForm('updateForm')">取 消</el-button>
                <el-button type="primary" @click="updateHomework();updateFormVisible = false;resetForm('updateForm')">确
                    定
                </el-button>
            </div>
        </el-dialog>

        <el-button type="primary" @click="addFormVisible = true">添加作业</el-button>

        <el-dialog title="作业信息" :visible.sync="addFormVisible" @close="resetForm('addForm')">
            <el-form :model="addForm" ref="addForm">
                <el-form-item label="作业名称" :label-width="formLabelWidth" prop="cwName">
                    <el-input v-model="addForm.hName" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="作业内容" :label-width="formLabelWidth" prop="cwInfo">
                    <el-input v-model="addForm.hInfo" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="上传作业文件" :label-width="formLabelWidth">
                    <el-upload ref='upload' :before-remove="beforeRemove" :on-change="handleChange" action
                        :http-request="getFile" :file-list="fileList" multiple>
                        <el-button size="small" type="primary">浏览</el-button>
                    </el-upload>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="addFormVisible = false;resetForm('addForm')">取 消</el-button>
                <el-button type="primary" @click="addHomework();addFormVisible = false;resetForm('addForm')">确 定
                </el-button>
            </div>
        </el-dialog> -->
    </div>
</template>

<script>
    export default {
        inject: ['reload'],
        data() {
            return {
                formLabelWidth: '120px',
                addFormVisible: false,
                updateFormVisible: false,
                tableData: [],
                fileList: [],
                hId: '',
                addForm: {
                    hName: '',
                    hInfo: '',
                },
                updateForm: {
                    hName: '',
                    hInfo: '',
                },
            }
        },
        mounted: function () {
            this.selectHomework()
        },
        methods: {
            download(index, row) {
                let params = { filename: row.hDoc }
                this.$axios.post('/api/downloadTeacherHomework', this.$qs.stringify(params), { responseType: 'blob' })
                    .then(function (res) {
                        let url = window.URL.createObjectURL(new Blob([res.data]))
                        let link = document.createElement('a')
                        link.style.display = 'none'
                        link.href = url
                        link.setAttribute('download', row.hDoc)
                        document.body.appendChild(link)
                        link.click()
                    }).catch(function (error) {
                        console.log(error);
                    });
            },
            selectSSHomework(index, row) {
                console.log(row.tId)
                this.$router.push({ path: 'MyHomework', query: { hId: row.hId } })
                let params = {
                    hId: row.hId,
                    sId: sessionStorage.getItem('sId'),
                    tId: row.tId
                }
                let that = this
                this.$axios.post("/api/addMyHomework", this.$qs.stringify(params))
                    .then(function (res) {
                    }).catch(function (error) {
                        console.log(error);
                    });
            },
            prev() {
                this.$router.go(-1)
            },
            // updateHomework() {
            //     let that = this
            //     const formData = new FormData()
            //     this.fileList.forEach(fileList => {
            //         formData.append('fileList', fileList.raw); //此处一定是append file.raw 上传文件只需维护fileList file.raw.name要加上 
            //     })
            //     formData.append('crId', this.$route.query.crId)
            //     formData.append('hId', this.hId)
            //     formData.append('tId', sessionStorage.getItem('tId'))
            //     formData.append('hName', this.updateForm.hName)
            //     formData.append('hInfo', this.updateForm.hInfo)
            //     this.$axios.post("/api/updateHomework", formData,
            //         {
            //             headers: { 'Content-Type': 'multipart/form-data' }
            //         })
            //         .then(function (res) {
            //             that.reload()
            //             console.log(res.data);
            //         }).catch(function (error) {
            //             console.log(error);
            //         });
            //     this.$refs.upload.clearFiles()
            // },
            // addHomework() {
            //     let that = this
            //     const formData = new FormData()
            //     this.fileList.forEach(fileList => {
            //         formData.append('fileList', fileList.raw); //此处一定是append file.raw 上传文件只需维护fileList file.raw.name要加上 
            //     })
            //     formData.append('crId', this.$route.query.crId)
            //     formData.append('tId', sessionStorage.getItem('tId'))
            //     formData.append('hName', this.addForm.hName)
            //     formData.append('hInfo', this.addForm.hInfo)
            //     this.$axios.post("/api/addHomework", formData,
            //         {
            //             headers: { 'Content-Type': 'multipart/form-data' }
            //         })
            //         .then(function (res) {
            //             that.reload()
            //             console.log(res.data);
            //         }).catch(function (error) {
            //             console.log(error);
            //         });
            //     this.$refs.upload.clearFiles()
            // },
            // resetForm(formName) {
            //     if (this.$refs[formName] !== undefined) {
            //         this.$refs[formName].resetFields();
            //     }
            //     this.$refs.upload.clearFiles()
            // },
            selectHomework() {
                let sId = sessionStorage.getItem('sId');
                console.log(sId)
                let crId = this.$route.query.crId;
                let that = this
                let params = {
                    crId: crId,
                    sId: sId
                }
                this.$axios.post("/api/selectCourseByCRid", this.$qs.stringify(params))
                    .then(function (res) {
                        if (res.data == '') {
                            that.tableData = [];
                            that.$message({
                                title: '系统提示',
                                message: '当前课程没有课件！',
                                showClose: true,
                                center: true,
                                type: 'success'
                            })
                        } else {
                            that.tableData = res.data;
                            console.log(res.data);
                        }
                    }).catch(function (error) {
                        console.log(error);
                    });
            },
            // handleEdit(index, row) {
            //     this.hId = row.hId
            //     console.log(row.hId);
            // },
            // handleDelete(index, row) {
            //     let that = this
            //     this.hId = row.hId
            //     this.hDoc = row.hDoc
            //     const formData = new FormData()
            //     formData.append('hId', row.hId)
            //     formData.append('hDoc', row.hDoc)
            //     this.$axios.post("/api/deleteHomework", formData)
            //         .then(function (res) {
            //             that.reload()
            //         }).catch(function (error) {
            //             console.log(error);
            //         });
            // },
            // handleChange(file, fileList) {
            //     let existFile = fileList.slice(0, fileList.length - 1).find(f => f.name === file.name)
            //     if (existFile) {
            //         this.$message.error('当前文件已经存在!');
            //     }
            //     this.fileList = fileList
            // },
            // beforeRemove(file, fileList) {
            //     return this.$confirm(`确定移除 ${file.name}？`);
            // },
            // getFile(item) {
            //     console.log('name:' + item.file.name)
            //     this.file = item.file
            // },
        }
    }
</script>