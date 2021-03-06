<template>
<div v-if="pub">
    <pageheader/>
    <sidemenu active="/pubs"></sidemenu>
    <div class="page-content">
        <div class="header">
            <b-container>
                <!--
                        inspiration
                        https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/24301
                        https://searchworks.stanford.edu/view/rt034xr8593
                -->
                <b-row>
                    <b-col cols="2">
                        <projectavatar :project="pub.project"/>
                    </b-col>
                    <b-col cols="10" style="background-color: white;"><!--hide avatar when screen is narrow-->
                        <doibadge style="float: right;" :doi="pub.doi"/>
                        <h4 style="color: #666; margin-bottom: 10px;">
                            {{pub.name}} 
                        </h4>
                        <p class="text">{{pub.desc}}</p>
                        <p style="line-height: 200%;">
                            <b-badge v-for="topic in pub.tags" :key="topic" class="topic">{{topic}}</b-badge>
                        </p>
                    </b-col>
                </b-row>
                <br>
                <b-tabs class="brainlife-tab" v-model="tab_index">
                    <b-tab title="Details"/>
                    <b-tab title="Datasets"/>
                    <b-tab title="Apps"/>
                </b-tabs>
            </b-container>
        </div><!--header-->

        <!--main content-->
        <b-container>
            <b-row>
                <b-col>
                    <el-alert v-if="pub.removed" title="This publication has been removed" type="warning" show-icon :closable="false"></el-alert>
                    <!-- detail -->
                    <div v-if="tab_index == 0">

                        <b-row>
                            <b-col cols="2">
                                <span class="form-header">Created On</span>
                            </b-col>
                            <b-col>
                                <p><time>{{new Date(pub.create_date).toLocaleDateString()}}</time></p>
                                <br>
                            </b-col>
                        </b-row>                         
                        <b-row>
                            <b-col cols="2">
                                <span class="form-header">Authors</span>
                            </b-col>
                            <b-col>
                                <p v-for="contact in pub.authors" :key="contact._id">
                                    <contact :fullname="contact.fullname" :email="contact.email"></contact>
                                </p>
                                <br>
                            </b-col>
                        </b-row>
                        <b-row v-if="pub.readme">
                            <b-col cols="2">
                                <span class="form-header">Detail</span>
                            </b-col>
                            <b-col>
                                <vue-markdown :source="pub.readme" class="readme"></vue-markdown>
                                <br>
                            </b-col>
                        </b-row>  
                        <b-row v-if="pub.contributors.length > 0">
                            <b-col cols="2">
                                <span class="form-header">Contributors</span>
                            </b-col>
                            <b-col>
                                <p v-for="contact in pub.contributors" :key="contact._id">
                                    <contact :fullname="contact.fullname" :email="contact.email"></contact>
                                </p>
                                <br>
                            </b-col>
                        </b-row>
             

                        <b-row v-if="pub.doi">
                            <b-col cols="2">
                                <span class="form-header">Citation</span>
                            </b-col>
                            <b-col cols="10">
                                <p>
                                    <small class="text-muted">Citation to this dataset/app published on Brainlife</small>
                                </p>
                                <b-card no-body>
                                    <b-tabs pills card>
                                        <b-tab title="Text">
                                            <p>
                                                <citation :doi="pub.doi"/>
                                            </p> 
                                            <small class="text-muted">in harvard3 format. <a href="https://citation.crosscite.org" target="_blank">Use other format</a></small>
                                        </b-tab>
                                        <b-tab title="bibtex">
                                            <citation :doi="pub.doi" accept="application/x-bibtex"/>
                                        </b-tab>
                                    </b-tabs>
                                </b-card>
                                <br>
                            </b-col>
                       </b-row>  


                       <b-row v-if="pub.fundings.length > 0">
                            <b-col cols="2">
                                <span class="form-header">Funded By</span>
                            </b-col>
                            <b-col>
                                <ul style="list-style: none; padding: 0px;">
                                    <li v-for="funding in pub.fundings" :key="funding._id" class="funder">
                                        <div v-if="funding.funder == 'NSF'" class="funder-label bg-success">NSF</div>
                                        <div v-else-if="funding.funder == 'NIH'" class="funder-label bg-info">NIH</div>
                                        <div v-else class="funder-label bg-warning">{{funding.funder}}</div>
                                        {{funding.id}}
                                    </li>
                                </ul>

                            </b-col>
                        </b-row>
                        <b-row>
                            <b-col cols="2">
                                <span class="form-header">License</span>
                            </b-col>
                            <b-col>
                                <p><small class="text-muted">Datasets are published with the following license.</small></p>
                                <license :id="pub.license"/>
                                <br>
                            </b-col>
                        </b-row> 

                        <b-row>
                            <b-col cols="2">
                                <span class="form-header">Project</span>
                            </b-col>
                            <b-col>
                                <p><small class="text-muted">This publication is hosted in the following Brainlife project</small></p>
                                <a href="javascript:void(0)" @click="openproject(pub.project._id)"><h5><icon name="shield-alt"/> {{pub.project.name}}</h5></a>
                                <p class="text">{{pub.project.desc}}</p>
                                <!--
                                <p><small class="text-muted">This publication was created from the following project.</small></p>
                                <projectcard :project="pub.project"/>
                                -->
                                <br>
                            </b-col>
                        </b-row>
                        <b-row>
                            <b-col cols="2">
                                <span class="form-header">Social</span>
                            </b-col>
                            <b-col>
                                <p><small class="text-muted">You can share this publication via ..</small></p>
                                <social-sharing :url="social_url" :title="pub.name" :description="pub.desc"
                                      hashtags="brainlife,openscience"
                                      twitter-user="brainlifeio" inline-template>
                                    <b-row class="social-buttons">
                                        <b-col> 
                                            <network network="email"> <icon name="envelope"/> Email </network> <br>
                                            <network network="twitter"> <icon name="brands/twitter"/> Twitter </network><br>
                                            <network network="facebook"> <icon name="brands/facebook"/> Facebook </network><br>
                                        </b-col>
                                        <b-col> 
                                            <network network="googleplus"> <icon name="brands/google-plus"/> Google + </network> <br>
                                            <network network="linkedin"> <icon name="brands/linkedin"/> LinkedIn </network><br>
                                            <network network="pinterest"> <icon name="brands/pinterest"/> Pinterest </network><br>
                                            <!--<network network="sms"> <icon name="commenting-o"/> SMS </network><br>-->
                                            <!--<network network="line"> <icon name="brands/line"/> Line </network> <br>-->
                                        </b-col>
                                        <b-col>
                                            <network network="reddit"> <icon name="brands/reddit"/> Reddit </network><br>
                                            <network network="skype"> <icon name="brands/skype"/> Skype </network><br>
                                            <network network="weibo"> <icon name="brands/weibo"/> Weibo </network> <br>
                                            <!-- <network network="whatsapp"> <icon name="brands/whatsapp"/> Whatsapp </network><br>-->
                                            <!-- <network network="telegram"> <icon name="brands/telegram"/> Telegram </network><br>-->
                                        </b-col>
                                        <!--
                                        <b-col>
                                            <network network="vk"> <icon name="vk"/> VKontakte </network><br>
                                            <network network="odnoklassniki"> <icon name="odnoklassniki"/> Odnoklassniki </network><br>
                                        </b-col>
                                        -->
                                    </b-row>
                                </social-sharing>
                                <br>
                            </b-col>
                            <b-col cols="3">
                                <b-card>
                                    <div class='altmetric-embed' data-badge-type='medium-donut' data-badge-details="right" data-hide-no-mentions="false" :data-doi="pub.doi"></div>
                                </b-card>
                            </b-col>
                        </b-row>

                        <hr>
                        <b-row>
                            <b-col cols="2">
                            </b-col>
                            <b-col>
                                <vue-disqus shortname="brain-life" :identifier="pub._id"/>
                            </b-col>
                        </b-row>
                    </div>
                    <div v-if="tab_index == 1">
                        <!-- datasets -->
                        <p>
                            <b>{{ds.subjects}}</b> <span class="text-muted">Subjects</span> <span style="opacity: 0.2">|</span>
                            <b>{{ds.count}}</b> <span class="text-muted">Datasets</span>
                            (<b>{{ds.size | filesize}}</b>)
                        </p>

                        <div class="group" v-for="(group, subject) in dataset_groups" :key="subject">
                            <b-row>
                                <b-col cols="2">
                                    <b>{{subject}}</b>
                                </b-col>
                                <b-col>
                                    <div v-for="(datatype, datatype_id) in group.datatypes" :key="datatype_id">
                                        <b-row v-for="(block, datatype_tags_s) in datatype.datatype_tags" :key="datatype_tags_s" style="margin-bottom: 3px;">
                                            <div @click="toggle(block, subject, datatype_id, JSON.parse(datatype_tags_s))" class="toggler">
                                                <div style="width: 20px; display: inline-block;" class="text-muted">
                                                    <icon name="caret-right" v-if="!block.show"/> 
                                                    <icon name="caret-down" v-if="block.show"/> 
                                                </div>
                                                <datatypetag :datatype="datatypes[datatype_id]" :tags="JSON.parse(datatype_tags_s)"/>
                                                &nbsp;
                                                <span class="text-muted">{{datatypes[datatype_id].desc}}</span>
                                                <small class="text-muted" style="float: right;">{{block.count}} datasets {{block.size|filesize}}</small>
                                            </div>
                                            <transition name="fadeHeight">
                                                <b-list-group class="datasets" v-if="block.show && block.datasets">
                                                    <b-list-group-item v-for="(dataset, idx) in block.datasets" :key="idx" class="dataset" @click="view(dataset._id)">
                                                        {{dataset.desc}}
                                                        <span v-if="!dataset.desc" class="text-muted">{{dataset._id}}.tar.gz</span>
                                                        <tags :tags="dataset.tags"/>
                                                        <div style="float: right; width: 90px; text-align: right">{{new Date(dataset.create_date).toLocaleDateString()}}</div>
                                                        <div style="float: right; width: 90px;"><span v-if="dataset.size" class="text-muted">{{dataset.size|filesize}}</span>&nbsp;</div>
                                                        <div style="float: right; width: 90px;"><span v-if="dataset.download_count" class="text-muted"><icon name="download"/> {{dataset.download_count}} times</span>&nbsp;</div>
                                                        <!--<td>{{dataset.storage}}</td>-->
                                                    </b-list-group-item>
                                                </b-list-group>
                                            </transition>
                                        </b-row>
                                    </div>
                                </b-col>
                            </b-row>
                        </div>
                    </div>
                    
                    <div v-if="tab_index == 2">
                        <p class="text-muted">Following Apps are used to generate published datasets.</p>
                        <b-row>
                            <b-col cols="6" v-for="rec in apps" :key="rec.app._id" style="margin-bottom: 10px;">
                                <app :app="rec.app" height="270px" :branch="rec.service_branch||'master'"></app>
                                <div class="button" style="float: right;" @click="download_app(rec.service, rec.service_branch)"><icon name="download"/></div>
                            </b-col>
                        </b-row>
                    </div>
                </b-col>
            </b-row>
        </b-container>
        <br>
        <br>
    </div><!--page-content-->
</div>
</template>

<script>
import Vue from 'vue'
import pageheader from '@/components/pageheader'
import sidemenu from '@/components/sidemenu'
import projectavatar from '@/components/projectavatar'
import contact from '@/components/contact'
import VueMarkdown from 'vue-markdown'
import license from '@/components/license'
import datatypetag from '@/components/datatypetag'
import tags from '@/components/tags'
import citation from '@/components/citation'
import app from '@/components/app'
import doibadge from '@/components/doibadge'

export default {

    components: { 
        pageheader, sidemenu, projectavatar, 
        contact, VueMarkdown, license, 
        datatypetag, tags, 
        app, citation,
        doibadge, 
    },

    //https://help.altmetric.com/support/solutions/articles/6000141419-what-metadata-is-required-to-track-our-content-
    //https://github.com/declandewet/vue-meta
    metaInfo() {
        var meta = [];
        if(this.pub) {
            meta.push({name: 'DC.DOI', content: this.pub.doi});
            meta.push({name: 'DC.title', content: this.pub.name});
            meta.push({name: 'DC.description', content: this.pub.desc});
            this.pub.authors.forEach(author=>{
                meta.push({name: 'DC.creator', content: author.fullname});
            });
            this.pub.tags.forEach(tag=>{
                meta.push({name: 'DC.subject', content: tag});
            });
        }
        return {
            //title: "hi", 
            meta,
        } 
    },

    data () {
        return {
            pub: null, //publication detail
            dataset_groups: null, //datasets inventory grouped 
            apps: null, //list of apps

            datatypes: {}, 

            tab_index: 0,
            query: "",
            config: Vue.config,
        }
    },

    computed: {
        social_url: function() {
            if(this.pub.doi) return "http://doi.org/"+this.pub.doi;
            return document.location;
        },

        ds: function() {
            let stats = {subjects: 0, count: 0, size: 0};
            for(var subject in this.dataset_groups) {
                stats.subjects++; 
                stats.size += this.dataset_groups[subject].size;
                stats.count += this.dataset_groups[subject].count;
            }
            return stats;
        }
    },

    mounted: function() {
        //load publication detail
        this.$http.get('pub', {params: {
            find: JSON.stringify({_id: this.$route.params.id}),
            populate: 'project',
            deref_contacts: true,
        }})
        .then(res=>{
            this.pub = res.body.pubs[0];

            if(Vue.config.debug) this.pub.doi = "10.1038/nature.2014.14583";

            //load all datatypes
            return this.$http.get('datatype');
        })
        .then(res=>{
            res.body.datatypes.forEach((d)=>{
                this.datatypes[d._id] = d;
            });

            //load inventory..
            return this.$http.get('pub/datasets-inventory/'+this.$route.params.id);
        })
        .then(res=>{
            let groups = {};
            res.body.forEach(rec=>{
                let subject = rec._id.subject;
                let datatype = rec._id.datatype;
                let datatype_tags = rec._id.datatype_tags;
                let datatype_tags_s = JSON.stringify(rec._id.datatype_tags);
                if(!groups[subject]) {
                    groups[subject] = { size: 0, count: 0, datatypes: {} };
                }
                if(!groups[subject].datatypes[datatype]) {
                    groups[subject].datatypes[datatype] = { size: 0, count:0, datatype_tags: {}};
                }
                if(!groups[subject].datatypes[datatype].datatype_tags[datatype_tags_s]) {
                    groups[subject].datatypes[datatype].datatype_tags[datatype_tags_s] = {size: 0, count: 0, show: false, datasets: null};
                }
                groups[subject].datatypes[datatype].datatype_tags[datatype_tags_s].size += rec.size;
                groups[subject].datatypes[datatype].datatype_tags[datatype_tags_s].count += rec.count;
                groups[subject].datatypes[datatype].size += rec.size;
                groups[subject].datatypes[datatype].count += rec.count;
                groups[subject].size += rec.size;
                groups[subject].count += rec.count;
            });
            this.dataset_groups = groups;

            //load apps
            return this.$http.get('pub/apps/'+this.$route.params.id, {params: {
                //populate: 'inputs.datatype outputs.datatype contributors',
            }});
        })
        .then(res=>{
            this.apps = res.body;

            //open dataset previously selected
            if(document.location.hash) {
                let id = document.location.hash.substring(1);
                this.$root.$emit('dataset.view', {id});
            }

            Vue.nextTick(()=>{
                //re-initialize altmetric badge - now that we have badge <div> placed
                _altmetric_embed_init(this.$el);
            });

        }).catch(console.error);
    },
    
    methods: {
        view(id) {
            document.location.hash = id;
            this.$root.$emit('dataset.view', {id});
        },

        openproject(project_id) {
            this.$router.push('/project/'+project_id);
        },

        toggle(block, subject, datatype, datatype_tags) {
            block.show = !block.show;
            if(!block.datasets) {
                //load datasets
                this.$http.get('pub/datasets/'+this.$route.params.id, {params: {
                    find: JSON.stringify({
                        'meta.subject': subject,
                        datatype: datatype,
                        datatype_tags: datatype_tags,
                    }),
                    //populate: 'project',
                    //sort: 'meta.subject datatype tags',
                    //skip: this.datasets_perpage*(this.datasets_page-1),
                    //limit: this.datasets_perpage,
                }}).then(res=>{
                    block.datasets = res.body.datasets;
                }).catch(console.error);
            }
        },

        download_app(service, branch) {
            if(!branch) branch = "master";
            document.location = "https://github.com/"+service+"/archive/"+branch+".zip";
        },
    }
}
</script>

<style>
.social-buttons span[data-link] {
background-color: white;
border-radius: 10px;
padding: 10px;
margin: 5px;
display: inline-block;
width: 140px;
transition: 0.5s background-color, 0.5s color;
}
.social-buttons span[data-link]:hover {
background-color: #007bff;
color: white;
cursor: pointer;
}
.social-buttons svg {
position: relative;
top: 2px;
height: 15px;
width: 15px;
margin: 0 5px;
color: #999;
transition: 0.5s color;
}
.social-buttons span[data-link]:hover svg {
color: white;
}
</style>
<style scoped>
.header {
background-color: white;
margin-bottom: 30px;
padding: 30px 0px 0px 0px;
border-bottom: 1px solid #ccc;
}
.topic {
padding: 6px; 
background-color: #eee;
text-transform: uppercase;
color: #999;
border-radius: 0px;
margin-right: 4px;
}
.funder {
background-color: white;
margin: 5px;
display: inline-block;
padding-right: 10px;
font-weight: bold;
color: #999;
}
.funder .funder-label {
color: white;
display: inline-block;
padding: 3px 5px;
}
.datasets {
width: 100%;
margin-top: 5px;
margin-bottom: 5px;
margin-left: 28px;
box-shadow: 2px 2px 2px #ddd;
}
.dataset:hover {
cursor: pointer;
background-color: #f7f7f7;
}
.toggler {
padding: 4px;
width: 100%;
}
.toggler:hover {
cursor: pointer;
background-color: #eee;
}
.fadeHeight-enter-active,
.fadeHeight-leave-active {
transition: all 0.2s;
max-height: 230px;
}
.fadeHeight-enter,
.fadeHeight-leave-to
{
opacity: 0;
max-height: 0px;
}
.project h5 {
color: #007bff;
}
.group {
margin-top: 10px;
padding-top: 10px;
border-top: 1px solid #eee;
}
</style>


