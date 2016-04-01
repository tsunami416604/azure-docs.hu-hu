<properties 
    pageTitle="預備雲端服務部署 (Node.js) | Microsoft Azure" 
    description="了解如何將 Azure 應用程式部署至預備環境，然後使用虛擬 IP (VIP) 交換來部署至生產環境。" 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="11/20/2015" 
    ms.author="robmcm"/>



# 在 Azure 中預備應用程式

封裝的應用程式可以先部署到 Azure 的預備環境
進行測試後，再移到生產
環境，讓應用程式透過網際網路進行存取。 Auch die Eigenschaften
預備環境和生產環境完全一致，但有一點例外，
您只能使用 Azure 產生的隱藏 URL 存取預備應用程式
。 驗證
應用程式可正常運作之後，就能將它部署到生產
環境，方法是執行虛擬 IP (VIP) 交換作業。

> [AZURE.NOTE] 在本文中的步驟僅適用於裝載成 Azure 雲端服務的節點應用程式。

## 步驟 1：預備應用程式

此工作涵蓋如何使用預備應用程式 **Microsoft
Azure PowerShell**。

1.  當發行服務，您只需傳遞 **-位置** 參數
     **Publish-azureserviceproject** 指令程式。

    ```powershell
    Publish-AzureServiceProject -Slot staging
    ```

2.  登入 [Azure classic portal] ，然後選取 **雲端服務**。 服務建立雲端之後， **預備** 資料行狀態已更新為 **執行**, ，按一下服務名稱。

    ![portal displaying a running service][cloud-service]

3.  選取 **儀表板**, ，然後選取 **預備**。

    ![cloud service dashboard][cloud-service-dashboard]

4. 請注意在值 **網站 URL** 右邊的項目。 DNS 名稱是 Azure 產生的隱藏內部識別碼。

    ![site url][cloud-service-staging-url]

現在，您可以利用預備網站 URL，驗證應用程式在預備環境中可正常運作。

## 步驟 2：交換 VIP 將應用程式升級至生產環境

驗證預備環境中的應用程式升級版本後，
您可以快速地使其可用於生產環境，方法是
交換預備和生產環境的虛擬 IP (VIP)
。

> [AZURE.NOTE] 這個步驟假設您有已部署
也已預備應用程式的
重新接收。

1.  登入 [Azure classic portal], ，按一下 [ **雲端服務** ，然後選取服務名稱。

2.  從 **儀表板**, ，請選取 **預備**, ，然後按一下 [ **交換** 頁面的底部。 這樣會開啟 [VIP 交換] 對話方塊。

    ![vip swap dialog][vip-swap-dialog]

3.  檢閱資訊，然後按一下 **確定**。 兩個部署開始更新，預備部署會切換至生產環境，而生產部署會切換至預備環境。

您已順利預備部署，並透過交換 VIP 使用預備環境中的部署升級生產
部署。

## 其他資源

- [如何在 Azure 中交換 VIP 將服務升級部署至生產環境]

[Azure classic portal]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[How to Deploy a Service Upgrade to Production by Swapping VIPs in Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production


