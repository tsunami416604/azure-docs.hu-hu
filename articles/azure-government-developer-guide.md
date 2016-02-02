<properties 
    pageTitle="Azure Government 開發人員指南" 
    description="這為 Azure Government 的開發應用程式提供功能和指引的比較" 
    services="" 
    documentationCenter="" 
    authors="Joharve2" 
    manager="Chrisnie" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="azure-government" 
    ms.date="10/29/2015" 
    ms.author="jharve"/>



# Microsoft Azure Government 開發人員指南

此開發人員指南將針對應用程式開發人員和系統管理員需要與這些 Azure 個別區域互動及處理的差異，提供相關提供詳細資料。




## 本主題內容

+ 
+ 
+ 
+ 
+ 


## 

Microsoft Azure Government 是 Microsoft Azure 的獨立執行個體，用來處理美國聯邦機構、州和地方政府及其解決方案提供者的的安全性和法規遵循需求。 Azure Government 提供與非美國政府部署隔離的實體及網路環境，並提供遴選過的美國工作人員。

Microsoft 提供一些工具來建立雲端應用程式並部署至 Microsoft 的全域 Microsoft Azure 服務 (全域服務) 及 Microsoft Azure Government 服務。

建立應用程式並部署至 Azure Government 服務時，由於這與全域服務相反，因此開發人員必須知道這兩種服務的主要差異。 特別是針對安裝及設定其程式設計環境、設定端點、撰寫應用程式，以及將其部署為 Azure Government 的服務等方面。



此內容的適用對象是要對 Microsoft Azure Government 進行部署的合作夥伴和開發人員。



## 

由於大部分目前可用的技術內容皆假設應用程式是針對「全域服務」開發，而不是針對 Microsoft Azure Government，因此確保開發人員知道為裝載於 Azure Government 而開發的應用程式有何主要差異，便相當重要。

- 首先，有服務和功能差異，這表示「全域服務」特定區域中的某些功能可能無法在 Azure Government 中提供。

- 其次，Azure Government 中所提供的功能與「全域服務」有組態差異。 因此，您應該檢閱您的範例程式碼、組態及步驟，以確保您是在「Azure Government 雲端服務」環境內建置和執行。


## 

Azure Government 目前在在「美國政府愛荷華州」(US GOV IOWA) 及「美國政府維吉尼亞州 (US GOV VIRGINIA)」區域有下列可用的服務：

- 虛擬機器
- 雲端服務
- 儲存體
- Active Directory
- 排程器
- 虛擬網路
- SQL Database
- Azure 檔案
- 媒體服務
- 流量管理員
- 服務匯流排
- StorSimple
- Redis 快取
- Azure 備份
- 自動化
- ExpressRoute
- 

有其他服務可用，並且會持續新增更多服務。

目前，「美國政府愛荷華州」和「美國政府維吉尼亞州」是支援 Azure Government 的資料中心。 如需目前的資料中心及可用的服務，請參閱上述區域頁面。

## 

將公用 Microsoft Azure 與 SQL Database 端點對應至 Azure Government 特定端點時，可使用下表做為指南。


 服務類型| Azure 公用| Azure Government
---|---|---
 管理入口網站| manage.windowsazure.com| manage.windowsazure.us
 一般| *.windows.net| *.usgovcloudapi.net
 核心| *.core.windows.net| *.core.usgovcloudapi.net
 計算| *.cloudapp.net| *.usgovcloudapp.net
 Blob 儲存體| *.blob.core.windows.net| *.blob.core.usgovcloudapi.net
 佇列儲存體| *.queue.core.windows.net| *.queue.core.usgovcloudapi.net
 資料表儲存體| *.table.core.windows.net| *.table.core.usgovcloudapi.net
 服務管理| management.core.windows.net| management.core.usgovcloudapi.net
 SQL Database| *.database.windows.net| *.database.usgovcloudapi.net
 | | 

* 

## 

如果您有興趣深入了解 Azure Government，請利用下面的連結。

- 

- 

- 

- 

- 









[1]: ./media/azure-government-developer-guide/publisherguide.png 
[link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md 
[link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md 
[link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md 

