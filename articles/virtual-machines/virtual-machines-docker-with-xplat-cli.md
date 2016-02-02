<properties
    pageTitle="在 Azure 上使用 Linux 的 Docker VM 擴充程式"
    description="說明 Docker 和 Azure 虛擬機器延伸模組，並示範如何以程式設計方式使用 Azure CLI，從命令列在 Azure 上建立 Docker 主機的虛擬機器。"
    services="virtual-machines"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="virtual-machines"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="09/22/2015"
    ms.author="rasquill"/>


# 透過 Azure 命令列介面 (Azure CL) 使用 Docker VM 延伸模組

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。



本主題說明如何透過 Azure CLI 中的服務管理 (asm) 模式，在任何平台上建立包含 Docker VM 延伸模組的 VM。 [Docker](https://www.docker.com/) 是其中一個最常用的虛擬化方式使用 [Linux 容器](http://en.wikipedia.org/wiki/LXC) 而不是一種獨立資料和執行計算共用資源上部署虛擬機器。 您可以使用 Docker VM 擴充 [Azure Linux 代理程式](virtual-machines-linux-agent-user-guide.md) 來建立 Docker VM 來託管任何數量的應用程式在 Azure 上的容器。 若要查看容器及其優點的高層級討論，請參閱 [Docker 高層級白板](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard)。

+ [如何搭配使用 Docker VM 延伸模組與 Azure]
+ [適用於 Linux 和 Windows 的虛擬機器延伸模組]
+ [容器及容器管理資源的 [Azure]
+ [下一步]

## 如何搭配使用 Docker VM 擴充程式與 Azure

若要使用 Azure 的 Docker VM 延伸模組，您必須安裝的版本 [Azure 命令列介面](https://github.com/Azure/azure-sdk-tools-xplat) (Azure CLI) 以上 0.8.6 (撰寫本文的目前版本為 0.8.10)。 您可以在 Mac、Linux 及 Windows 上安裝 Azure CLI。



在 Azure 上使用 Docker 的完整程序相當簡單：

+ 在您要控管 Azure 的電腦上安裝 Azure CLI 和其相依性 (若在 Windows 上，就是指如同虛擬機器一樣執行的 Linux 散發套件)
+ 使用 Azure CLI Docker 命令在 Azure 中建立 VM Docker 主機
+ 使用本機 Docker 命令在 Azure 中的 Docker VM 內管理 Docker 容器。


### 安裝 Azure 命令列介面 (Azure CLI)

若要安裝和設定 Azure CLI，請參閱 [如何安裝 Azure 命令列介面](../xplat-cli-install.md)。 若要確認安裝，請輸入 `azure` 在命令提示字元之後很快就能看到 Azure CLI ASCII 作品，其中會列出可供您使用的基本命令。 如果安裝正確執行，您應該可以輸入 `azure 說明 vm` 和看到列出的命令中有一個 「 docker 」。
> [AZURE.NOTE] Docker 具備適用於 Windows，安裝程式 [Boot2Docker](https://docs.docker.com/installation/windows/), ，您也可以使用自動建立的 docker 用戶端可讓您搭配 Azure Vm 做為 docker 主機。

### 將 Azure CLI 連接至您的 Azure 帳戶

在使用 Azure CLI 前，必須讓您的 Azure 帳戶認證與您平台上的 Azure CLI 產生關聯。 區段 [如何連接到您的 Azure 訂閱](../xplat-cli-connect.md) 說明如何下載及匯入您 **.publishsettings** 檔案或 Azure CLI 與組織識別碼建立關聯。
> [AZURE.NOTE] 因為使用一個或其他多個驗證方法的行為會有些許差異，因此請確實閱讀上述文件以了解不同的功能。

### 安裝 Docker 及使用適合 Azure 的 Docker VM 擴充程式

請依照下列 [Docker 安裝指示](https://docs.docker.com/installation/#installation) 本機電腦上安裝 Docker。

若要搭配使用 Docker 與 Azure 虛擬機器，VM 所使用的 Linux 映像必須 [Azure Linux VM 代理程式](virtual-machines-linux-agent-user-guide.md) 安裝。 目前來說，只有兩種映像類型提供此安裝：

+ Azure 映像庫中的 Ubuntu 映像，或

+ 使用已安裝並設定的 Azure Linux VM 代理程式所建立的自訂 Linux 映像。 請參閱 [Azure Linux VM 代理程式](virtual-machines-linux-agent-user-guide.md) 如需有關如何建置自訂 Linux VM 的 Azure VM 代理程式。

### 使用 Azure 映像庫

若要從 Bash 或終端機工作階段中，使用下列 Azure CLI 命令尋找 VM 映像庫中最新的 Ubuntu 映像，請輸入

`azure vm 映像清單 | grep Ubuntu-14_04`

選取其中一個映像名稱，例如 `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-en-us-30GB`, ，並使用下列命令來建立新的 VM 使用該映像。

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-en-us-30GB" <username> <password>
```

其中：

+ *<vm-cloudservice name>* 是將成為 Azure 中 Docker 容器主機電腦的 VM 名稱。

+  *&lt;username&gt;* 是VM 預設根目錄使用者的使用者名稱。

+ *&lt;password&gt;* 是 *username* 帳戶的密碼，需符合 Azure 的複雜性標準。

> [AZURE.NOTE] 目前，密碼必須至少 8 個字元，包含一個小寫字母和一個大寫字母、 數字和特殊字元，例如下列字元: `! @# $%^ & + =`。不，上一個句子中的句號並不是一個特殊字元。

如果命令執行成功，根據您使用的精準引數和選項，您應該可以看到如下所示的畫面：

![](./media/virtual-machines-docker-with-xplat-cli/dockercreateresults.png)
> [AZURE.NOTE] 建立虛擬機器可能需要幾分鐘的時間，但已佈建它之後 (狀態值是 `ReadyRole`)，Docker 精靈 (Docker 服務) 啟動，而您可以 Docker 容器主機連線。

若要測試您已在 Azure 中建立的 Docker VM，請輸入

`docker--tls-H tcp: / / < vm--您的使用名稱 >。 cloudapp.net:2376 資訊`

其中 *< vm--您的使用名稱 >* 是您在呼叫中使用的虛擬機器名稱 `建立 azure vm 的 docker`。 您應該可看到如下所示的內容，這代表您的 Docker 主機 VM 已在 Azure 中啟用和執行，並且正等待您的命令。

現在您可以嘗試連接您的 docker 用戶端使用來取得資訊 (在某些 Docker 用戶端設定，例如，在 Mac 上，您可能需要使用 `sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

要確定一切運作正常，您可以檢查 Docker 延伸模組的 VM：

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### Docker 主機 VM 驗證

除了建立 Docker VM， `建立 azure vm 的 docker` 命令也會自動建立必要的憑證，才能允許您的 Docker 用戶端電腦使用 HTTPS 與 Azure 容器主機連線，憑證會儲存在用戶端和主機機器，視需要。 在後續的嘗試上，現有憑證會重複使用並與新的主機共用。

根據預設，憑證會放在 `~/.docker`, ，而 Docker 將設定為連接埠上執行 **2376年**。 如果您想要使用不同的連接埠或目錄，則可以使用下列其中一種 `建立 azure vm 的 docker` 命令列選項來設定您的 Docker 容器主機 VM，使用不同的連接埠或不同的憑證來連接用戶端:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

在主機上的 Docker 精靈設定為接聽，並驗證指定的連接埠使用產生的憑證上的用戶端連線 `建立 azure vm 的 docker` 命令。 用戶端機器必須使用這些認證來取得 Docker 主機的存取權。
> [AZURE.NOTE] 在沒有這些憑證下運作的網路主機，將很容易受到任何可連線到此機器的使用者攻擊。 在您修改預設設定之前，請確保您已了解存在您電腦和應用程式中的風險。

## 後續步驟

您已準備好移至 [Docker 使用者指南]，並使用 Docker VM。 若要建立新的入口網站啟用 Docker 的 VM，請參閱 [如何使用 Docker VM 擴充程式與入口網站]。





[subheading 1]: #subheading-1 
[subheading 2]: #subheading-2 
[subheading 3]: #subheading-3 
[next steps]: #next-steps 
[how to use the docker vm extension with azure]: #How-to-use-the-Docker-VM-Extension-with-Azure 
[virtual machine extensions for linux and windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows 
[container and container management resources for azure]: #Container-and-Container-Management-Resources-for-Azure 
[5]: ./media/markdown-template-for-new-articles/octocats.png 
[6]: ./media/markdown-template-for-new-articles/pretty49.png 
[7]: ./media/markdown-template-for-new-articles/channel-9.png 
[link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md 
[link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md 
[link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md 
[how to use the docker vm extension with the portal]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/ 
[docker user guide]: https://docs.docker.com/userguide/ 

