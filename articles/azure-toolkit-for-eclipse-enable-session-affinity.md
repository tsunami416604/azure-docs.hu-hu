<properties
    pageTitle="使用 Azure Toolkit for Eclipse 啟用工作階段同質"
    description="了解如何使用 Azure Toolkit for Eclipse 來啟用工作階段同質。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# 啟用工作階段同質 #

在 Azure Toolkit for Eclipse 中，您可以為角色啟用 HTTP 工作階段同質 (或「黏性工作階段」)。 下圖顯示 **負載平衡** 用來啟用工作階段親和性功能的 [內容] 對話方塊 ︰

![][ic719492]

## 為角色啟用工作階段同質 ##

1. 在 Eclipse 的專案總管] 中的角色上按一下滑鼠右鍵，按一下 **Azure**, ，然後按一下 [ **負載平衡**。
1. 在 **WorkerRole1 負載平衡內容** 對話方塊 ︰
    1. 檢查 **這個角色啟用 HTTP 工作階段相似性 （自黏工作階段）。**
    1. 如 **輸入端點，以使用**, ，比方說，選取要使用，請輸入的端點 **http （公用 ︰ 80、 私用 ︰ 8080）**。 您的應用程式必須使用這個端點作為其 HTTP 端點。 您可以為角色啟用多個端點，但只能選取其中一個端點來支援黏性工作階段。
    1. 重建您的應用程式

啟用後，如果您有多個角色執行個體，來自特定用戶端的 HTTP 要求會繼續由相同的角色執行個體來處理。

Eclipse Toolkit 藉由將稱為應用程式要求路由 (ARR) 的特殊 IIS 模組安裝到每個角色執行個體，來完成這項作業。 ARR 會將 HTTP 要求重新路由傳送至適當的角色執行個體。 此工具組會自動重新設定選取的端點，因此傳入 HTTP 流量會先路由傳送至 ARR 軟體。 此工具組也會建立可設定您的 Java 伺服器接聽的新內部端點。 這是 ARR 將 HTTP 流量的端點重新路由傳送至適當角色執行個體時所用的端點。 如此一來，多個執行個體部署中的每個角色執行個體可作為其他所有執行個體的反向 Proxy，以啟用黏性工作階段。

## 工作階段同質的相關注意事項 ##

* 工作階段同質不會在計算模擬器中執行。 您可以在不干擾建置流程或計算模擬器執行的情況下，將這些設定套用至計算模擬器，但功能本身不會在計算模擬器中執行。
* 啟用工作階段同質會導致 Azure 中的部署所佔用的磁碟空間量增加，因為當您在 Azure 雲端中啟動服務時，會下載額外的軟體並安裝到您的角色執行個體。
* 初始化每個角色的時間會比較長。
* 系統會加入一個內部端點，如上所述作為路由器來重新路由傳送流量。

如需如何啟用工作階段親和性時，維護工作階段資料的範例，請參閱 [如何維護工作階段親和性的工作階段資料][]。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式][]

[安裝 Azure Toolkit for Eclipse][] 

[如何使用工作階段同質來維護工作階段資料][]

如需有關如何使用 java 的 Azure 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[How to Maintain Session Data with Session Affinity]: http://go.microsoft.com/fwlink/?LinkID=699539
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png


