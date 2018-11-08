项目里面如果先添加文件，再加入 `.gitignore` ，`.gitignore` 可能会不生效。

于是要删除缓存的文件：
执行 git rm -r --cached  "bin/"     ，删除文件的命令. 
执行 git commit -m" 删除bin文件"    ，提交，并加注释
执行 git push origin master   　　　，提交到远程服务器

