<properties
    pageTitle="Azure 服務端點"
    description="說明適用於 Eclipse 的 Azure 工具組中 Azure 服務端點的設定。"
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
    ms.date="11/19/2015" 
    ms.author="robmcm"/>




# Azure 服務端點

Azure 服務端點會判斷您的應用程式是否已部署至全域 Azure 平台並由該平台管理，Azure 由位於中國的 21Vianet 或私人 Azure 平台運作。 [**服務端點**] 對話方塊可讓您指定要使用的服務端點。 若要在 Eclipse 中開啟 [**服務端點**] 對話方塊，請依序按一下 [**視窗**] 和 [**喜好設定**]，展開 [**Azure**] 後，然後按一下 [**服務端點**]。 設定 [**作用中設定**] 欄位，可決定您目前工作區中的 Azure 專案將使用的 Azure 服務端點。

[**服務端點**] 對話方塊如下顯示。

![][ic719493]

## 設定服務端點

在 [**服務端點**] 對話方塊中，執行下列其中一個動作：

* 如果想使用全域 Azure 平台，請從 [**作用中設定**] 下拉式清單中，選取 [**windowsazure.com**]，並按一下 [**確定**]。
* 如果想使用由位於中國的 21Vianet 所運作的 Azure，請從 [**作用中設定**] 下拉式清單中，選取 [**windowsazure.cn (中國)**] 後，並按一下 [**確定**]。
* 如果想使用私人 Azure 平台：
    1. 按一下 [**編輯**]。
    2. 隨即會開啟一個對話方塊，告知您 [**服務端點**] 對話方塊將關閉，喜好設定檔案將開啟。 按一下 [確定]****。
    3. 
    4. 儲存並關閉 preferencesets.xml。
    5. 重新開啟 [**服務端點**] 對話方塊。
    6. 從 [**作用中設定**] 下拉式清單中，選取您所建立的作用中設定，然後按一下 [**確定**]。
    7. 

## 另請參閱














[azure java developer center]: http://go.microsoft.com/fwlink/?LinkID=699547 
[azure toolkit for eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529 
[creating a hello world application for azure in eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533 
[installing the azure toolkit for eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546 
[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png 

