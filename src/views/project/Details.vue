<template>
    <div style="min-height: 600px" v-loading="loading">
        <el-card shadow="never" style="min-height: 400px"  >
            <div slot="header">
                <el-row>
                    <el-col :span="16">
                        <span>{{project.name}}</span>
                    </el-col>
                    <el-col :span="8">
                        <div  style="text-align: right;">
                            <el-button 
                            @click="goGithub(project.url)"
                            style="padding: 3px 0" 
                            type="text"
                            icon="el-icon-back">前往GitHub</el-button>
                            <el-button 
                            @click="more"
                            style="padding: 3px 0" 
                            type="text"
                            icon="el-icon-more-outline">更多项目</el-button>
                        </div>
                    </el-col>
                </el-row> 
            </div>
            <div style="font-size: 0.9rem;line-height: 1.5;color: #606c71;">
                发布 {{project.createTime}}<br>
                更新 {{project.updateTime}}
            </div>
            <div style="font-size: 1.1rem;line-height: 1.5;color: #303133;padding: 20px 0px 0px 0px">
                {{project.description}}
            </div>
            <div style="font-size: 1.1rem;line-height: 1.5;color: #303133;padding: 10px 0px 25px 0px;border-bottom: 1px solid #E4E7ED;">
                <el-tag size="small" type="success">{{project.language}}</el-tag>
                <el-tooltip effect="dark" :content="'star '+project.stargazersCount" placement="bottom">
                    <i class="el-icon-star-off" style="margin: 0px 5px 0px 15px"></i>
                </el-tooltip>
                {{project.stargazersCount}}
                <el-tooltip effect="dark" :content="'watch '+project.watchersCount" placement="bottom">
                    <i class="el-icon-view" style="margin: 0px 5px 0px 15px"></i>
                </el-tooltip>
                {{project.watchersCount}}
                <el-tooltip effect="dark" :content="'fork '+project.forksCount" placement="bottom">
                    <i class="el-icon-bell" style="margin: 0px 5px 0px 15px"></i>
                </el-tooltip>   
                {{project.forksCount}}
            </div>
            <div v-html="project.content" v-if="project.content"></div>
            <div v-if="!project.content" style="padding: 20px 0px 20px 0px;text-align: center">
                <font style="font-size: 30px;color:#dddddd "><b>还没有介绍 (╯°Д°)╯︵ ┻━┻</b></font>
            </div>
        </el-card>
    </div>
</template>
<script>
    import { mapGetters } from 'vuex'
    import ProjectApi from '@/api/project'
    export default{
        data(){
            return {
                project:{
                    name:""
                },
                loading:false,
            }
        },
        computed: {
            ...mapGetters([
                'token',
            ])
        },
        mounted(){
            this.loading=true
            this.project.name=this.$route.params.name
            ProjectApi.single(this.project.name).then((response)=>{
                let result = response.data
                let base64 = require('js-base64').Base64
                this.project.id = result['id']
                this.project.url = result['html_url']
                this.project.stargazersCount = result['stargazers_count']
                this.project.watchersCount = result['watchers_count']
                this.project.forksCount = result['forks_count']
                this.project.language = result['language']
                this.project.description=result['description']
                this.project.content=this.$markdown(base64.decode(result['readme_content']))
                this.project.createTime=this.$util.utcToLocal(result['created_at'])
                this.project.updateTime=this.$util.utcToLocal(result['updated_at'])
            }).catch(()=>this.loading=false).then(()=>this.loading=false)
    
        },
        methods:{
            goGithub(url){
                window.open(url)
            },
            more(){
                this.$router.push('/user/project/main')
            }
        }
    }
</script>