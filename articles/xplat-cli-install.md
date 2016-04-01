<properties
    pageTitle="安裝 Azure 命令列介面 | Microsoft Azure"
    description="安裝適用於 Mac、Linux 和 Windows 的 Azure CLI 以開始使用 Azure 服務"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="dlepow"
    services=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/18/2015"
    ms.author="danlep"/>

# 安裝 Azure CLI

本文說明如何安裝 Azure 命令列介面 (Azure CLI)。 Azure CLI 提供一組開放原始碼的命令介面式命令，用於建立和管理 Microsoft Azure 中的資源。

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-both-include.md)]


Azure CLI 以 JavaScript 撰寫，而且需要 [Node.js](https://nodejs.org)。 使用實作 [Azure SDK for Node](https://github.com/azure/azure-sdk-for-node), ，並在 Apache 2.0 授權下發佈。 專案儲存機制位於 [https://github.com/azure/azure-xplat-cli](https://github.com/azure/azure-xplat-cli)。

> [AZURE.NOTE] 如果您已經安裝 Azure CLI，請與您的 Azure 資源連接。 如需詳細資訊，請參閱 [如何連接到您的 Azure 訂閱](xplat-cli-connect.md#configure)。

<a id="install"></a>
## 如何安裝 Azure CLI

有幾種方式可以安裝 Azure CLI。

1. 使用安裝程式
2. 安裝 Node.js 和 npm，然後使用 **npm 安裝** 命令
3. 執行 Azure CLI 做為 Docker 容器

一旦安裝 Azure CLI 之後，您將能夠使用 **azure** 命令從命令列介面 （Bash、 終端機、 命令提示字元，等等） 來存取 Azure CLI 命令。

## 使用安裝程式

我們提供以下安裝程式套件：

* [Windows 安裝程式][windows-installer]

* [OS X 安裝程式](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Linux 安裝程式][linux-installer]


## 安裝和使用 Node.js 和 npm

如果已經在系統上安裝 Node.js，請使用下列命令來安裝 Azure CLI：

    npm install azure-cli -g

> [AZURE.NOTE] 在 Linux 散發套件，您可能需要使用 `sudo` 才能順利執行 __npm__ 命令。

### 使用的 Linux 散發套件上安裝 node.js 和 npm [dpkg](http://en.wikipedia.org/wiki/Dpkg) 封裝管理
最常見的這類散發套件是使用 [進階封裝工具 (apt)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool) 或根據其他工具 `.deb` 封裝格式。 部分範例包括 Ubuntu 和 Debian。

大部分較新的這些發佈版本需要安裝 **nodejs 舊版** 以取得正確設定 **npm** 工具來安裝 Azure CLI。 下列程式碼顯示安裝的命令 **npm** Ubuntu 14.04 上適當設定。

    sudo apt-get install nodejs-legacy
    sudo apt-get install npm
    sudo npm install -g azure-cli

有些較舊的發佈版本 (例如 Ubuntu 12.04) 需要安裝 node.js 目前的二進位檔發佈。 下列程式碼會示範如何進行這項安裝和使用 **curl**。

>[AZURE.NOTE] 此處的命令取自找到的 Joyent 安裝指示 [這裡](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager)。 不過，當使用 **sudo** 做為管道目標時，您應該一律檢查的指令碼，您要安裝並驗證它們的功能如您預期. **sudo**。 功能愈強大，責任愈多。

    sudo apt-get install curl
    curl -sL https://deb.nodesource.com/setup | sudo bash -
    sudo apt-get install -y nodejs
    sudo npm install -g azure-cli

### 使用的 Linux 散發套件上安裝 node.js 和 npm [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager) 封裝管理

在以 RPM 為基礎的發佈版本上安裝 node.js 時必須啟用 EPEL 儲存機制。 下列程式碼會顯示在 CentOS 7 上進行安裝的最佳作法。 (請注意，在下列第一行中，'-' (連字號) 很重要！)

    su -
    yum update [enter]
    yum upgrade –y [enter]
    yum install epel-release [enter]
    yum install nodejs [enter]
    yum install npm [enter]
    npm install -g azure-cli  [enter]

### 在 Windows 和 Mac OS X 上安裝 node.js 和 npm

您可以在 Windows 與 OS X 使用從安裝程式安裝 node.js 和 npm [Nodejs.org](https://nodejs.org/download/)。 您可能需要重新啟動電腦才能完成安裝。 請檢查是否已正確安裝節點和 npm，方法是開啟命令視窗並輸入

    npm -v

如果顯示安裝的 npm 版本，您就可以繼續執行並安裝 Azure CLI 以及

    npm install -g azure-cli

在安裝結束時，您會看到類似下列的項目：

    azure-cli@0.8.0 ..\node_modules\azure-cli
  	|-- easy-table@0.0.1
  	|-- eyes@0.1.8
  	|-- xmlbuilder@0.4.2
  	|-- colors@0.6.1
  	|-- node-uuid@1.2.0
  	|-- async@0.2.7
  	|-- underscore@1.4.4
  	|-- tunnel@0.0.2
  	|-- omelette@0.1.0
  	|-- github@0.1.6
  	|-- commander@1.0.4 (keypress@0.1.0)
  	|-- xml2js@0.1.14 (sax@0.5.4)
  	|-- streamline@0.4.5
  	|-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
  	|-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
  	|-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

>[AZURE.NOTE] 對於 Linux 系統，您也可以安裝 Azure CLI 建置從 [來源](http://go.microsoft.com/fwlink/?linkid=253472)。 如需從原始碼進行建置的詳細資訊，請參閱封存中內含的 INSTALL 檔案。

## 使用 Docker 容器

在 Docker主機中，執行：
```
    docker run -it microsoft/azure-cli
```

## 執行 Azure CLI 命令

一旦安裝 Azure CLI 之後，您將能夠使用 **azure** 命令從命令列使用者介面 （Bash、 終端機、 命令提示字元，等等） 來存取 Azure CLI 命令。 例如，若要在 Windows 執行 help 命令，請啟動命令視窗並輸入下列命令：

```
    c:\> azure help
```

您現在已經準備就緒！ 接下來，您可以 [從 Azure CLI 連接到您的 Azure 訂閱](xplat-cli-connect.md) 並開始使用 **azure** 命令。


<a id="additional-resources"></a>
## 其他資源

* [搭配使用 Azure CLI 和資源管理命令][cliarm]

* [搭配使用 Azure CLI 和服務管理 (傳統) 命令][cliasm]

* 若要深入了解 Azure CLI 下載來源程式碼、 報告問題，或是對專案發表意見，請造訪 [Azure CLI 的 GitHub 儲存機制](https://github.com/azure/azure-xplat-cli)。

* 如果您遇到問題，使用 Azure CLI 或 Azure，請造訪 [Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home)。



[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md



