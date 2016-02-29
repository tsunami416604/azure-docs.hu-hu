<properties
    pageTitle="如何建立和部署雲端服務 | Microsoft Azure"
    description="了解如何在 Azure 中使用「快速建立」方法來建立和部署雲端服務。 這些範例使用 Azure 入口網站。"
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="adegeo"/>




# 如何建立和部署雲端服務

> [AZURE.SELECTOR]
- [Azure 傳統入口網站](cloud-services-how-to-create-deploy.md)
- [Azure 入口網站](cloud-services-how-to-create-deploy-portal.md)

Azure 的入口網站提供的兩種方式，讓您建立和部署定域機組服務: *快速建立* 和 *自訂建立*。

本文說明如何使用快速的建立方法來建立新的定域機組服務，然後使用 **上載** 上載及部署在 Azure 中的定域機組服務套件。 當您使用這個方法時，Azure 入口網站會在過程中提供便利的連結，讓您完成所有要求。 如果您準備在建立雲端服務時加以部署，可以同時使用 [自訂建立] 進行這兩項作業。

> [AZURE.NOTE] 如果您打算將輸出定域機組服務從 Visual Studio 小組服務 (VSTS)，使用快速的建立，，，然後會設定從 Azure 快速入門 」 或 「 儀表板的 VSTS 發行。 如需詳細資訊，請參閱 [連續的傳遞，使用 Visual Studio 的小組服務的 Azure 到][TFSTutorialForCloudService], ，或請參閱說明 **快速入門** 頁面。

## 概念
需要三個元件才能部署應用程式成為 Azure 中的雲端服務：

- **服務定義**  
  雲端服務定義檔 (.csdef) 定義服務模型，包括角色數目。

- **服務組態**  
  雲端服務組態檔 (.cscfg) 為雲端服務和個別角色提供組態設定，包括角色執行個體的數目。

- **服務套件**  
  服務套件 (.cspkg) 包含應用程式程式碼和組態和服務的定義檔。

您可以深入了解這些因應對策以及如何建立封裝 [這裡](cloud-services-model-and-package.md)。

## 準備您的應用程式
您部署雲端服務之前，必須先從應用程式程式碼和雲端服務組態檔 (.cscfg) 建立雲端服務封裝 (.cspkg)。 Azure SDK 提供準備這些必要部署檔案的工具。 您可以安裝從 SDK [Azure 下載](http://azure.microsoft.com/downloads/) 頁面上，在您想在其中開發應用程式程式碼的語言。

三個雲端服務功能需要特別組態，您才能匯出服務封裝：

- 如果您想要部署定域機組服務用於資料加密的安全通訊端層 (SSL) [將您的應用程式設定](cloud-services-configure-ssl-certificate-portal.md#modify) 的 SSL。

- 如果您想要設定遠端桌面] 連線至角色執行個體， [設定角色](cloud-services-role-enable-remote-desktop.md) 遠端桌面。 這只能在傳統入口網站中完成。

- 如果您要設定雲端服務的詳細資訊監視，請啟用雲端服務的 Azure 診斷。 *最小的監視* (監視層級的預設值) 使用角色執行個體 (虛擬機器) 主機作業系統從收集的效能計數器。 *設定監視的詳細資訊* 會收集效能資料中的角色執行個體，以便在應用程式處理期間發生的問題的更深入的分析為基礎的其他度量資訊。 若要了解如何啟用 Azure 診斷功能，請參閱 [啟用診斷在 Azure](cloud-services-dotnet-diagnostics.md)。

若要建立與部署的 web 的角色或背景工作角色的定域機組服務，您必須 [建立服務套件](cloud-services-model-and-package.md#servicepackagecspkg)。

## 開始之前

- 如果您尚未安裝 Azure SDK，按一下 [ **安裝 Azure SDK** 開啟 [Azure 下載處頁面](http://azure.microsoft.com/downloads/), ，然後下載 SDK，且您想在其中開發您的程式碼的語言。 (您稍後將有機會這麼做。)

- 如果任何角色執行個體需要憑證，請建立憑證。 雲端服務需要含有私密金鑰的 .pfx 檔。 [您可以上載憑證 Azure 到]() 當您建立並部署定域機組服務。

- 如果您計劃將雲端服務部署至同質群組，請建立同質群組。 您可以使用同質群組，將雲端服務和其他 Azure 服務部署到區域中的同一個位置。 您可以建立 [親和性] 群組中 **網路** 的 Azure 的傳統區域入口網站，在 **親和性群組** 頁面。


## 步驟 3：建立雲端服務和上傳部署套件

1. 登入 [Azure 入口網站][]。
2. 按一下 [ **新增 > 計算**, ，然後向下捲動並按一下 [ **定域機組服務**。

    ![發佈您的雲端服務](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. 在顯示的 [資訊] 頁面底部，按一下 [ **建立**。 
4. 在新 **定域機組服務** 刀，輸入的值 **DNS 名稱**。
5. 建立新的 **資源群組** 或選取現有的平台。
6. 選取 [ **位置**。
7. 選取 [ **套件**, ，及 **上載封裝** 刀，必要的欄位的填滿。  

     如果您的角色的任何包含單一執行個體時，確保 **部署即使一或多個角色包含單一執行個體** 已選取。

8. 請確定 **啟動部署** 已選取。
9. 按一下 [ **確定**。

    ![發佈您的雲端服務](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## 上傳憑證

如果您的部署套件是 [設定為使用憑證](cloud-services-configure-ssl-certificate-portal.md#modify), ，您可以立即上載憑證。

1. 選取 [ **憑證**, ，及 **將憑證加入** 刀，選取 [SSL 憑證.pfx 檔案]，然後提供 **密碼** 憑證，
2. 按一下 [ **附加憑證**, ，然後按一下 [ **[確定]** 上 **將憑證加入** 刀。
3. 按一下 [ **建立** 上 **定域機組服務** 刀。 部署已經到了 **準備** 您可以繼續到下一個步驟的狀態。

    ![發佈您的雲端服務](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)


## 確認部署是否成功完成

1. 按一下雲端服務執行個體。

    狀態應該會顯示 「 服務 」 **執行**。

2. 下 **Essentials**, ，按一下 [ **網站 URL** 若要在 web 瀏覽器中開啟您定域機組的服務。

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## 後續步驟

* [一般設定定域機組服務的](cloud-services-how-to-configure-portal.md)。
* 設定 [自訂網域名稱](cloud-services-custom-domain-name-portal.md)。
* [管理您的定域機組服務](cloud-services-how-to-manage-portal.md)。
* 設定 [ssl 憑證](cloud-services-configure-ssl-certificate-portal.md)。
