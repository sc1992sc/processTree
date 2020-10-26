<template>
    <div :ref="treeRefName"
    :class="treeClassName">
        <div v-for="(item) in convertData" :key="item.uuid" 
        :class="[isChild?'process-tree-childNodes-row':'process-tree-roots',
            isChild && item.isLong?'long-with-line':'']" 
        :style="isChild?{}:rootStyle">
            <div class="line" v-if="item.isLong"></div>

            <span class="process-tree-node"
            :class="isLeaftNode(item)">{{item.name}}</span>

            <div class="process-tree-childNodes"
            v-if="item.children"
            :class="item.children.length>1?'multiply-node':'single-node'">
                <processTree 
                :data="item.children || []" 
                :isChild="true"/>
            </div>
        </div>
    </div>
</template>

<script>
import uuidv4 from 'uuid'
export default {
    name:'processTree',
    props:{
        data:{
            type:Array,
            default:()=>{
                return []
            }
        },
        isChild:{
            type:Boolean,
            default:false
        }
    },
    data(){
        return {
            convertData:this.convert(this.data),
            rootStyle:{}
        }
    },
    watch:{
        data(){
            this.convertData=this.convert(this.data);
        }
    },
    computed:{
        treeRefName(){
            return this.isChild?'childTree':'baseTree'
        },
        treeClassName(){
            return this.isChild?'':'process-tree'
        }
    },
    methods:{
        initDomWidth(){
            let leafs=document.getElementsByClassName('leaf-node');
            leafs=Array.from(leafs);
            leafs=leafs.map(i=>{
                let total=this.getOffset(i,'offsetLeft');
                return total
            })
            
            this.rootStyle={width:Math.max(...leafs)*1.5+'px'}
        },
        getOffset(obj,offsetDir){		
            var realNum = obj[offsetDir];
            var positionParent = obj.offsetParent;  //获取上一级定位元素对象
                
            while(positionParent != null){
                realNum += positionParent[offsetDir];
                positionParent = positionParent.offsetParent;
            }
            return realNum;
        },
        convert(arr){
            return arr.map((item)=>{
                item.uuid=uuidv4();
                if(item.children && item.children.length>0){
                    item.children=this.convert(item.children);
                }
                return item
            })
        },
        isLeaftNode(data){
            return (data.children && data.children.length>0)?"":"leaf-node";
        }
    }
}
</script>

<style scoped>
.process-tree{
    padding: 10px;
    overflow: scroll;
    padding-bottom: 27px;
    width: 100%;
    padding-right:0;
    font-size:0;
    line-height:0
}
.process-tree-roots{
    width:250%;
    margin-bottom:20px;
}
.single-node::before{
    content:"";
    display:block;
    position: absolute;
    width:23px;
    height:3px;
    background:rgba(203,221,238,1);
    left:-23px;
    top:52%;
}
.multiply-node::before{
    content:"";
    display:block;
    position: absolute;
    width:3px;
    height:100%;
    background:rgba(203,221,238,1);
    left:-23px;
    top:0;
}

.process-tree-node{
    position: relative;
    padding:6px 10px;
    background:rgba(203,221,238,1);
    border-radius:2px;
    color:#333;
    display:inline-block;
    cursor: pointer;
    min-width:80px;
    text-align:center;
    font-size: 12px;
    line-height: 1.8em;
    vertical-align: middle;
    min-height:20px;
}
.process-tree-node::after{
    content:'';
    display:block;
    width:20px;
    height:3px;
    background:rgba(203,221,238,1);
    position:absolute;
    left:100%;
    top:50%
}
.leaf-node::after{
    display:none
}

.process-tree-childNodes{
    position: relative;
    display: inline-block;
    vertical-align: middle;
    margin-left:43px;
    top: -.5px;
}
.process-tree-childNodes>div{
    display:flex;
    flex-direction: column;
    justify-content:space-between;
}

.multiply-node .process-tree-childNodes-row::before{
    content:"";
    display:block;
    position: absolute;
    width:20px;
    height:3px;
    background:rgba(203,221,238,1);
    left:-20px;
    top:50%;
}
.multiply-node .process-tree-childNodes-row:first-child::after,
.multiply-node .process-tree-childNodes-row:last-child::after{
    content:'';
    position:absolute;
    display:block;
    width:4px;
    height:50%;
    background:#fff;
    left:-23px;
}
.multiply-node .long-with-line:first-child::after,
.multiply-node .long-with-line:last-child::after{
    left:-166px
}
.multiply-node .process-tree-childNodes-row:first-child::after{
    top:0px
}
.multiply-node .process-tree-childNodes-row:last-child::after{
    bottom:-4px;
}

.process-tree-childNodes-row{
    position: relative;
    margin-bottom:10px;
}
.process-tree-childNodes-row:last-child{
    margin-bottom:0
}

.long-with-line{
    margin-left:142px;
}
.line{
    position: absolute;
    width:142px;
    height:3px;
    background-color:rgba(203,221,238,1);
    top:50%;
    left:-161px
}
</style>
