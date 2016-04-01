<properties
   pageTitle="若要建立方案範本的市場指南 |Microsoft Azure"
   description="如何建立、認證和部署一個多 VM 映像解決方案範本供他人在 Azure Marketplace 上購買的詳細指示。"
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
      ms.date="10/28/2015"
      ms.author="hascipio; v-divte" />

# 建立 Azure Marketplace 解決方案範本的指南
完成步驟 1 之後 [帳戶建立與註冊][link-acct-creation], ，我們會引導您在 Azure 相容方案範本建立 [技術建立的解決方案範本的必要條件](marketplace-publishing-solution-template-creation-prerequisites.md)。 現在我們將逐步引導您建立方案範本的多個 Vm 上的步驟 [發佈入口網站][link-pubportal] Azure marketplace。

## 在發佈入口網站中建立解決方案範本供應項目
移至  [https://publish.windowsazure.com](http://publish.windowsazure.com)。 若要在第一次登入時 [發佈入口網站](https://publish.windowsazure.com/), ，使用相同帳戶必須具有已註冊您的公司賣方設定檔。 稍後您可以在發佈入口網站中將您公司的任何員工新增為共同管理員。

### 1.選取 [解決方案範本]

  ![繪圖][img-pubportal-menu-sol-templ]

### 2.建立新的解決方案範本

  ![繪圖][img-pubportal-sol-templ-new]

### 3.開始使用拓樸
解決方案範本是所有其拓撲的「父項」。 您可以在一個供應項目/解決方案範本中定義多個拓撲。 當供應項目進入預備環境時，它的所有拓撲也會一起進入。 請遵循下列步驟來定義您的供應項目：     
- 建立拓撲：「拓樸識別項」通常是解決方案範本的拓撲名稱。 拓撲識別項用於 URL，如下所示：

  Azure Marketplace:
http://azure.microsoft.com/marketplace/partners/ {PublisherNamespace} / {OfferIdentifier} {TopologyIdentifier}

  Azure 預覽入口網站 ︰
https://ms.portal.azure.com/#gallery/ {PublisherNamespace}。{} OfferIdentifier{} TopologyIdentifier

- 加入新的版本。

### 4.讓您的拓撲版本取得認證
上傳一個 zip 檔案，其包含佈建該特定拓撲版本所需的所有檔案。 此 zip 檔案必須包含下列項目：
- *mainTemplate.json* 和 *createUiDefinition.json* 在其根目錄的檔案。
- 任何連結的範本和所有必要的指令碼。

按一下 [上傳 zip 檔案之後, **要求憑證**。 Microsoft 認證團隊會檢閱檔案並認證拓撲。

您也可以使用下列步驟，不需實際為客戶部署就可驗證建立體驗：

1. 儲存 *createUiDefinition.json* 和產生的絕對 URL。 此 URL 必須可公開存取。
2. 使用此工具在編碼 URL [http://www.url-encode-decode.com/](http://www.url-encode-decode.com/)。
3. 粗體文字的位置取代 (編碼 URL) 的 *createUiDefinition.json* 需要驗證。

  > https://portal.azure.com/?clientOptimizations=false#blade/Microsoft_Azure_Compute/CreateMultiVmWizardBlade/internal_bladeCallId/anything/internal_bladeCallerParams/ **{"initialData": {}，"providerConfig": {"createUiDefinition":"http://yoururltocreateuidefinition.jsonURLencoded"}}**

4. 複製 URL 並在任何瀏覽器中貼上 URL，檢視 createUiDefinition.json 檔案的使用者體驗。

  > [AZURE.TIP] 而您的開發人員建立方案範本拓撲及取得這些認證，商務貴公司的行銷及/或法律部門可以處理行銷和合法的內容。

## 後續步驟
現在，您建立自己的解決方案範本，並送出具有所需的憑證檔案的 zip 檔案，您可以可以繼續並依照 [市場行銷內容指南](marketplace-publishing-push-to-staging.md) 之前準備在預備環境中測試您的產品。 或者，若要查看完整的服務商場發表文章，請參閱 [快速入門 ︰ 如何將方案發行至 Azure Marketplace](marketplace-publishing-getting-started.md)。

您也可能對以下相關文章有興趣：

- VM 映像 ︰ [關於虛擬機器映像在 Azure 中](https://msdn.microsoft.com/library/azure/dn790290.aspx)

- VM 延伸模組 ︰ [VM 代理程式和 VM 延伸模組概觀](https://msdn.microsoft.com/library/azure/dn832621.aspx) 和 [Azure VM 延伸模組與功能](https://msdn.microsoft.com/library/azure/dn606311.aspx)

- Azure 資源管理員 ︰ [撰寫 Azure ARM 範本](../resource-group-authoring-templates/) 和 [簡單 ARM 範本範例](https://github.com/rjmax/ArmExamples)

- 儲存體帳戶會節流處理 ︰ [如何監視儲存體帳戶節流](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) 和 [高階儲存體](../storage/storage-premium-storage-preview-portal/#scalability-and-performance-targets-when-using-premium-storage)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-microsoft-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com


