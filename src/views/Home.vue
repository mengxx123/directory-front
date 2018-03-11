<template>
    <my-page title="目录结构生成" :page="page">
        <div class="upload-box">
            <div ref="dropArea" class="drop-box"
                 @dragenter="handleDragEnter($event)"
                 @dragleave="handleDragLeave($event)"
                 @drop="handleDrop($event)"
                 @dragover='allowDrop($event)'>
                <ui-raised-button class="btn" label="选择文件夹" icon="folder">
                    <input class="ui-file-button" type="file" webkitdirectory directory @change="handleChange($event)">
                </ui-raised-button>
                <div class="text">或者把文件夹拖到这里</div>
            </div>
        </div>
        <!--<div v-if="resultObj">-->
            <!--<div>{{ resultObj }}</div>-->
            <!--<ul>-->
                <!--<li v-for="item of resultObj">* {{ resultObj[item] }}</li>-->
            <!--</ul>-->
        <!--</div>-->
        <div class="btns">
            <!-- <ui-raised-button class="btn" label="配置" icon="settings" @click="config" /> -->
            <ui-raised-button class="btn btn-copy" label="复制" icon="content_copy" v-if="result" :data-clipboard-text="result"  />
            <ui-raised-button class="btn" label="下载" icon="file_download" v-if="result" @click="download" />
        </div>
        <pre class="pre" v-if="result"><code>{{ result }}</code></pre>
        <ui-dialog :open="dialog" title="配置" @close="close">
            <div>缩进类型</div>
            <div>
                <ui-radio label="空格" name="group" nativeValue="simple1" v-model="value" class="demo-radio"/>
            </div>
            <div>
                <ui-radio label="Tab" name="group" nativeValue="simple2" v-model="value"  class="demo-radio"/>
            </div>
            <ui-text-field v-model.number="depth" label="目录读取深度" hintText="1-10"/>
            <ui-flat-button slot="actions" @click="close" primary label="取消"/>
            <ui-flat-button slot="actions" @click="close" primary label="重置"/>
            <ui-flat-button slot="actions" primary @click="close" label="确定"/>
        </ui-dialog>
    </my-page>
</template>

<script>
    /* eslint-disable */
    import dirReader from '@/util/dirReader'
    const treeify = require('treeify')
    const Clipboard = window.Clipboard
    const saveAs = window.saveAs

    export default {
        data () {
            return {
                result: '',
                resultObj: {
                    a: null,
                    b: {
                        c: null,
                        d: null
                    },
                    e: null
                },
                dialog: false,
                value: 'simple1',
                depth: 3,
                page: {
                    menu: [
                        {
                            type: 'icon',
                            icon: 'help',
                            to: '/directory/help'
                        }
                    ]
                }
            }
        },
        mounted() {
            this.clipboard = new Clipboard('.btn-copy')
            this.clipboard.on('success', function(e) {
                console.info('Action:', e.action);
                console.info('Text:', e.text);
                console.info('Trigger:', e.trigger);
                e.clearSelection();
            });
            this.clipboard.on('error', function(e) {
                console.error('Action:', e.action);
                console.error('Trigger:', e.trigger);
            });
        },
        destroyed() {
            this.clipboard.destroy()
        },
        methods: {
            handleDragEnter(e) {
                console.log('handleDragEnter')
                e.preventDefault()
            },
            handleDragLeave(e) {
                console.log('handleDragLeave')
                e.preventDefault()
            },
            handleDrop(e) {
                console.log('drop')
                console.log(e)
                this.execReader(e.dataTransfer)
                console.log(e.dataTransfer)
            },
            allowDrop(e) {
                e.preventDefault()
            },
            handleChange(e) {
                //判断是否选中文件
//                var file=$("#fileFolder").val();
//                if(file!=''){
//                    $("#msg").text('');
//                }
                var files = e.target.files
                if (!files || !files.length) {
                    return
                }
                console.log('这是啥')
                console.log(e.target)
                this.execReader({
                    files: files
                })
                //文件数量
                // actual_filesCount = files.length;
                // if(actual_filesCount > filesCount){
                //     $("#msg").text("文件过多，单次最多可上传"+filesCount+"个文件");
                //     return;
                // }
                // for (var i = 0, f; f = files[i]; ++i){
                //     actual_filesSize += f.size;
                //     if(actual_filesSize > filesSize){
                //         $("#msg").text("单次文件夹上传不能超过"+filesSize/1024/1024+"M");
                //         return;
                //     }
                // }
            },
            execReader(dataTransfer) {
                dirReader.exec(dataTransfer, {
                    maxDepth: 3,
                    onComplete: paths => {
                        console.log('完成', paths)
                        paths = paths.sort()
                        this.result = this.arrToObject(paths)
                    }
                })
            },
            config() {
                this.dialog = true
            },
            open () {
                this.dialog = true
            },
            close () {
                this.dialog = false
            },
            download() {
                var blob = new Blob([this.result], {type: "text/plain;charset=utf-8"});
                saveAs(blob, this.root + ".txt");
            },
            arrToObject(arr) {
                let obj = {}
                let curObj = {}
                let lastObj
                let lastPath
                let root = null
                for (let item of arr) {
                    let paths = item.split('/')
                    for (let i = 0; i < paths.length; i++) {
                        let path = paths[i]
                        if (i === 0) {
                            curObj = obj
                            if (!root) {
                                root = path
                            }
                        } else {
                            if (!curObj) {
                                lastObj[lastPath] = {}
                                curObj = lastObj[lastPath]
                            } else {
                            }
                            if (!curObj[path]) {
                                curObj[path] = null
                            }
                            lastObj = curObj
                            lastPath = path
                            curObj = curObj[path]
                        }
                    }
                }
                this.root = root
                this.resultObj = obj
                return root + '\n' + treeify.asTree(obj, true)
            }
        }
    }
</script>

<style lang="scss" scoped>
    .ui-file-button {
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        opacity: 0;
        cursor: pointer;
    }
    .btns {
        margin-bottom: 16px;
        .btn {
            margin-right: 8px;
        }
    }
    .pre {
        margin-bottom: 16px;
        code {
            padding: 16px;
        }
    }
    .upload-box {
        max-width: 320px;
        margin-bottom: 16px;
        text-align: center;
        .drop-box{
            height: 180px;
            padding-top: 56px;
            border: 2px dashed #999;
        }
        .text {
            margin-top: 16px;
            margin-bottom: 16px;
            color: #999;
            text-align: center;
        }
    }

</style>
