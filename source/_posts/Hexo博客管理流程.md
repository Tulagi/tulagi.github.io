---
title: Hexo博客管理流程
categories:
  - hexo
tags:
  - hexo
  - blog
date: 2018-12-02 23:41:12
---

> 记录下使用hexo写博客的流程

<!-- more -->

### Hexo静态博客写作与发布流程：

1. 用 `Markdown` 在 `Boostnote` 或 `Typora` 中写本地博客，推荐使用 `Boostnote` ，源文件是 `cson` 格式方便移植，且所有文章统一管理；
2. 执行 `hexo new post {文章标题}` 生成新博客文件；
3. 将本地博客内容复制到新生成博客文件的正文处(more以下)，博客的title就是本地博客的标题(hexo new生成的文件名)，more分隔符以上简单描述该文信息，并填写tag和categories；
4. 将本地博客涉及的图片复制到文章标题同名的文件夹中，并全局替换图片文件名前缀路径为文章标题目录名；
5. 执行 `hexo clean && hexo g && hexo d` 发布静态博客。
> hexo new title -> 拷贝正文 -> 写描述 -> 写tag -> 写categories -> 替换图片路径 -> 发布

### Hexo文件上传备份流程：

1. 下载hexo分支，该分支保存的是hexo博客的业务文件：

    ```
    git clone -b hexo git@github.com:Tulagi/tulagi.github.io.git branch_of_hexo
    ```

2. 执行 `backup_hexo.sh` 上传本地hexo博客业务文件：

    ```
    #!/bin/bash

    hexo_root_dir=/home/hexo
    hexo_src_dir=${hexo_root_dir}/hexo
    hexo_dest_dir=${hexo_root_dir}/hexo_backup

    cd ${hexo_root_dir}
    [ -d ${hexo_dest_dir} ] && { echo "INFO: hexo backup dir exist"; exit 1; }

    # 1. 下载hexo分支文件：
    git clone -b hexo git@github.com:Tulagi/tulagi.github.io.git ${hexo_dest_dir}

    # 2. 删除当前该分支所有文件：
    cd ${hexo_dest_dir}
    git rm -r ${hexo_dest_dir}/{_config.yml,.gitignore,node_modules.tree,package.json,package-lock.json,scaffolds,source,themes.tar.gz}

    # 3. 复制本地hexo目录文件：
    cp -r ${hexo_src_dir}/{_config.yml,.gitignore,package.json,package-lock.json,scaffolds,source} ${hexo_dest_dir}

    # 4. 记录 node_modules 中的模块
    tree -a ${hexo_src_dir}/node_modules/ > ${hexo_dest_dir}/node_modules.tree
    tree -a ${hexo_src_dir}/node_modules/.bin >> ${hexo_dest_dir}/node_modules.tree

    # 5. 备份主题
    cd ${hexo_src_dir}
    tar -czvf ${hexo_dest_dir}/themes.tar.gz themes
    cd -

    # 6. 上传
    git add -A
    git commit -m "$(date)"
    git push origin hexo:hexo
    ```

