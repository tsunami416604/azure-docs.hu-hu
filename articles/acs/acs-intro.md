<properties
   pageTitle="Azure 容器服務簡介 | Microsoft Azure"
   description="Azure 容器服務 (ACS) 提供簡化建立、設定及管理虛擬機器叢集的方法，這些虛擬機器預先設定為執行容器化應用程式。"
   services="virtual-machines"
   documentationCenter=""
   authors="rgardler"
   manager="nepeters"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Containers, Micro-services, Mesos, Azure"/>
   
<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="home-page"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="rogardle"/>

# Azure 容器服務簡介

Azure 容器服務 (ACS) 提供方法來簡化建立，
設定及管理叢集的虛擬機器
預先設定為執行編應用程式。 使用最佳化
受歡迎的開放原始碼排程和協調流程工具] 的設定
ACS 可讓您使用現有技能，或是在大型繪圖和
成長社群專業能力來部署和管理的主體容器為基礎
Microsoft Azure 上的應用程式。

<br />
![ACS 提供一個方法來在 Azure 的多部主機上管理容器化應用程式。](./media/acs-intro/acs-cluster.png)
<br /><br />

ACS 會使用 Docker，以確保您的應用程式容器
完整移植。 它也支援您所選擇的馬拉松，Chronos 和
Apache Mesos 或 Docker Swarm，以確保這些應用程式可以是
調整為成千上萬甚至數千個容器。

Azure 容器服務可讓您充分利用
Azure 的同時仍維持應用程式的企業級功能
可攜性，包括協調流程層級。

在此服務在預覽時，我們會要求，想測試
服務 [自我提名](http://aka.ms/acspreview)。 提供預覽存取之後，我們會傳送一封電子郵件，內含進一步詳細資料，包括部署範本以及快速入門指示。 若要使用服務時，您需要 Azure 訂閱，如果您有一個尚未，為什麼不註冊的
[免費
試用](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AA4C1C935)嗎？

使用 Azure 容器服務
-----------------------------

我們與 Azure 容器服務的目標是要提供容器
裝載環境中，使用開放原始碼工具和技術，亦即
今天我們的客戶受歡迎。 為了這個目的，我們會公開標準
Docker 和您所選擇的 orchestrator 的 API 端點。 使用這些
端點，您可以利用能夠進行交談的任何軟體
這些端點。 例如，如果是 Docker Swarm 端點
您可以選擇使用 Docker Compose，而針對 Apache Mesos 貴用戶
選擇使用 DCOS CLI。

使用 Azure 容器服務建立 Docker 叢集
-------------------------------------------------------

一旦 [要求](http://aka.ms/acspreview) 而且已授與存取權
預覽中，您可以使用其中一個數字的 Azure 資源管理員
範本可讓您部署您的第一個叢集，透過 Azure
。 使用這些範本，您可以快速地建立服務和
啟動立即部署它的應用程式。 若要開始您
只被要決定您的叢集，以及您是否要的大小
使用 Docker Swarm 或 Apache Mesos 來管理您的應用程式。

您也可以 [使用命令列](/documentation/articles/resource-group-template-deploy/)
若要建立 Azure 容器服務使用相同的範本。 您一次
熟悉這些範本，您將能夠結構
撰寫您自己和完全自動化的 Azure 容器建立
服務叢集。

將提供完整的文件和支援預覽參與者
一旦服務開啟公開這裡發行。

部署應用程式
------------------------

在預覽期間，我們提供 Docker Swarm 或 Apache 的選擇
Mesos （與 DCOS 馬拉松和 DCOS Chronos 架構）
協調流程。 

### 使用 Apache Mesos

Apache Mesos 是開放原始碼專案放在 Apache Software
Foundation。 它會列出一些 [中的最大名稱
IT](http://mesos.apache.org/documentation/latest/powered-by-mesos/) 做為
使用者和參與者。

![針對顯示代理程式與主機的 Swarm 設定的 ACS。](media/acs-intro/acs-mesos.png)

Mesos 內含令人印象深刻的功能集。

-   可擴充至 10000 個節點

-   使用 ZooKeeper 進行主要和從屬容錯複寫

-   支援 Docker 容器

-   在工作與 Linux 容器之間的原生隔離

-   多資源排程 (記憶體、CPU、磁碟和連接埠)

-   用於開發全新平行應用程式的 Java、Python 和 C++ API

-   用於檢視叢集狀態的 Web UI

Mesos 有大量的支援
[架構](http://mesos.apache.org/documentation/latest/frameworks/)
可用來排程 Azure 容器上的工作負載
服務。 根據預設，ACS 包括 Marathon 和 Chronos 架構。

#### 使用 Marathon 和 Chronos

馬拉松是整個叢集 init 和控制系統中的服務
cgroups 或在 ACS，Docker 容器。 它是理想
合作夥伴 Chronos，Mesos 錯誤容錯工作排程器的
處理相依性和排程時間為基礎。

馬拉松和 Chronos 提供 Web UI，您可以用來部署程式
應用程式。 您將存取此 url 看起來像下
`http://DNS\_PREFIX.REGION.cloudapp.azure.com`
其中 DNS\_PREFIX 及區域兩者都被定義在部署階段。 的
課程中，您也可以提供您自己的 DNS 名稱。

您也可以使用 REST Api 的通訊馬拉松和
Chronos。 有好幾個用戶端程式庫適用於每個工具，
涵蓋各種不同的語言，當然，您可以使用 HTTP
以任何語言的通訊協定。 此外，提供許多受歡迎的 DevOps 工具
這些排程器的支援。 這提供最大的彈性程式
使用 ACS 叢集時，作業小組。

將提供完整的文件和支援預覽參與者
一旦服務開啟公開這裡發行。

### 使用 Docker Swarm

Docker Swarm 為 Docker 提供原生叢集。 因為 Docker Swarm
提供標準的 Docker API 已經與其通訊的任何工具
Docker 精靈可以使用 Swarm 無障礙地延伸到多部主機
Azure 容器服務。 

![設定來使用顯示 jumpbox、代理程式與主機之 Apache Mesos 設定的 ACS。](media/acs-intro/acs-swarm.png)

包括支援的工具來管理 Swarm 叢集上的容器，但並不會限制
要完成下列動作 ︰

-   Dokku

-   Docker CLI 與 Docker Compose

-   Krane

-   Jenkins

將提供完整的文件和支援預覽參與者
一旦服務開啟公開這裡發行。

獲得存取權限
--------------

在此服務在預覽時，我們要求那些想要測試
服務 [自我提名](http://aka.ms/acspreview)。 您首先需要 Azure
訂用帳戶，如果您有一個尚未為何不註冊的 [免費
試用](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AA4C1C935)嗎？

影片
------
AzureCon 通知：

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

開始使用 ACS：  

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]


