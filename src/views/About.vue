<template>
    <my-page title="关于8993">
        <div>这是关于页面</div>
        <div>版本：{{ version }}</div>
    </my-page>
</template>

<script>
    /* eslint-disable */
    const treeify = require('treeify')

    export default {
        data () {
            return {
                version: ''
            }
        },
        mounted() {
            this.init()
        },
        methods: {
            init() {
                let arr = ["交接文件/123.txt", "交接文件/jianan-front.zip", "交接文件/jianan-mobile.zip", "交接文件/zktw-front.zip", "交接文件/zktw-mobile.zip", "交接文件/说明.md", "交接文件/asd/1.txt", "交接文件/asd/2.txt"]
                let obj = this.arrToObject(arr)
                console.log(obj)
                console.log(treeify.asTree(obj, true))
            },
            arrToObject(arr) {
                let obj = {}
                let curObj = {}
                let lastObj
                let lastPath
                let root = null
                for (let item of arr) {
                    console.log('ietm', item)
                    let paths = item.split('/')
                    for (let i = 0; i < paths.length; i++) {
                        let path = paths[i]
                        console.log('路径：' + path)
                        if (i === 0) {
                            curObj = obj
                            if (!root) {
                                root = path
                            }
                        } else {
                            if (!curObj) {
                                console.log('我看')
                                lastObj[lastPath] = {}
                                curObj = lastObj[lastPath]
                            } else {
                                console.log('不为空', curObj)
                            }
                            if (!curObj[path]) {
                                curObj[path] = null
                            }
                            lastObj = curObj
                            lastPath = path
                            curObj = curObj[path]
                        }
                        console.log('过程', JSON.stringify(obj))
                    }
                }
                console.log(obj)
                console.log(root)
                return obj
            }
        }
    }
</script>

<style scoped>
</style>
