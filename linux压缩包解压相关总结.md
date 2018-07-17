#.gz文件包

​         gzip   -5   /root/vim.cc    把vim.cc文件压缩成.gz格式的文件包

​                   -#  指明压缩比；1-9，默认为6，数字越大，压缩比越大

​                   -r  递归至目录中对每个文件进行压缩

​         gzip  -c  /root/vim.cc  输出至标准输出

​         gzip  -c  /root/vim.cc  >  /root/vim.cc.gz   输出并保存至vim.cc.gz文件

​         gzip  -d  /root/vim.cc.gz  解压缩vim.cc.gz文件。压缩文件自动删除

​         gunzip   /root/vim.cc.gz   解压缩vim.cc.gz文件。压缩文件自动删除

​         zcat  /root/vim.cc.gz   查看vim.cc.gz文件内容

#.bz2文件包

​         bzip2   -5  /root/vim.cc   把vim.cc文件压缩成.bz2格式的文件包

​                   -#  指明压缩比；1-9，默认为6，数字越大，压缩比越大

​                   -k  压缩后保留原文件

​         bzip2  -d  /root/vim.cc.bz2   解压缩vim.cc.bz2文件。压缩文件自动删除

​         bunzip2  /root/vim.cc.bz2   解压缩vim.cc.bz2文件。压缩文件自动删除

​         bzcat   /root/vim.cc.bz2   查看vim.cc.bz2文件内容

#.zip文件包

​         zip  vim.cc.zip  /root/vim.cc /root/vim.txt  可以多个文件压缩归档成单个文件

​         unzip  vim.cc.zip    解压缩文件包

#.tar文件包

​         tar  -c  -f  /root/vim.tar  /root/vim.cc  /root/vim.txt  归档文件不会压缩

​         tar  -x  -f  /root/vim.tar  -C  /root   展开归档文件到/root目录下面

​         tar  -x  -f  /root/vim.tar   展开文档到当前目录

​         tar  -t  -f  /root/vim.tar   查看归档文件

​         多个选项可以合并， -f由于要带参数，所以应该放在最右侧 –cf, -xf, -tf

#.tar.gz文件包

​         tar  -zcf  /root/vim.tar.gz  /root/vim.cc  归档并压缩

​         tar  -zxf  /root/vim.tar.gz   -C  /root   展开归档到/root目录

​         tar  -zxf  /root/vim.tar.gz     展开归档文件到当前目录

#.tar.bz2文件包

​         tar  -jcf  /root/vim.tar.bz2  /root/vim.cc  归档并压缩

​         tar  -jxf  /root/vim.tar.bz2  -C  /root    展开归档到/root目录

tar  -jxf  /root/vim.tar.bz2   展开归档文件到当前目录

#.tar.xz文件包

​         tar  -Jcf  /root/vim.tar.xz  /root/vim.cc  归档并压缩

​         tar  -Jxf  /root/vim.tar.xz  -C  /root   展开归档到/root目录

​         tar  -Jxf  /root/vim.tar.xz    展开归档文件到当前目录

展开归档可以直接使用tar -xf，而无须为其指定对应的压缩工具选项