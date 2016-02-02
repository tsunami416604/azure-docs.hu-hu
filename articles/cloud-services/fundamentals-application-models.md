<properties 
    pageTitle="計算 Azure 所提供的裝載選項" 
    description="了解 Azure 計算裝載選項以及其運作方式：虛擬機器、網站、雲端服務等。" 
    headerExpose="" 
    footerExpose="" 
    services="cloud-services,virtual-machines"
    authors="Thraka" 
    documentationCenter=""
    manager="timlt"/>

<tags 
    ms.service="multiple" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2015" 
    ms.author="adegeo;cephalin;kathydav"/>





# 計算 Azure 所提供的裝載選項

Azure 對於執行的應用程式提供不同的裝載模型。 每個模型都有不同的服務集，因此請根據您要執行的工作選擇服務集。 本文分別提供數種選項，並說明每種技術和提供使用時機的範例。

| 計算選項| 對象|
| ------------------ | --------   |
| [應用程式服務]| 可擴充 Web 應用程式、行動應用程式、API 應用程式，以及任何裝置的邏輯應用程式|
| [雲端服務]| 具備更高的作業系統控制能力的高度可用、可擴充的多層式架構雲端應用程式|
| [虛擬機器]| 具備完整作業系統控制能力的自訂 Windows 和 Linux VM|

[AZURE.INCLUDE [content](../../includes/app-service-choose-me-content.md)]

[AZURE.INCLUDE [content](../../includes/cloud-services-choose-me-content.md)]

[AZURE.INCLUDE [content](../../includes/virtual-machines-choose-me-content.md)]

## 其他選項

Azure 也針對更特殊的用途提供其他計算裝載模型，如下所示：

* [行動服務](/services/mobile-services/)  
  經最佳化，可針對在行動裝置上執行的應用程式提供雲端後端。
* [批次](/services/batch/)  
  經最佳化而可處理大量的類似工作，特別適用於將本身調撥在多部電腦上執行為平行工作的工作負載。
* [HDInsight (Hadoop)](/services/hdinsight/)  
  最佳化執行 [MapReduce](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options/#hadoop) Hadoop 叢集上的工作。

## 該用哪個模型？思考和選擇

這三個一般用途 Azure 計算裝載模型可讓您在雲端中建立可擴充而可靠的應用程式。 基於這個重要的相似性，您應該使用哪一個？

App Service 是大部分 Web 應用程式的最佳選擇。 部署和管理都已整合到平台，網站可以迅速調整規模以因應過高的流量負載，而內建的負載平衡和流量管理員提供高可用性。 您可以將現有網站移至應用程式服務，輕鬆地與 [線上移轉工具](https://www.migratetoazure.net/), 、 從 Web 應用程式組件庫中，使用開放原始碼應用程式，或建立新的網站使用的架構和您選擇的工具。  [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) 功能可讓您輕鬆地將背景工作處理加入您的應用程式，或甚至還能執行的計算工作負載，根本不是 web 應用程式。

如果您需要加強控制 Web 伺服器環境，例如想要從遠端登入伺服器或設定伺服器啟動工作，Azure 雲端服務通常是最佳選項。

如果現有應用程式需要進行大幅修改才能在 Azure 網站或 Azure 雲端服務中執行，您可以選擇 Azure 虛擬機器來簡化移轉至雲端的工作。 不過，相較於 Azure 網站和雲端服務，正確設定、保護和維護 VM 需要投入更多時間和 IT 專業知識。 如果您考慮採用 Azure 虛擬機器，請確定您已將修補、更新和管理 VM 環境所需的持續性維護工作都納入考量。

有時候，任何單一選項都不是正確的選項。 在這種情況下，可以併用多個選項。 例如，假設您要建置發揮雲端服務 Web 角色管理效益的應用程式，但是由於相容性或效能考量，您也需使用在虛擬機器中託管的標準 SQL Server。



## 後續步驟

* [比較](../choose-web-site-cloud-service-vm/) App Service、 雲端服務和虛擬機器
* 深入了解 [應用程式服務](../app-service-web-overview.md)
* 深入了解 [雲端服務](services/cloud-services/)
* 深入了解 [虛擬機器](https://msdn.microsoft.com/library/azure/jj156143.aspx)




[07_combinetechnologies]: ./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png 
[app service]: #tellmeas 
[virtual machines]: #tellmevm 
[cloud services]: #tellmecs 

