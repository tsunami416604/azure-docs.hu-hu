<properties 
    pageTitle="BizTalk 服務的管理與開發工作清單 | Microsoft Azure BizTalk 服務" 
    description="" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="msftman" 
    manager="dwrede" 
    editor="cgronlun"/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/02/2015" 
    ms.author="deonhe"/>


# BizTalk 服務的管理與開發工作清單

## 開始使用

使用「Microsoft Azure BizTalk 服務」時，應考量數項內部部署與雲端架構元件。 開始操作前，請考量下列處理流程：

| 步驟| 負責人| 工作| 相關連結|
|----|----|----|----|

1.|Administrator|建立使用 Microsoft 帳戶或組織的帳戶 | Microsoft Azure 訂用帳戶[Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)|
| 2。 |Administrator|建立或佈建 BizTalk 服務。 |[建立 BizTalk 服務: 使用 Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=302280)|
| 3。 |Administrator|註冊您或貴公司的 BizTalk 服務 deployment|[登錄及更新 BizTalk 服務部署在 BizTalk 服務入口網站](https://msdn.microsoft.com/library/azure/hh689837.aspx)|
| 4。 |Administrator|適用於應用程式使用 BizTalk 配接器服務來連線到內部特定業務 (LOB) 系統，或使用佇列或主題目的地。 建立 Azure 服務匯流排命名空間。 開發人員提供此命名空間、 服務匯流排簽發者名稱和服務匯流排簽發者金鑰值。 |[How to: 建立或修改服務匯流排服務命名空間]() 和 [取得簽發者名稱和簽發者金鑰值]()|
| 5。 |Developer|安裝 SDK 和 Visual Studio 中建立 BizTalk 服務專案。 |[安裝 Azure BizTalk 服務 SDK]() 和 [Azure 上建立豐富傳訊端點]()|
| 6。 |Developer|將您的 BizTalk 服務專案部署至裝載於 Azure BizTalk 服務。 |[部署和重新整理 BizTalk 服務專案](https://msdn.microsoft.com/library/azure/hh689881.aspx)|
|7。 |Administrator|如果您使用 EDI 會套用。 您可在「Microsoft Azure BizTalk 服務入口網站」上新增「合作夥伴」和建立「合約」。 當您建立協議時，您可以新增橋接器和/或協議設定開發人員所建立的轉換。 |[設定 EDI、 AS2 和 EDIFACT 在 BizTalk 服務入口網站](https://msdn.microsoft.com/library/azure/hh689853.aspx)|
|8。 |Administrator|使用 Azure 傳統入口網站，來監視 BizTalk 服務，包括效能度量的健全狀況。 |[BizTalk 服務: 儀表板、 監視器和調整索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281)|
| 9。 |Administrator|使用 Microsoft Azure BizTalk 服務入口網站、 管理橋接器檔案被處理時，BizTalk 服務和追蹤訊息所使用的成品。 |[使用 BizTalk 服務入口網站](https://msdn.microsoft.com/library/azure/dn874043.aspx)|
|10。 |Administrator|建立備份計劃來備份 BizTalk 服務。 |[業務持續性和 BizTalk 服務的嚴重損壞修復](https://msdn.microsoft.com/library/azure/dn509557.aspx) |
## 後續步驟

[教學課程和範例](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[在 Visual Studio 中建立專案](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[安裝 Azure BizTalk 服務 SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## 概念

[在 Visual Studio 中建立專案](https://msdn.microsoft.com/library/azure/hh689811.aspx)
[EDI、 AS2 和 EDIFACT 傳訊 (企業對企業)](https://msdn.microsoft.com/library/azure/hh689898.aspx)
## 其他資源

[新增來源、 目的地和橋接器傳訊端點](https://msdn.microsoft.com/library/azure/hh689877.aspx)
[了解並建立訊息對應和轉換](https://msdn.microsoft.com/library/azure/hh689905.aspx)
[使用 BizTalk 配接器服務 (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)
[Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=303664)





