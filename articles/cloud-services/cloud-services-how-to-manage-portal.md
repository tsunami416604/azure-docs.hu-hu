<properties 
    pageTitle="如何管理雲端服務 |Microsoft Azure" 
    description="了解如何在 Azure 入口網站中管理雲端服務。 這些範例使用 Azure 入口網站。" 
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
    ms.date="09/22/2015"
    ms.author="adegeo"/>


# 如何管理雲端服務

> [AZURE.SELECTOR]
- [Azure 傳統入口網站](cloud-services-how-to-manage.md)
- [Azure 入口網站](cloud-services-how-to-manage-portal.md)

在 **雲端服務** 區域的 Azure 入口網站，您可以更新服務角色或部署、 將預備的部署到生產環境的升級，資源連結至您的雲端服務，可讓您可以查看資源依存性並將資源一起調整並刪除雲端服務或部署。


## 作法：更新雲端服務角色或部署

如果您需要更新您的雲端服務的應用程式程式碼，使用 **更新** 雲端服務] 分頁上。 您可以更新單一角色或所有角色。 您將需要上傳新的服務套件和服務組態檔。

1. 在 [Azure 入口網站][], ，選取您想要更新的雲端服務。 這會開啟雲端服務執行個體刀鋒視窗。

2. 在分頁中，按一下 [ **更新** ] 按鈕。

    ![更新按鈕](./media/cloud-services-how-to-manage-portal/update-button.png)

3. 使用新的服務封裝檔 (.cspkg) 和服務組態檔 (.cscfg) 更新部署。

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **您可以選擇** 更新部署標籤和儲存體帳戶。 

5. 如果更新會造成角色數目或任何角色的大小，選取 **角色大小或角色數目變更時，允許更新** 核取方塊，讓更新能夠繼續。 

    >[AZURE.WARNING] 請注意，如果您變更 （亦即，角色執行個體所裝載的虛擬機器的大小） 的角色或角色數目的大小，必須重新製作映像，每個角色執行個體 （虛擬機器） 和本機資料將會遺失。

6. 如果任何服務角色只有一個角色執行個體，請選取 **更新，即使一或多個角色包含單一執行個體的核取方塊** 以便繼續進行升級。 

    要讓 Azure 保證服務在雲端服務更新期間有 99.95% 的可用性，每個角色都至少必須有兩個角色執行個體 (虛擬機器)。 如此才能讓一個虛擬機器在受到更新時，還有另一個虛擬機器可以處理用戶端要求。

8. 按一下 [ **確定** 開始更新服務。



## 作法：交換部署以使預備部署升格為生產部署

使用 **交換** 升級到生產環境的雲端服務預備環境部署。 當您決定部署新版的雲端服務時，可以在雲端服務預備環境中預備並測試這個新版本，同時讓您的客戶繼續在生產部署中使用目前版本。 當您準備好升級至生產環境的新版本時，您可以使用 **交換** 切換這兩個部署位址的 Url。 

您可以交換部署 **雲端服務** 頁面或儀表板。

1. 在 [Azure 入口網站][], ，選取您想要更新的雲端服務。 這會開啟雲端服務執行個體刀鋒視窗。

2. 在分頁中，按一下 [ **交換** ] 按鈕。

    ![Cloud Services Swap](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. 隨即開啟下列確認提示。

    ![Cloud Services Swap](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. 確認部署資訊之後，請按一下 **確定** 交換部署。

    交換部署的速度很快，因為唯一變更的是部署的虛擬 IP 位址 (VIP)。

    當您確定新的生產部署如預期執行時，即可刪除預備環境中的部署，以節省運算成本。

## 作法：將資源連結到雲端服務

Azure 入口網站不會像目前 Azure 傳統入口網站一樣將資源連結一起。 相反地，您必須在雲端服務所使用的相同資源群組中部署額外的資源。

## 作法：刪除部署和雲端服務

刪除雲端服務之前，您必須先刪除每個現有的部署。

為了節省運算成本，您可以在確認生產部署如預期運作之後刪除預備部署。 只要角色執行個體存在，您就需要為其支付運算成本，即使雲端服務未執行也一樣。

使用下列程序，刪除部署或雲端服務。 

1. 在 [Azure 入口網站][], ，選取您想要刪除的雲端服務。 這會開啟雲端服務執行個體刀鋒視窗。

2. 在分頁中，按一下 [ **刪除** ] 按鈕。

    ![Cloud Services Swap](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. 您可以刪除整個雲端服務，藉由檢查 **雲端服務和部署** 或選擇 **生產部署** 或 **預備部署**。

    ![Cloud Services Swap](./media/cloud-services-how-to-manage-portal/delete-blade.png) 

4. 按一下 [ **刪除** 底部的按鈕。

5. 若要刪除雲端服務，請按一下 [ **刪除雲端服務**。 然後，在確認提示中，按一下 **是**。

> [AZURE.NOTE]
> 如果對雲端服務設定了詳細資訊監視，則當您刪除該雲端服務時，Azure 並不會從您的儲存體帳戶中刪除監視資料。 您將需要手動刪除資料。 有關在哪裡可以找到這些度量表的資訊，請參閱 [這](cloud-services-how-to-monitor.md) 文件。

[Azure portal]: https://portal.azure.com

## 後續步驟

* [您的雲端服務的一般組態](cloud-services-how-to-configure-portal.md)。
* 了解如何 [部署雲端服務](cloud-services-how-to-create-deploy-portal.md)。
* 設定 [自訂網域名稱](cloud-services-custom-domain-name-portal.md)。
* 設定 [ssl 憑證](cloud-services-configure-ssl-certificate-portal.md)。

