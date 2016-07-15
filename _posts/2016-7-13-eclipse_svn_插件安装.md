**eclipse svn link方式安装插件**

1. eclipse安装目录新建MyPlugin目录，里面在新建svn目录， 将svn插件的features与 plugins文件夹复制到SVN里面
2. 在eclipse安装目录下找到dropins文件，在里面新建svn.link,内容是**/myPlugin/SVN，然后保存即可。

**myeclipse web项目导入eclispe**
右键项目，properties--project facets
选中Dynamic Web Module和java，选择版本(窗口拉宽才能看到)， Further Configuration 目录由WebContent改为当前的WebRoot。(tomcat
6选2.5， tomcat7 选3)。 点击OK。 删除新生成的WebContent目录




