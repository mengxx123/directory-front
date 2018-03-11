<template>
    <my-page title="目录结构生成 - 帮助" backable>
        <ui-article>
            <h2>介绍</h2>
            <p>一个生成目录结构的小工具。可用于项目 README 文件中，用来介绍项目的目录结构。</p>
            <p>注意：下载后的文件请用文本编辑器打开，如果用 Windows 自带的记事本打开，无法看到换行的效果。</p>
            <p>生成的效果如下：</p>
            <pre><code>project
├─ 123.txt
├─ asd
│  ├─ 1.txt
│  ├─ 2.txt
│  └─ efg
│     └─ 3.txt
├─ LICENSE
└─ README.md
</code></pre>
            <h2>使用命令</h2>
            <p>在 Windows 或 Linux 机器上，也可以使用 tree 命令生成目录。</p>
            <p><code>tree /f > output.txt</code></p>
            <p>生成的效果如下：</p>
            <pre><code>D:.
│  123.txt
│  LICENSE
│  README.md
│
├─asd
│  │  1.txt
│  │  2.txt
│  │
│  └─efg
│          3.txt
│
└─static
</code></pre>
        </ui-article>
    </my-page>
</template>
