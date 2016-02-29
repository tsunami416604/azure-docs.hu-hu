<properties
   pageTitle="針對 Marketplace 測試您的解決方案範本供應項目 | Microsoft Azure"
   description="了解如何針對 Azure Marketplace 測試您的解決方案範本供應項目。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/04/2015"
   ms.author="hascipio; v-divte" />

# 在預備環境中測試您的解決方案範本供應項目
預備環境代表將您的供應項目部署在私人的「沙箱」中，您可以在推送到生產環境之前在沙箱中測試與驗證其功能。 供應項目會出現在預備環境中，就如同客戶已部署該項目一樣。 您的供應項目必須經過認證才能推送至預備環境。

預備提供項目之後，您可以檢視及測試在優惠 [Azure 入口網站](https://portal.azure.com/)。

請遵循下列步驟來發送至預備環境提供和測試應用程式中 [Azure 入口網站](https://portal.azure.com/):

1.  移至 [發佈入口網站](https://publish.windowsazure.com) > **方案範本** ] 索引標籤 > 您的產品 > **發行** > **發送至預備環境**。
2.  提供您將用來預覽和測試供應項目的 Azure 訂用帳戶清單。
3.  使用上一個步驟中使用的訂用帳戶 ID，登入 Azure Preview 入口網站。
4.  在 Azure Preview 入口網站中，將以下提到的重點至少執行一回合測試：
  - 請確定該行銷內容可在 Azure Marketplace 中正確顯示。
  - 拓撲的端對端部署。
  - 執行效能測試和壓力測試。
  - 確定您的拓撲遵守最佳作法。

## 後續步驟
如果您滿意結果，則您可以繼續到最終的優惠發行階段， **步驟 4**:  [您提供的服務部署至 Marketplace](marketplace-publishing-push-to-production.md)。 否則，請在您的供應項目中進行變更並再次要求憑證。

> [AZURE.NOTE] 行銷內容變更時，就不需要憑證。

請參閱 [快速入門: 如何將方案發行至 Azure Marketplace](marketplace-publishing-getting-started.md) 如 「 發行者 」 的所有工作的指南。

