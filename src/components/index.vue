<template>
    <div>
        <header class="vsp-c-header" v-intro-step="1" v-intro="'Welcome to Channel Map Tool Web.'" v-intro-position="'top'">
            <div class="vsp-c-site-logo">
                <router-link to="/">
                    <a class="vsp-c-site-logo__img" style="font-size:18px">
                        <img src='../assets/MK.png' height='32'/>
                        ChannelMap Tool
                        <!-- <div style="margin-left:30%;margin-right:40%;" v-intro-step="1" v-intro="'Welcome to Build Carrier Serice Web.'" v-intro-position="'top'"> 
                        </div> -->
                    </a>
                </router-link>
            </div>
            <nav class="vsp-c-navigation" style="height:32px">
                <Button type="dashed" shape="circle" style="font-size:14px;" @click="wakeuserguide">New User Guide</Button>&nbsp;&nbsp;&nbsp;&nbsp;
                <Button type="dashed" shape="circle" style="font-size:14px;" @click="wakevideo">Video Introduction</Button>&nbsp;&nbsp;&nbsp;&nbsp;
                <Button type="dashed" shape="circle" style="font-size:14px;" @click="wakeabout">About</Button>
                <Modal
                v-model="about_open">
                <p slot="header">
                    <Icon type="ios-chatbubbles" color='green' size='25'></Icon>
                    About &nbsp;&nbsp;&nbsp;&nbsp; ChannelMap Tool Web.
                </p>
                <Timeline>
                    <TimelineItem color="green">Version 1.0: ChannelMap Tool Web published.</TimelineItem>
                    <TimelineItem color="green">Version 1.1: Support Create Subscription Package.</TimelineItem>
                    <TimelineItem color="green">Version 1.2: Support Data Analysis.</TimelineItem>
                    <TimelineItem color="green">Version 1.3: Support New User Guide.</TimelineItem>
                    <TimelineItem color="green">Version 1.4: Support Quality Level Update.</TimelineItem>
                    <TimelineItem color="green">Version 1.5: Support Offer/Offer Group Manage.</TimelineItem>
                    <TimelineItem color="green">Version 1.6: Video Introduction Added.</TimelineItem>
                </Timeline>
                </Modal>
                <Modal fullscreen
                    v-model="video_open">
                    <video controls style="margin-left:5%;margin-right:5%;width:90%;margin-top:2%">
                        <source src="http://mrbj-auto-01/ChannelMapTool/intro.mp4" type="video/mp4">
                    </video>
                </Modal>
            </nav>
        </header>
        <Row style="margin-top:50px;margin-left:1%;margin-right:1%">
            <Col span='14'>
                <Card style='text-align:left'>
                    <p slot="title" style="height:30px">
                        <Icon type="ios-navigate" color='#008B8B' size='20'></Icon>
                        Query Options &nbsp;&nbsp;&nbsp;&nbsp;
                        <Select filterable v-model="envName" style="width:150px" placeholder='Environment...' size="small" v-intro-step="2" v-intro="'Change Environment here...'" v-intro-position="'right'">
                            <Option v-for="item in envlist" :value="item" :key="item">{{ item }}</Option>
                        </Select>
                        <Button @click="getChannelMaps" size="small" type="primary" icon="md-return-right" :loading="querycmloading"></Button>
                        <Select filterable v-model="channelMapId" style="width:250px" placeholder='ChanneMaps...' size="small" :loading="querycmloading" loading-text="Loading...wait me for seconds..." not-found-text="No Data Here."
                        v-intro-step="3" v-intro="'Select ChannelMap here, query action support in this Select.'" v-intro-position="'right'">
                            <Option v-for="item in channelMaplist" :value="item.Id" :key="item.Id">{{ item.Name }}</Option>
                        </Select>
                        <Button @click="getChannels" size="small" type="primary" shape="circle" icon="ios-search" :loading="querychannelloading"></Button>
                    </p>
                    <Row style="margin-top:5px">
                        <Col span="8">
                            <Input v-model="skeyservicecollectionname" placeholder="ServiceCollection Name..." style="width: 150px" size="small" v-intro-step="4" v-intro="'Find target channels in queried results.'" v-intro-position="'right'"></Input>
                            <Button @click="searchbysname" size="small" type="info" shape="circle" icon="ios-search" :loading="sbysnloading"></Button>&nbsp;&nbsp;&nbsp;&nbsp;
                            <Button @click="clearsearchbysname" size="small" type="warning" v-intro-step="5" v-intro="'Clear search.'" v-intro-position="'right'">Clear Search</Button>
                        </Col>
                        <Col span="8">
                            <Button @click="addtoprepare" size="small" type="success" v-intro-step="7" v-intro="'Select Channels and Add to prepare list.'" v-intro-position="'right'">Add->PreList</Button>
                            &nbsp;&nbsp;
                            <Button @click="openprepare" size="small" type="success"  v-intro-step="8" v-intro="'Open Prepare list here.'" v-intro-position="'right'">Open PreList</Button>
                            &nbsp;&nbsp;
                            <Button @click="deletechannels" size="small" type="error"  v-intro-step="9" v-intro="'Delete Selected Channels from ChannelMap.'" v-intro-position="'right'">Delete</Button>
                        </Col>
                        <Col span="8"><Page :total="totchannel" size="small" :page-size="50" @on-change="changePage" v-intro-step="10" v-intro="'Channels page list.'" v-intro-position="'top'"/></Col>
                        <Modal width='30%' height=500
                            v-model="preparemodalopen"
                            title="Prepare List"
                            :styles="{top: '35px',background:'#DCDCDC'}">
                            <Row>
                                <Col span="16">
                                    <Button @click="clearprepare" size="small" type="success">Clear PreList</Button>
                                </Col>
                                <Col span="8">
                                    <Page :total="totpreparechannel" size="small" :page-size="20" @on-change="changepreparePage"/>
                                </Col>
                            </Row>
                            <Table :columns="prepareColumns" :data="pagepreparelist" border size="small"></Table>
                        </Modal>
                        <Modal v-model="qualitylevelmodalopen"
                            title="Change Quality Level">
                            <Row>
                                <Col span="8"><Tag size="small">Quality Level List</Tag></Col>
                                <Col span="16">
                                    <Select filterable v-model="targetqualitylevel" style="width:150px" placeholder='Quality Level...' size="small">
                                        <Option v-for="item in qualitylevellist" :value="item" :key="item">{{ item }}</Option>
                                    </Select>                                
                                </Col>
                            </Row>
                            <div slot="footer">
                                <Button @click="updatequalitylevel" size="small" long type="success">Update</Button>
                            </div>
                        </Modal>
                    </Row>
                    <Scroll :on-reach-edge="handleReachEdge" height='800' loading-text='Loading next 100 records.' v-intro-step="6" v-intro="'Channel list. 50 channels each page.'" v-intro-position="'right'">
                        <Table size="small" :columns="channelColumns" no-data-text='Processing and Loading' :loading='channellistloading' :data="pagechannellist" border @on-row-dbclick="handledbClick" @on-select="selectchannel" @on-select-all="selectchannel"></Table>    
                    </Scroll>
                    <Spin  v-if="qualitylevelloading" size="large" fix></Spin>
                </Card>
            </Col>
            <Col span="10" style="padding-left:10px;">
                <Card  :bordered="false">
                    <p slot="title">
                        <Icon type="md-log-in" color='#D02090' size='20'></Icon>
                        Create Channel Map 
                    </p>
                    <Row>
                        <Col span="8" style="margin-top:5px">ChannelMap Name:</Col>
                        <Col span="16"><Input v-model="cmname" placeholder="channel map name..." v-intro-step="11" v-intro="'Channel Map Name to create ChannelMap.'" v-intro-position="'right'"/></Col>
                    </Row>
                    <Row style="margin-top:5px">
                        <Col span="8" style="margin-top:5px">Package Name:</Col>
                        <Col span="14">
                            <Select
                                v-model="packagename"
                                filterable
                                remote
                                :remote-method="querypackage"
                                :loading="querypackageloading"  placeholder='Package name...' loading-text="Loading...wait me for seconds..." not-found-text="No Data Here."
                                 v-intro-step="12" v-intro="'Subscription Package name here. Remote search support in this Select.'" v-intro-position="'right'">
                                <Option v-for="(option, index) in packagelist" :value="option.Name[0].Value" :key="index">{{option.Name[0].Value}}</Option>
                            </Select>
                        </Col>
                        <Col span="2"  v-intro-step="14" v-intro="'Manager Offers.'" v-intro-position="'top'">
                            <Button @click="openoffer" type="primary">Offers</Button>
                        </Col>
                        <Modal v-model="offermodalopen"
                            title="Manage Offers">
                            <Row>
                                <Col span="4"><Tag color='primary'>Offers:</Tag></Col>
                                <Col span="20">
                                    <Table size="small" :columns="OfferColumns" no-data-text='Processing and Loading' :data="offerslist" border></Table>    
                                </Col>
                            </Row>
                            <Row style='margin-top:10px'>
                                <Col span="4"><Tag color='success'>New Offer:</Tag></Col>
                                <Col span="20">
                                    <Input v-model="newoffername" placeholder="Offer name..." />
                                    <Row style='margin-top:10px'>
                                        <Col span="20">
                                            <Select
                                                v-model="offergroupname"
                                                filterable
                                                multiple
                                                remote
                                                :remote-method="queryoffergroup"
                                                :loading="queryoffergrouploading"  placeholder='offer group...' loading-text="Loading...wait me for seconds..." not-found-text="No Data Here.">
                                                <Option v-for="(option, index) in offergrouplist" :value="option.Data" :key="index">{{option.Name}}</Option>
                                            </Select>
                                        </Col>
                                        <Col span="4">
                                            <Button @click="addoffergroup" type="success">Create</Button>
                                        </Col>
                                    </Row>
                                    <RadioGroup v-model="offerpricebutton" @on-change="offerbuttonchanged">
                                        <Radio label="Free">
                                            <Icon type="md-happy"></Icon>
                                            <span>Free</span>
                                        </Radio>
                                        <Radio label="Subscription">
                                            <Icon type="md-sad"></Icon>
                                            <span>Subscription</span>
                                        </Radio>
                                    </RadioGroup>
                                    <Input v-if="offerprice_open" v-model="offerprice" suffix="logo-usd" placeholder="Price"/>
                                    <Button @click="createoffer" size="small" type="primary">Create New Offer</Button>
                                </Col>
                            </Row>
                        </Modal>
                    </Row>
                    <Row style="margin-top:5px">
                        <Col>
                            <Button @click="createbasicchannelMap" size="small" type="success">Create ChannelMap</Button>
                            &nbsp;&nbsp;&nbsp;&nbsp;
                            <Button @click="updatechannelmap" size="small" type="success">Update ChannelMap</Button>
                            &nbsp;&nbsp;&nbsp;&nbsp;
                            <Button @click="createsvodpackage" size="small" type="success">SubscriptionPackage</Button>                            
                        </Col>
                    </Row>
                </Card>
                <Card v-if="showchannelchart" style="margin-top:5px">
                    <p slot="title">
                        <Icon type="md-checkmark-circle-outline" color='#D02090' size='20'></Icon>
                        Channels Analysis 
                    </p>            
                    <chart :options="channelchart"></chart>               
                </Card> 
                <Card v-if="showsvodchart" style="margin-top:5px">
                    <p slot="title">
                        <Icon type="md-checkmark-circle-outline" color='#D02090' size='20'></Icon>
                        Package Analysis 
                    </p>            
                    <chart :options="svodchart" style='height:300px'></chart>               
                </Card>
                <Card style="margin-top:5px" v-intro-step="13" v-intro="'Operation eventlogs here.'" v-intro-position="'top'">
                    <p slot="title">
                        <Icon type="md-checkmark-circle-outline" color='#D02090' size='20'></Icon>
                        Event Logs 
                    </p>
                    <Row v-for="log in eventloglist" :key="log.val">
                        <Col span="1" v-if="log.type=='info'" style="color:gray;"><Icon type="md-notifications" /></Col>
                        <Col span="1" v-if="log.type=='success'" style="color:green;"><Icon type="md-checkmark" /></Col>
                        <Col span="1" v-if="log.type=='error'" style="color:red;"><Icon type="md-close" /></Col>
                        <Col span="1" v-if="log.type=='warn'" style="color:#FF8C00;"><Icon type="md-warning" /></Col>          
                        <Col span="23" v-if="log.type=='error'" style="color:red">{{log.val}}</Col>
                        <Col span="23" v-if="log.type!='error'">{{log.val}}</Col>
                    </Row>
                </Card>
            </Col>
        </Row>
        <BackTop></BackTop>
    </div>
</template>
<script>
import axios from 'axios'
import Cookies from 'js-cookie';
import Vue from 'vue';
import $ from 'jquery'
import ECharts from 'vue-echarts'
Vue.component('chart', ECharts)
export default {
    data () {
        return {
            envlist:["Ericsson.DevTest","Ericsson.Preprod","Ericsson.OSIM","MFN.ProdA"],
            qualitylevellist:['SD','HD','UHD'],
            channelMaplist:[],
            channelist:[],
            tempchannellist:[],
            pagechannellist:[],
            servicecollectionlist:[],
            preparelist:[],
            pagepreparelist:[],
            selectionchannel:[],
            packagelist:[],
            eventloglist:[],
            offerslist:[],
            offergrouplist:[],
            addoffergrouplist:[],
            server:'',
            envName:'',
            packagename:'',
            svodname:'',
            cmname:'',
            newoffername:'',
            offergroupname:'',
            tempoffergroupname:'',
            qualitylevel:'',
            targetqualitylevel:'',
            targetservicecollectionid:'',
            offerpricebutton:'',
            target:null,
            channelchart:{
                tooltip : {
                    trigger: 'item',
                    formatter: "{a} <br/>{b} : {c} ({d}%)"
                },
                legend: {
                    orient: 'vertical',
                    left: 'left',
                    data: ['Added','Failed','Exist']
                },
                series : [
                    {
                        name: 'Channels',
                        type: 'pie',
                        radius : '55%',
                        center: ['50%', '60%'],
                        data:[
                            {value:0, name:'Added'},
                            {value:0, name:'Failed'},
                            {value:0, name:'Exist'},
                        ],
                        itemStyle: {
                            emphasis: {
                                shadowBlur: 10,
                                shadowOffsetX: 0,
                                shadowColor: 'rgba(0, 0, 0, 0.5)'
                            }
                        }
                    }
                ]
            },
            svodchart:{
                tooltip : {
                    trigger: 'item',
                    formatter: "{a} <br/>{b} : {c} ({d}%)"
                },
                legend: {
                    orient: 'vertical',
                    left: 'left',
                    data: ['Added','Exist','Failed']
                },
                series : [
                    {
                        name: 'Packages',
                        type: 'pie',
                        radius : '55%',
                        center: ['50%', '60%'],
                        data:[
                            {value:0, name:'Added'},
                            {value:0, name:'Exist'},
                            {value:0, name:'Failed'},
                        ],
                        itemStyle: {
                            emphasis: {
                                shadowBlur: 10,
                                shadowOffsetX: 0,
                                shadowColor: 'rgba(0, 0, 0, 0.5)'
                            }
                        }
                    }
                ]
            },
            subscriptionpackage:null,
            newoffer:null,
            skeyservicecollectionname:'',
            channelMapId:0,
            totchannel:0,
            totpreparechannel:0,
            channellistloading:false,
            querycmloading:false,
            querychannelloading:false,
            sbysnloading:false,
            querypackageloading:false,
            qualitylevelloading:false,
            queryoffergrouploading:false,
            preparemodalopen:false,
            showchannelchart:false,
            showsvodchart:false,     
            about_open:false,
            video_open:false,
            offerprice_open:false,
            qualitylevelmodalopen:false,
            servicecollectionmodalopen:false,
            offermodalopen:false,
            offerprice:'0.00',
            channelColumns:[
                {
                    type: 'selection',
                    width: 60,
                },
                {
                    title:'ChannelNumber',
                    key:'ChannelNumber',
                    width:150
                },
                {
                    title:'ServiceCollectionName',
                    key:'ServiceCollectionName',
                    render: (h, params) => {
                        return h('Poptip', {
                            props: {
                                title: 'QualityLevel',
                                placement: 'bottom'
                            }
                        }, [
                            h('Button', {props:{type:'dashed'},
                            on:{
                                click:()=>{
                                    this.qualitylevelloading=true
                                    axios.get('http://mrbj-auto-01/channelmaptool/channelmap/getqualitylevel?env='+this.envName+'&sid='+params.row.ServiceCollectionId).then(
                                        response=>{
                                            this.qualitylevel=response.data
                                            this.qualitylevelloading=false
                                        }
                                    )
                                },

                            }},params.row.ServiceCollectionName),
                            h('div', {
                                slot: 'content',                               
                            }, [
                                h('li', [h('Tag', {props:{color:'#495060',size: 'small'}},'Current Quality Level: '+this.qualitylevel)]),
                                h('li', [h('Button', {props:{type:'primary',size: 'small'},
                                on:{
                                    click:()=>{
                                        this.targetservicecollectionid=params.row.ServiceCollectionId
                                        this.qualitylevelmodalopen=true
                                    }
                                }},'Change Quality Level')]),
                            ])
                        ]);
                    }
                },
                {
                    title:'CallLetter',
                    key:'CallLetter',
                }
            ],
            prepareColumns:[
                {
                    title:'ChannelNumber',
                    key:'ChannelNumber',
                    width:150
                },
                {
                    title:'ServiceCollectionName',
                    key:'ServiceCollectionName'
                },
                {
                    title:'',
                    key:'Remove',
                    width:60,
                    render: (h, params) => {
                        return h('div', [
                            h('Button', {
                                props: {
                                    type:'warning',
                                    size:'small',
                                    shape:'circle',
                                    icon:'md-remove'
                                },
                                on:{
                                    click:()=>{
                                        var index=0
                                        for(var i=0;i<this.preparelist.length;i++){
                                            if(this.preparelist[i].ChannelNumber==params.row.ChannelNumber){
                                                index=i
                                            }
                                        }
                                        if(index>-1){
                                            this.preparelist.splice(index,1)
                                            this.totpreparechannel=this.preparelist.length
                                            if(this.preparelist!=null){
                                                if(this.preparelist.length>20){
                                                    this.pagepreparelist=this.preparelist.slice(0,20)
                                                }else{
                                                    this.pagepreparelist=this.preparelist
                                                }
                                            }
                                        }
                                    }
                                }
                            }, ),
                        ])
                    }
                },
            ],
            OfferColumns:[
                {
                    type:'index',
                    width:50
                },
                {
                    title:'Name',
                    key:'Name'
                },
                {
                    title:'OfferType',
                    key:'OfferType',
                    width:150
                }
            ]
        }
    },
    mounted () {
      this.init()
      var firstload = Cookies.get('firstload');
        if(firstload!='firstload'){
            this.$intro().start(); // start the guide
            Cookies.set('firstload','firstload');
        }  
    },
    methods: {
        init(){
            this.envName=this.envlist[0]         
        },
        getEnvironmentConfig(item){
            axios.get('http://mrbj-auto-01/channelmaptool/channelmap/listenv?sv='+item).then(
                response=>{
                    if(response.data!=null){
                        this.envlist=response.data
                        this.envName=this.envlist[0].Name
                    }
                }
            )
        },
        getChannelMaps(){
            this.querycmloading=true
            axios.get('http://mrbj-auto-01/channelmaptool/channelmap/listchannelmaps?env='+this.envName).then(
                response=>{
                    if(response.data!=null){
                        this.channelMaplist=response.data
                        this.channelMapId=this.channelMaplist[0].Id
                        this.querycmloading=false
                    }
                }
            )
        },
        getChannels(){
            this.$Message.config({
                top: 50
            });
            if(this.envName!=''&&this.channelMapId!=0){
                this.querychannelloading=true
                this.channellistloading=true
                axios.get('http://mrbj-auto-01/channelmaptool/channelmap/listchannelbymapid?env='+this.envName+'&mapid='+this.channelMapId).then(
                    response=>{
                        if(response.data!=null){
                            this.channelist=response.data
                            this.tempchannellist=response.data
                            this.totchannel=this.channelist.length
                            this.pagechannellist=this.channelist.slice(0,50)
                            this.$Message.success("Query done.")
                            this.querychannelloading=false
                            this.channellistloading=false
                        }
                    }
                )
            }else{
                this.$Message.error("Query option cannot be empty.")
            }
        },
        changePage(val){
            if(val*50>this.totchannel){
                this.pagechannellist=this.channelist.slice((val-1)*50,this.totchannel);
            }
            else{
                this.pagechannellist=this.channelist.slice((val-1)*50,val*50);
            }
        },
        changepreparePage(val){
            if(val*20>this.totchannel){ 
                this.pagepreparelist=this.preparelist.slice((val-1)*20,this.totchannel);
            }
            else{
                this.pagepreparelist=this.preparelist.slice((val-1)*20,val*20);
            }
        },
        searchbysname(){
            if(this.channelist!=null){
                var searchchannellist=[]
                for(var i=0;i<this.channelist.length;i++){
                    if(this.channelist[i].ServiceCollectionName.indexOf(this.skeyservicecollectionname)>-1){
                        searchchannellist.push(this.channelist[i])
                    }
                }
                this.channelist=searchchannellist
                this.totchannel=this.channelist.length
                if(this.channelist.length<50){
                    this.pagechannellist=this.channelist
                }else{
                    this.pagechannellist=this.channelist.slice(0,50)
                }
            }else{
                this.$Message.config({
                    top: 50
                });
                this.$Message.error("Please Execute Query Channel first")
            }
        },
        clearsearchbysname(){
            this.channelist=this.tempchannellist
            this.totchannel=this.channelist.length
            if(this.channelist.length<50){
                this.pagechannellist=this.channelist
            }else{
                this.pagechannellist=this.channelist.slice(0,50)
            }
        },
        selectchannel(selection){
            this.selectionchannel=selection
        },
        openprepare(){
            this.preparemodalopen=true
        },
        clearprepare(){
            this.preparelist=[]
            this.pagepreparelist=[]
            this.totpreparechannel=0
        },
        addtoprepare(){
            var addedcount=0
            if(this.preparelist.length>0){
                for(var i=0;i<this.selectionchannel.length;i++){
                    var addflag=true
                   for(var j=0;j<this.preparelist.length;j++){
                       if(this.selectionchannel[i].ChannelNumber==this.preparelist[j].ChannelNumber){
                           addflag=false
                       }
                   }
                   if(addflag){
                       this.preparelist.push(this.selectionchannel[i])
                       addedcount++
                   }
                }
            }else{
                this.selectionchannel.forEach(channel => {
                    this.preparelist.push(channel)
                    addedcount++
                })
            }
            this.totpreparechannel+=addedcount
            this.pagepreparelist=this.preparelist.slice(0,20)
            this.$Message.config({
                    top: 50
                });
            if(addedcount>0){
                this.$Message.success("Added "+addedcount+" channels to prepare list.")
            }else{
                this.$Message.warning("No new channels added to prepare list.")
            }
            this.preparemodalopen=true
        },
        querypackage(query){
            if(query!==''){
                this.svodname=query
                this.querypackageloading=true
                axios.get('http://mrbj-auto-01/channelmaptool/channelmap/getsvodpackagebypartialname?env='+this.envName+'&name='+query).then(
                    response=>{
                        this.packagelist=response.data;
                        this.querypackageloading=false
                    }
                )
            }
        },
        createbasicchannelMap(){
            var that=this
            that.$Message.config({
                top: 50
            });
            that.eventloglist=[]
            var addedcount=0
            var failedcount=0
            var existcount=0
            that.showchannelchart=true
            that.showsvodchart=false
            if(that.cmname!=""){
                var find=false
                that.eventloglist.push({type:'info',val:"Check if Channel Map exist."})
                $.ajax({
                    url:'http://mrbj-auto-01/channelmaptool/channelmap/listchannelmaps?env='+that.envName,
                    type:'GET',
                    async:false,
                    timeout:3000,
                    dataType:'json',
                    success:function(data){
                        that.channelMaplist=data
                    }
                })
                that.channelMaplist.forEach(channelmap => {
                    if(channelmap.Name==that.cmname){
                        find=true
                        that.target=channelmap
                    }
                });
                if(!find){
                    that.eventloglist.push({type:'info',val:"Not exist. Creating Channel Map..."})
                    $.ajax({
                        url:'http://mrbj-auto-01/channelmaptool/channelmap/createbasicchmap?env='+that.envName+'&name='+that.cmname,
                        type:'GET',
                        async:false,
                        timeout:5000,
                        dataType:'json',
                        success:function(data){
                            that.target=data
                            that.eventloglist.push({type:'success',val:"Target Channel Map Created."})
                            that.eventloglist.push({type:'info',val:"Id: "+that.target.Id+". Name: "+that.target.Name})
                        }
                    })
                    if(that.target!=null){
                        if(that.preparelist.length>0){
                            for(var i=0;i<that.preparelist.length;i++){
                                var chl=that.preparelist[i]
                                $.ajax({
                                    url:'http://mrbj-auto-01/channelmaptool/channelmap/addchannels?env='+that.envName+'&cid='+chl.ChannelNumber+'&scid='+chl.ServiceCollection.Id+'&mapid='+that.target.Id,
                                    type:'GET',
                                    async:false,
                                    timeout:5000,
                                    dataType:'json',
                                    success:function(data){
                                        that.eventloglist.push({type:'info',val:data})
                                        if(data!=null){
                                            if(data.toString().indexOf('Added')>-1){
                                                addedcount++
                                            }else if(data.toString().indexOf('failed')>-1){
                                                failedcount++
                                            }else if(data.toString().indexOf('Exist')>-1){
                                                existcount++
                                            }
                                        }
                                    }
                                })
                            }
                        }else{
                            that.$Message.error("Empty Preparelist!")
                        }
                        
                    }else{
                        that.eventloglist.push({type:'error',val:"Failed to create channel map."})
                    }
                }else{
                    that.eventloglist.push({type:'warn',val:"Channel Map with same name already exist. Please try again with another name."})
                    that.eventloglist.push({type:'info',val:"Id: "+that.target.Id+". Name: "+that.target.Name})
                }
            }else{
                that.$Message.error("Empty value not allowed for channel map name.")
                that.eventloglist.push({type:'error',val:"Failed to create channel map due to empty channel map name."})
            }

            that.channelchart={
                tooltip : {
                    trigger: 'item',
                    formatter: "{a} <br/>{b} : {c} ({d}%)"
                },
                legend: {
                    orient: 'vertical',
                    left: 'left',
                    data: ['Added','Failed','Exist']
                },
                series : [
                    {
                        name: 'Channels',
                        type: 'pie',
                        radius : '55%',
                        center: ['50%', '60%'],
                        data:[
                            {value:addedcount, name:'Added'},
                            {value:failedcount, name:'Failed'},
                            {value:existcount, name:'Exist'},
                        ],
                        itemStyle: {
                            emphasis: {
                                shadowBlur: 10,
                                shadowOffsetX: 0,
                                shadowColor: 'rgba(0, 0, 0, 0.5)'
                            }
                        }
                    }
                ]
            };
        },
        updatechannelmap(){
            var that=this
            that.$Message.config({
                top: 50
            });
            that.eventloglist=[]
            var addedcount=0
            var failedcount=0
            var existcount=0
            that.showchannelchart=true
            that.showsvodchart=false
            if(that.cmname!=""){
                var find=false
                that.eventloglist.push({type:'info',val:"Check if Channel Map exist."})
                $.ajax({
                    url:'http://mrbj-auto-01/channelmaptool/channelmap/listchannelmaps?env='+that.envName,
                    type:'GET',
                    async:false,
                    timeout:5000,
                    dataType:'json',
                    success:function(data){
                        that.channelMaplist=data
                    }
                })
                that.channelMaplist.forEach(channelmap => {
                    if(channelmap.Name==that.cmname){
                        find=true
                        that.target=channelmap
                    }
                });
                if(find){
                    that.eventloglist.push({type:'info',val:"Found Target Channel Map."})
                    if(that.preparelist.length>0){
                        for(var i=0;i<that.preparelist.length;i++){
                            var chl=that.preparelist[i]
                            $.ajax({
                                url:'http://mrbj-auto-01/channelmaptool/channelmap/addchannels?env='+that.envName+'&cid='+chl.ChannelNumber+'&scid='+chl.ServiceCollection.Id+'&mapid='+that.target.Id,
                                type:'GET',
                                async:false,
                                timeout:5000,
                                dataType:'json',
                                success:function(data){
                                    that.eventloglist.push({type:'info',val:data})
                                    if(data!=null){
                                        if(data.toString().indexOf('Added')>-1){
                                            addedcount++
                                        }else if(data.toString().indexOf('failed')>-1){
                                            failedcount++
                                        }else if(data.toString().indexOf('Exist')>-1){
                                            existcount++
                                        }
                                    }
                                }
                            })
                        }
                    }else{
                        that.$Message.error("Empty Preparelist!")
                    }
                }else{
                    that.eventloglist.push({type:'warn',val:"Channel Map not exist, Please create first."})
                }
            }else{
                that.$Message.error("Empty value not allowed for channel map name.")
                that.eventloglist.push({type:'error',val:"Failed to update channel map due to empty channel map name."})
            }
            that.channelchart={
                tooltip : {
                    trigger: 'item',
                    formatter: "{a} <br/>{b} : {c} ({d}%)"
                },
                color: [
                    "#3fb1e3",
                    "#CD2626",
                    "#1C1C1C",
                ],
                legend: {
                    orient: 'vertical',
                    left: 'left',
                    data: ['Added','Failed','Exist']
                },
                series : [
                    {
                        name: 'Channels',
                        type: 'pie',
                        radius : '55%',
                        center: ['50%', '60%'],
                        data:[
                            {value:addedcount, name:'Added'},
                            {value:failedcount, name:'Failed'},
                            {value:existcount, name:'Exist'},
                        ],
                        itemStyle: {
                            emphasis: {
                                shadowBlur: 10,
                                shadowOffsetX: 0,
                                shadowColor: 'rgba(0, 0, 0, 0.5)'
                            }
                        }
                    }
                ]
            };
        },
        deletechannels(){
            var that=this
            that.$Message.config({
                top: 50
            });
            if(that.selectionchannel.length>0){
                var deletedcount=0
                that.selectionchannel.forEach(chl => {
                    $.ajax({
                        url:'http://mrbj-auto-01/channelmaptool/channelmap/removechannels?env='+that.envName+'&cid='+chl.ChannelNumber+'&mapid='+that.channelMapId,
                        type:'GET',
                        async:false,
                        timeout:5000,
                        dataType:'json',
                        success:function(data){
                            if(data){
                                deletedcount++
                                var index=-1
                                for(var i=0;i<that.channelist.length;i++){
                                    if(chl.ChannelNumber==that.channelist[i].ChannelNumber){
                                        index=i
                                        break
                                    }
                                }
                                if(index>-1){
                                    that.channelist.splice(index,1)
                                    that.pagechannellist=that.channelist.slice(0,50)
                                    that.totchannel=that.channelist.length
                                }
                                that.eventloglist.push({type:'info',val:"Channel: "+chl.ChannelNumber+' deleted,'})
                            }
                        }
                    })
                });
                this.$Message.info('Totaly deleted '+deletedcount+' channles.')
            }else{
                that.$Message.error('Please select target channels first.')
            }
        },
        updatequalitylevel(){
            this.$Message.config({
                top: 50
            });
            axios.get('http://mrbj-auto-01/channelmaptool/channelmap/updatequalitylevel?env='+this.envName+'&sid='+this.targetservicecollectionid+'&qualitylevel='+this.targetqualitylevel).then(
                response=>{
                    if(response.data){
                        this.$Message.success('Quality Level Updated to '+this.targetqualitylevel)
                    }else{
                        this.$Message.error('Failed to udpate Quality Level')
                    }
                }
            )
        },
        createsvodpackage(){
            var that=this
            var targetname=that.svodname
            var exist=false
            var addedcount=0
            var existcount=0
            var failedcount=0
            that.showchannelchart=false
            that.showsvodchart=true
            that.$Message.config({
                top: 50
            });
            that.eventloglist=[]
            console.log(targetname)
            console.log(that.packagename)
            if(that.packagename!=''){
                targetname=that.packagename
                exist=true
            }else{
                that.packagelist.forEach(svodpackage => {
                    if(that.svodname==svodpackage.Name[0].Value){
                        exist=true
                    }
                });
            }
            if(!exist){
                $.ajax({
                    url:'http://mrbj-auto-01/channelmaptool/channelmap/createbasicsvodpackage?env='+this.envName+'&name='+targetname,
                    type:'GET',
                    async:false,
                    timeout:5000,
                    dataType:'json',
                    success:function(data){
                        that.subscriptionpackage=data
                        if(that.subscriptionpackage!=null){
                            that.eventloglist.push({type:'success',val:"Subscription Package Created."})
                            that.eventloglist.push({type:'info',val:"Id: "+that.subscriptionpackage.Id+' Name: '+that.subscriptionpackage.Name[0].Value})
                        }
                    }
                })
            }else{
                that.eventloglist.push({type:'warn',val:'Same Subscription Package already exist.'})
                that.packagelist.forEach(svodpack => {
                    if(svodpack.Name[0].Value==targetname){
                        that.subscriptionpackage=svodpack
                    }
                });
                that.eventloglist.push({type:'info',val:'Id: '+that.subscriptionpackage.Id+' Name: '+that.subscriptionpackage.Name[0].Value})
            }
            console.log(that.subscriptionpackage.Id)
            if(that.subscriptionpackage!=null){
                if(that.preparelist.length>0){
                    that.preparelist.forEach(channel => {
                        $.ajax({
                            url:'http://mrbj-auto-01/channelmaptool/channelmap/addservicecollectiontosvodpackage?env='+this.envName+'&pid='+that.subscriptionpackage.Id+'&sid='+channel.ServiceCollection.Id,
                            type:'GET',
                            async:false,
                            timeout:5000,
                            dataType:'json',
                            success:function(data){
                                that.eventloglist.push({type:'info',val:data})
                                if(data!=null){
                                    if(data.toString().indexOf('Added')>-1){
                                        addedcount++
                                    }else if(data.toString().indexOf('Exist')>-1){
                                        existcount++
                                    }
                                }
                            },error:function(){
                                failedcount++
                            }

                        })
                    });
                }else{
                    that.$Message.error("Empty Preparelist!")
                }
            }
            that.svodchart={
                tooltip : {
                    trigger: 'item',
                    formatter: "{a} <br/>{b} : {c} ({d}%)"
                },
                color: [
                    "#3fb1e3",
                    "#1C1C1C",
                    "#CD2626",
                ],
                legend: {
                    orient: 'vertical',
                    left: 'left',
                    data: ['Added','Exist','Failed']
                },
                series : [
                    {
                        name: 'Packages',
                        type: 'pie',
                        radius : '55%',
                        center: ['50%', '60%'],
                        data:[
                            {value:addedcount, name:'Added'},
                            {value:existcount, name:'Exist'},
                            {value:failedcount, name:'Failed'},

                        ],
                        itemStyle: {
                            emphasis: {
                                shadowBlur: 10,
                                shadowOffsetX: 0,
                                shadowColor: 'rgba(0, 0, 0, 0.5)'
                            }
                        }
                    }
                ]
            };
        },
        wakeuserguide(){
            this.$intro().start() // start the guide
        },
        wakeabout(){
            this.about_open=true
        },
        wakevideo(){
            this.video_open=true
        },
        openoffer(){
            var targetname=this.svodname
            var pid=''
            var exist=false
            this.offerpricebutton='Free'
            this.$Message.config({
                top: 50
            });
            if(this.packagename!=''){
                targetname=this.packagename
            }
            this.packagelist.forEach(svodpackage => {
                if(targetname==svodpackage.Name[0].Value){
                    exist=true
                    console.log(svodpackage)
                    pid=svodpackage.Id
                }
            });
            if(targetname!=''){
                if(!exist){
                    this.$Message.error('Subscription Package Not Exist. Please create subscription package first.')
                }else{
                    this.offermodalopen=true
                    axios.get('http://mrbj-auto-01/channelmaptool/channelmap/getoffers?env='+this.envName+'&pid='+pid).then(
                        response=>{
                            this.offerslist=response.data
                        }
                    )
                }
            }else{
                this.$Message.error('Empty subscription name or selection not allowed')
            }
        },
        queryoffergroup(query){
            this.tempoffergroupname=query
            axios.get('http://mrbj-auto-01/channelmaptool/channelmap/getsubscriberoffergroupsbypartialname?env='+this.envName+'&partialname='+query).then(
                response=>{
                    this.offergrouplist=response.data
                }
            )
        },
        createoffer(){
            var packageid=''
            this.packagelist.forEach(pack => {
                if(this.packagename==pack.Name[0].Value){
                    packageid=pack.Id
                }
            });
            var dt=''
            this.offergroupname.forEach(ofdt => {
                dt+=ofdt+'_'
            });
            this.$Message.config({
                top: 50
            });
            axios.get('http://mrbj-auto-01/channelmaptool/channelmap/createoffer?env='+this.envName+'&packageid='+packageid+'&offername='+this.newoffername+'&offertype='+this.offerpricebutton+'&price='+this.offerprice+'&dt='+dt).then(
                response=>{
                    this.newoffer=response.data
                    if(this.newoffer!=null){
                        this.$Message.success('New Offer: '+this.newoffer.Name+' Created.')
                        axios.get('http://mrbj-auto-01/channelmaptool/channelmap/getoffers?env='+this.envName+'&pid='+packageid).then(
                            response=>{
                                this.offerslist=response.data
                            }
                        )
                    }else{
                        this.$Message.error('Failed to create new offer.')
                    }
                }
            )
        },
        addoffergroup(){
            this.$Message.config({
                top: 50
            });
            var exist=false
            this.offergrouplist.forEach(offergroup => {
                if(this.tempoffergroupname==offergroup.Name){
                    exist=true
                }
            });
            if(!exist){
                axios.get('http://mrbj-auto-01/channelmaptool/channelmap/createoffergroup?env='+this.envName+'&offergroupname='+this.tempoffergroupname).then(
                    response=>{
                        var target=response.data
                        if(target!=null){
                            this.$Message.success('Offer Group: '+this.tempoffergroupname+' Created.')
                        }
                    }
                )
            }else{
                this.$Message.error('Offer Group Already Exist.')
            }
        },
        offerbuttonchanged(){
            if(this.offerpricebutton=='Subscription'){
                this.offerprice_open=true
            }else if(this.offerpricebutton=='Free'){
                this.offerprice_open=false
            }
        },
        handleReachEdge(){

        },
        handledbClick(){
            this.servicecollectionmodalopen=true
        },
        exportchannelmap(){

        },
        importchannelmap(){

        }
    }
}
</script>
<style>
.i-icon {
        background:url('../assets/loading1.gif') no-repeat;
    }
.ivu-table .table-info-row td{
    background-color: #DCDCDC;
    color: #000;
    -moz-box-shadow:2px 0px 5px #000; 
    -webkit-box-shadow:2px 0px 5px #000; 
    box-shadow:2px 0px 5px #000;
}
.ivu-table-small td {
    height: 36px;
}
.vsp-c-header {
    display: -ms-flexbox;
    display: flex;
    -ms-flex-pack: justify;
    justify-content: space-between;
    -ms-flex-align: center;
    align-items: center;
    position: fixed;
    top: 0;
    right: 0;
    left: 0;
    width: 100%;
    background-color: #ffffff;
    z-index: 1999;
    padding: 0 1rem;
    box-shadow: 0 0 30px 0 rgba(0, 0, 0, 0.35);
}
.vsp-c-header .vsp-c-navigation__hamburger-btn {
    display: none;
}
.vsp-c-header .vsp-c-navigation {
    display: -ms-flexbox;
    display: flex;
    -ms-flex-align: center;
    align-items: center;
}
.vsp-c-header .vsp-c-navigation a:not(.vsp-c-btn) {
    color: #333333;
    font-weight: 300;
    padding: 1.8rem 0 1.65rem;
    border-bottom: .2rem solid transparent;
}
.vsp-c-header .vsp-c-navigation a {
    margin-left: 3rem;
}
.vsp-u-text-uppercase {
    text-transform: uppercase;
}
.demo-spin-icon-load{
        animation: ani-demo-spin 1s linear infinite;
    }
    @keyframes ani-demo-spin {
        from { transform: rotate(0deg);}
        50%  { transform: rotate(180deg);}
        to   { transform: rotate(360deg);}
    }
    .demo-spin-col{
        height: 100px;
        position: relative;
        border: 1px solid #eee;
    }
</style>

