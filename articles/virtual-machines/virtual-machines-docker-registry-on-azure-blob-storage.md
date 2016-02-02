<properties 
  pageTitle="在 Azure 上部署您自己的私用 Docker Registry | Microsoft Azure"
  description="描述如何使用 Docker Registry 裝載 Azure Blob 儲存體服務上的容器映像。"
  services="virtual-machines"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="" 
  tags="azure-service-management,azure-resource-manager" />

<tags
  ms.service="virtual-machines"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="06/17/2015" 
  ms.author="ahmetb" />


# 在 Azure 上部署您自己的私用 Docker Registry

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



本文件描述何謂 Docker 私用登錄，並顯示您如何能夠部署 Docker Registry 2.0
若要使用 Azure Blob 儲存體的 Microsoft Azure 上的 Docker 私用登錄容器映像。

本文件假設：

1. 您知道如何使用 Docker，並具備要儲存的 Docker 映像。 (您沒有嗎？ [了解 Docker](https://www.docker.com))
2. 您具有已安裝 Docker 引擎的伺服器。 (您不知道嗎？ [快速在 Azure 上執行。](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## 什麼是私用 Docker Registry？

若要將應用程式編定域機組，
您建構 Docker 容器映像，並將其儲存位置，讓它可以是
您自己以及由其他人使用。

建立容器映像並將其傳送至雲端時很容易，
這也是可靠地儲存產生的映像。 基於這個理由，
Docker 提供集中式的服務呼叫 [Docker Hub ][docker-hub] 儲存
容器映像的雲端，並可讓您建立容器
隨時使用這些映像。

雖然 [Docker Hub ][docker-hub] 是付費的服務，儲存
私用應用程式容器映像，Docker 尊重開發人員的需求，
提供開放原始碼工具組，以儲存在您自己的私用 Docker 映像
在防火牆後方或內部部署而不需使用公用網際網路登錄。
因為 Azure Blob 儲存體可輕鬆受到保護，您可以快速地使用它來建立
並使用自行控制您的 Azure 中的私用 Docker 登錄。

## 為什麼應該在 Azure 上裝載 Docker Registry？

裝載在 Microsoft Azure 上的 Docker Registry 執行個體，並將儲存程式
Azure Blob 儲存體上的映像，您可以有數個優點:

**安全性:** 您的 Docker 映像不會離開 Azure 資料中心，因此它們會執行
  如果您使用 Docker Hub 如同不會跨越公用網際網路。

**效能:** 您的 Docker 影像會儲存在相同
資料中心或與您的應用程式的區域。 這表示會將映像
提取更快速且更可靠地相較於 Docker 中樞。

**可靠性:** 藉由使用 Microsoft Azure Blob 儲存體，您可使用
許多儲存體屬性，例如高可用性、 備援、 進階
儲存體 (Ssd) 等等。

## 將 Docker Registry 設定為使用 Azure Blob 儲存體

(建議您先閱讀 [Docker Registry 2.0 文件 ][registry-docs]
再繼續進行。)

您可以 [設定 ][registry-config] Docker Registry 兩個不同的方式。
您可以：

1. 使用 `config.yml` 檔案。 在此情況下，您將需要建立
  個別的 Docker 映像的上方 `登錄` 映像。
2. 覆寫預設組態檔，透過環境變數:
  取得完成工作，而不需要建立和維護個別的 Docker 映像。

為了簡單起見，本主題會遵循選項 2: 使用
環境變數中。

若要執行 Docker Registry 執行個體:
* 使用您的 Azure 儲存體帳戶儲存映像
* 在虛擬機器的連接埠 5000 上接聽
* 未設定驗證 (不建議，請參閱下列附註)

您必須執行下列 Docker 命令在您 bash
終端機 (取代 `< 儲存體帳戶 >` 和 `< 儲存體金鑰 >` 
使用您的認證):

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

一旦命令結束，您可以看到容器裝載
私用 Docker Registry 執行個體執行 `docker ps`
您的主機上的命令:

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT] 設定 Docker Registry 的安全性
本文件未涵蓋和可存取您的登錄
任何人未經驗證的預設，如果您開啟的連接埠
登錄虛擬機器端點上連接埠或負載平衡器，如果您
使用上述的部署命令。
>
> 請閱讀 [設定 Docker
若要了解如何保護登錄] [登錄設定] 文件
登錄執行個體和您的映像。

## 後續步驟

一旦您設定了登錄，就可以使用它的其他功能。 開始使用 docker [登錄文件]。


[docker-hub]: https://hub.docker.com/ 
[registry]: https://github.com/docker/distribution 
[registry-docs]: http://docs.docker.com/registry/ 
[registry-config]: http://docs.docker.com/registry/configuration/ 

