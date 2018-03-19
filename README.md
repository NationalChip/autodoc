# 文档在线化向导

# 前言

本文档是一个利用gitbook editor+github+jenkins自动将本地MD文档生成在线的html和pdf文档的指引，请先理解以下概念：

* 书籍，是gitbook中新建的一系列MD文档的有机组合，物理上一本书所有的MD文档都保存在一个顶级目录下；
* 仓库，是github进行进行版本控制的一系列相关文本文件集合，如源代码文件，MD文件；
* 任务，是jenkins进行构建操作的一个抽象模型，一个任务包含源代码的仓库地址\(SVN、GIT等\)、构建操作的触发条件（定时、事件、手动）、构建的操作（清理环境、编译源代码、发布至生产环境等，在本文档中，构建操作是调用gitbook build/gitbook pdf 命令编译MD文件为html和pdf文件，并将后者发布至nginx服务器）等属性。

在本指引中，一个文档对应一本gitbook中的书籍，对应一个github中的仓库，对应一个jenkins构建任务，也就是说，要达到本地文档修改后自动同步更新至web服务器需要新建一本gitbook，一个github repo，一个jenkins job，数据流程为:

gitboo editor编辑本地书籍 --&gt; push更改至github repo --&gt; github利用webhook通知jenkins进行构建 --&gt; jenkins拉取最新的MD文件，编译成html和pdf --&gt; 发布最新的html和pdf至nginx服务器。

# 准备

* github账号：

  * 强烈建议通过凌总将自己的github账号与[公司Github](https://github.com/NationalChip)关联，并将仓库建立到公司目录下

* gitbook editor编辑器：

  * 安装参考[官方向导](https://www.gitbook.com/editor)

# 步骤

### 1.在公司的github账号下建立新仓库：

![](/assets/Selection_021.png)

仓库建立完成后不要向仓库添加任何内容，导航到仓库的Settings下的webhooks页面，点击“Add webhook"，webhook配置固定如下：

* Payload url: [http://139.196.170.32:8080/github-webhook/](http://139.196.170.32:8080/github-webhook/)
* Content type:application/json

最后点击Add webhook完成添加，如果成功会显示绿色的√。![](/assets/Selection_022.png)![](/assets/Selection_023.png)![](/assets/Selection_024.png)

### 2.新建jenkins构建任务

浏览器打开jenkins[管理后台](http://139.196.170.32:8080/)，输入用户名密码admin/admin（请不要随意改动密码）登录：

![](/assets/Selection_025.png)

在左侧的菜单栏点击新建任务

![](/assets/Selection_026.png)

**一定要通过复制ota创建**![](/assets/Selection_027.png)修改任务描述\(可选\)和Repo URL完成任务新建：![](/assets/Selection_029.png)

### 3.新建gitbook书籍

使用gitbook editor新建书籍，并选取合适的名字，如本书"AutoDoc Guide"

![](/assets/Selection_019.png)

进入书籍并编写内容，然后依次点击gitbook右上角的”Save"和“Publish"按钮![](/assets/Selection_030.png)第一次publish时会让你填写github仓库地址，请务必填写https协议的地址\(gitbook editor暂不支持git协议\)，首次publish会让你填写github的用户名和密码

![](/assets/Selection_031.png)

![](/assets/Selection_032.png)

点击”Sync"，成功后便可以转到jenkins的任务后台会发现，自动触发了图书发布操作：

![](/assets/Selection_033.png)进入任务的控制台可以查看构建的详细过程：

![](/assets/Selection_035.png)然后就可以[在线](http://139.196.170.32/docs/autodoc/)查看文档了，也可以下载[PDF](http://139.196.170.32/docs/autodoc/pdf)版本。

# _注意：_

* _首次同步至github触发的构建操作很可能失败，此时可以点击倒数第二张图中的“立即构建”命令手动触发构建操作，也可以随时登入jenkins后台手动触发构建操作；_
* _请尽量将本地的更改先多次Save到本地再向github push，以减少jenkens服务器的构建压力；_
* 构建任务自动生成了pdf格式的文档，地址为 http://139.196.170.32/docs/{jenkins\_\_job\_\_name}/pdf，如本文档的pdf版本为http://139.196.170.32/docs/autodoc/pdf。



