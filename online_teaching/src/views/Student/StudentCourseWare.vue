<template>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/StudentContainer' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item :to="{ path: '/StudentContainer/StudentCourse' }">已选课程</el-breadcrumb-item>
            <el-breadcrumb-item><a @click="prev">课程详情</a></el-breadcrumb-item>
            <el-breadcrumb-item>课程课件</el-breadcrumb-item>
        </el-breadcrumb>
        </br>
        <el-table :data="wareData" style="width: 100%">
            <el-table-column label="课件编号" width="180" prop="crId">
                <template slot-scope="scope">
                    <i class="el-icon-reading"></i>
                    <span style="margin-left: 10px">{{ scope.$index+1 }}</span>
                </template>
            </el-table-column>
            <el-table-column label="课件名称" width="180">
                <template slot-scope="scope">
                    <el-popover trigger="hover" placement="top">
                        <p>课件名称: {{ scope.row.cwName }}</p>
                        <p>课件信息: {{ scope.row.cwInfo }}</p>
                        <div slot="reference" class="name-wrapper">
                            <el-tag size="medium">{{ scope.row.cwName }}</el-tag>
                        </div>
                    </el-popover>
                </template>
            </el-table-column>
            <el-table-column label="课件" width="300">
                <template slot-scope="scope">
                    <i class="el-icon-reading"></i>
                    <el-link @click='download(scope.$index, scope.row)'>{{ scope.row.cwDoc}}</el-link>
                </template>

            </el-table-column>

            <!-- <el-table-column label="操作">
                <template slot-scope="scope">
                    <el-button size="mini" @click="updateFormVisible=true,handleEdit(scope.$index, scope.row);">编辑
                    </el-button>
                    <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                </template>
            </el-table-column> -->
        </el-table>


        <!-- <el-button type="primary" @click="dialogFormVisible = true">添加课件</el-button> -->


        <!-- <el-dialog title="课件信息" :visible.sync="dialogFormVisible" @close="resetForm('wareform')">
            <el-form :model="wareform" ref="wareform">
                <el-form-item label="课件名称" :label-width="formLabelWidth" prop="cwName">
                    <el-input v-model="wareform.cwName" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="课件信息" :label-width="formLabelWidth" prop="cwInfo">
                    <el-input v-model="wareform.cwInfo" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="上传课件" :label-width="formLabelWidth">
                    <el-upload ref='upload' :before-remove="beforeRemove" :on-change="handleChange" action
                        :http-request="getFile" :file-list="fileList" multiple>
                        <el-button size="small" type="primary">浏览</el-button>
                    </el-upload>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false;resetForm('wareform')">取 消</el-button>
                <el-button type="primary" @click="fileupload();dialogFormVisible = false;resetForm('wareform')">确 定
                </el-button>
            </div>
        </el-dialog>

        <el-dialog title="修改信息" :visible.sync="updateFormVisible" @close="resetForm('updateform')">
            <el-form :model="updateform" ref="updateform">
                <el-form-item label="课件名称" :label-width="formLabelWidth" prop="cwName">
                    <el-input v-model="updateform.cwName" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="课件信息" :label-width="formLabelWidth" prop="cwInfo">
                    <el-input v-model="updateform.cwInfo" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="上传课件" :label-width="formLabelWidth">
                    <el-upload ref='upload' :before-remove="beforeRemove" :on-change="handleChange" action
                        :http-request="getFile" :file-list="fileList" multiple>
                        <el-button size="small" type="primary">浏览</el-button>
                    </el-upload>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="updateFormVisible = false;resetForm('updateform')">取 消</el-button>
                <el-button type="primary" @click="updateWare();updateFormVisible = false;resetForm('updateform')">确 定
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
                //tableData: [],
                wareData: [],
                // dialogFormVisible: false,
                // updateFormVisible: false,
                // wareform: {
                //     cwName: '',
                //     cwInfo: '',
                // },
                // updateform: {
                //     cwName: '',
                //     cwInfo: '',
                // },
                //cwId: '',
                //cwDoc: '',
                // formLabelWidth: '120px',
                // fileList: [],
                // file: {},
            }
        },
        mounted: function () {
            this.selectAllWare()
        },
        methods: {
            prev() {
                this.$router.go(-1)
            },
            download(index, row) {
                let params = { filename: row.cwDoc }
                this.$axios.post('/api/downloadCourseWare', this.$qs.stringify(params), { responseType: 'blob' })
                    .then(function (res) {
                        let url = window.URL.createObjectURL(new Blob([res.data]))
                        let link = document.createElement('a')
                        link.style.display = 'none'
                        link.href = url
                        link.setAttribute('download', row.cwDoc)
                        document.body.appendChild(link)
                        link.click()
                    }).catch(function (error) {
                        console.log(error);
                    });
            },
            // updateWare() {
            //     let that = this
            //     const formData = new FormData()
            //     this.fileList.forEach(fileList => {
            //         formData.append('fileList', fileList.raw); //此处一定是append file.raw 上传文件只需维护fileList file.raw.name要加上 
            //     })
            //     formData.append('cwId', this.cwId)
            //     formData.append('cwName', this.updateform.cwName)
            //     formData.append('cwInfo', this.updateform.cwInfo)
            //     this.$axios.post("/api/updateWare", formData,
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
            // beforeRemove(file, fileList) {
            //     return this.$confirm(`确定移除 ${file.name}？`);
            // },
            // getFile(item) {
            //     console.log(item.file)
            //     this.file = item.file
            // },
            // fileupload() {
            //     let that = this
            //     const formData = new FormData()
            //     this.fileList.forEach(fileList => {
            //         formData.append('fileList', fileList.raw); //此处一定是append file.raw 上传文件只需维护fileList file.raw.name要加上 
            //     })
            //     formData.append('crId', this.$route.query.crId)
            //     formData.append('cwName', this.wareform.cwName)
            //     formData.append('cwInfo', this.wareform.cwInfo)
            //     this.$axios.post("/api/fileupload", formData,
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
            // handleChange(file, fileList) {
            //     let existFile = fileList.slice(0, fileList.length - 1).find(f => f.name === file.name)
            //     if (existFile) {
            //         this.$message.error('当前文件已经存在!');
            //     }
            //     this.fileList = fileList
            // },
            // handleEdit(index, row) {
            //     this.cwId = row.cwId
            // },
            // handleDelete(index, row) {
            //     let that = this
            //     this.cwId = row.cwId
            //     this.cwDoc = row.cwDoc
            //     const formData = new FormData()
            //     formData.append('cwId', row.cwId)
            //     formData.append('cwDoc', row.cwDoc)
            //     this.$axios.post("/api/deleteWare", formData)
            //         .then(function (res) {
            //             that.reload()
            //         }).catch(function (error) {
            //             console.log(error);
            //         });
            // },
            selectAllWare() {
                let that = this
                let params = { crId: that.$route.query.crId }
                this.$axios.post("/api/selectWareByCRid", this.$qs.stringify(params))
                    .then(function (res) {
                        if (res.data == '') {
                            that.wareData = [];
                            that.$message({
                                title: '系统提示',
                                message: '当前课程没有课件！',
                                showClose: true,
                                center: true,
                                type: 'success'
                            })
                        } else {
                            that.wareData = res.data;

                        }
                    }).catch(function (error) {
                        console.log(error);
                    });
            }
        }
    }
</script>