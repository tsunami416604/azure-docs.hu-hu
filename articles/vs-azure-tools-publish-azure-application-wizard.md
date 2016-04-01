<properties 
   pageTitle="發佈 Azure 應用程式精靈 | Microsoft Azure"
   description="發佈 Azure 應用程式精靈"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2015"
   ms.author="tarcher" />

# 發佈 Azure 應用程式精靈

## 概觀

開發 Visual Studio 中的 web 應用程式之後，您可以使用發行該應用程式更輕鬆地以 Azure 雲端服務 **發行 Azure 應用程式** 精靈。 第一節說明您在使用精靈前必須完成的步驟，其他節說明精靈的功能。

>[AZURE.NOTE] 本主題是關於部署至雲端服務，而不要網站。 部署至網站的相關資訊，請參閱 [如何部署 Azure 網站](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false)。

## 必要條件

發佈 web 應用程式到 Azure 之前，您必須有 Microsoft 帳戶和 Azure 訂用帳戶，而且您必須將 web 應用程式與 Azure 雲端服務產生關聯。 如果您已經完成這些工作，您就可以跳至下一節。

1. 取得 Microsoft 帳戶和 Azure 訂用帳戶。 您可以嘗試免費一個月的免費 Azure 訂閱 [這裡](https://azure.microsoft.com/pricing/free-trial/)

1. 在 Azure 上建立雲端服務和儲存體帳戶。 您可以從 [伺服器總管，在 Visual Studio 中，或使用 [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)。 如需如何設定 Azure 環境的詳細資訊，請參閱 [設定服務需要設定發行雲端服務從 Visual Studio](vs-azure-tools-publish-azure-application-wizard)。

1. 啟用 Azure 的 web 應用程式。 若要啟用 web 應用程式以將其從 Visual Studio 發佈至 Azure，您必須將它與 Visual Studio 中的 Azure 雲端服務專案產生關聯。 建立相關聯的雲端服務專案中，開啟 web 應用程式專案的捷徑功能表，然後選擇 [轉換]， **轉換成 Azure 雲端服務專案**。

1. 雲端服務專案加入至方案之後，再次開啟相同的捷徑功能表，然後選擇 [ **發行**。 如需如何啟用 Azure 的應用程式的詳細資訊，請參閱 [How to ︰ 移轉並發行 Web 應用程式至 Azure 雲端服務從 Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx)。

>[AZURE.NOTE] 請務必使用系統管理員認證 （系統管理員身分執行） 啟動 Visual Studio。

1. 當您準備好要發行應用程式時，開啟 Azure 雲端服務專案的捷徑功能表，然後選擇 [ **發行**。 下列步驟會顯示發佈 Azure 應用程式精靈。

## 選擇您的訂用帳戶。

### 選擇訂用帳戶

1. 第一次使用精靈之前，您必須登入。 選擇 **登入** 連結。 出現提示時登入 Azure 入口網站，並提供您的 Azure 使用者名稱和密碼。 

    ![這是其中一個發佈精靈畫面](./media/vs-azure-tools-publish-azure-application-wizard/IC799159.png)

    在訂用帳戶清單中填入與您的帳戶相關聯的訂用帳戶。 您也可能會從您先前匯入的任何訂用帳戶檔案看到訂用帳戶。

1. 在 **選擇您的訂閱** 清單中，選擇要用於此部署的訂閱。

   如果您選擇 **< 管理...> >**, 、 **管理訂閱** 對話方塊隨即出現，而您可以選擇您想要使用的訂用帳戶和使用者帳戶。  **帳戶** 索引標籤會顯示所有的帳戶，而 **訂閱** ] 索引標籤會顯示所有與帳戶相關聯的訂閱。 您也可以選擇要從其中使用 Azure 資源的區域，並且從 Azure 入口網站建立或匯入訂用帳戶的憑證。 從訂閱檔案匯入任何訂用帳戶，如果相關聯的憑證會出現在 [ **憑證** ] 索引標籤。 當您完成時，選擇 [ **關閉** ] 按鈕。

    ![管理訂閱](./media/vs-azure-tools-publish-azure-application-wizard/IC799160.png)

    >[AZURE.NOTE] 訂閱檔案可以包含多個訂閱。

1. 選擇 **下一步** ] 按鈕以繼續。 

    如果您的訂用帳戶中沒有任何雲端服務，您需要在 Azure 中建立雲端服務以裝載您的專案。  **建立雲端服務和儲存體帳戶** ] 對話方塊隨即出現。

    指定雲端服務的新名稱。 名稱在 Azure 中必須是唯一的。 然後指定接近您或大部分用戶端之資料中心的區域或同質群組。 此名稱也用於 Azure 為雲端服務所建立的新儲存體帳戶。

1. 修改任何設定，您想針對此部署，然後選擇 [發行 **發行** （下一節會提供更多詳細的各種設定）] 按鈕。 若要檢閱發行前的設定，請選擇 [ **下一步** ] 按鈕。

    >[AZURE.NOTE] 如果您選擇發佈在此步驟中，您可以監視此部署 Visual Studio 中的狀態。

您可以使用，以修改部署的一般及進階設定 **發行 Azure 應用程式** 精靈。 例如，您可以選擇一項設定，在發佈應用程式前將其部署至測試環境。 下圖顯示 **一般設定** Azure 的部署] 索引標籤。

![一般設定](./media/vs-azure-tools-publish-azure-application-wizard/IC749013.png)

## 設定您的發佈設定

### 設定發佈設定

1. 在 **雲端服務** 清單中，執行下列一組步驟 ︰

   1. 在下拉式清單方塊中，選擇現有的雲端服務。 服務的資料中心位置會隨即出現。 您應該記下這個位置並確定您的儲存體帳戶位置位於相同的資料中心。

    1. 選擇 **新建** 來建立 Azure 託管的雲端服務。 在 **Create Cloud Service** 對話方塊中，提供服務的名稱，然後指定 [區域或同質群組指定您想要裝載此雲端服務的資料中心的位置。 名稱在 Azure 中必須是唯一的。

1. 在 **環境** 清單中選擇 [ **生產** 或 **預備**。 如果您想要將應用程式部署至測試環境，請選擇預備環境。 您可以在稍後將應用程式移至生產環境。

1. 在 **組建組態** 清單中選擇 [ **偵錯** 或 **版本**。

1. 在 **服務組態** 清單中選擇 [ **定域機組** 或 **本機**。

    選取 **啟用所有角色的遠端桌面** 核取方塊，如果您想要能夠從遠端連線至服務。 此選項主要用於疑難排解。 當您選取此核取方塊， **遠端桌面設定** ] 對話方塊隨即出現。 選擇設定連結以變更組態。

    選取 **所有 web 角色啟用 Web 都 Deploy** 核取方塊以啟用 web 服務的部署。 您必須啟用遠端桌面以使用這項功能。 如需詳細資訊，請參閱 [[發行雲端服務使用 Azure Tools](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx)。 如需 Web Deploy 的詳細資訊，請參閱 [[發行雲端服務使用 Azure Tools](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx)。

1. 選擇 **進階設定** ] 索引標籤。 在 **部署標籤** ] 欄位中，接受預設名稱，或輸入您選擇的名稱。 若要將日期附加至部署標籤，請選取核取方塊。

    ![發佈精靈的第三個畫面](./media/vs-azure-tools-publish-azure-application-wizard/IC749014.png)

1. 在 **儲存體帳戶** 清單中，選擇要用於此部署的儲存體帳戶。 比較雲端服務和儲存體帳戶的資料中心位置。 在理想的情況下，這些位置應該相同。

    >[AZURE.NOTE] Azure 儲存體帳戶會儲存應用程式部署封裝。 部署應用程式之後，封裝會從儲存體帳戶中移除。

1. 選取 **部署更新** 只核取方塊，如果您想要部署更新的元件。 這種部署類型比完整部署更快速。 選擇 **設定** 連結，以開啟 **部署更新設定** 對話方塊中，如下圖所示。 

    ![部署設定](./media/vs-azure-tools-publish-azure-application-wizard/IC617060.png)

    您可以選擇更新部署的兩個選項之一，累加或同時。 累加部署一次會更新一個已部署的執行個體，讓您的應用程式保持連線並可供使用者使用。 同時部署會一次更新所有已部署的執行個體。 同時更新的速度比累加更新還快，但如果您選擇此選項，您的應用程式可能無法在更新過程中使用。

    如果無法更新部署，您應該選取此核取方塊，如果您想要在更新部署失敗時自動更新完整部署，請執行完整部署。 完整部署會重設雲端服務的虛擬 IP (VIP) 位址。 如需詳細資訊，請參閱 [How to ︰ 雲端服務保留固定的虛擬 IP 位址](https://msdn.microsoft.com/library/azure/jj614593.aspx)。


1. 若要偵錯您的服務，請選取 **啟用 IntelliTrace** 核取方塊，或如果您要部署 **偵錯** 組態，且想要偵錯雲端服務在 Azure 中，選取 **啟用遠端偵錯工具的所有角色** 核取方塊以部署的遠端偵錯服務。

2. 若要從頭到尾分析應用程式，請選取 **啟用程式碼剖析** 核取方塊，然後按一下 **設定** 連結，以顯示程式碼剖析選項。 


    >[AZURE.NOTE] 您必須使用 Visual Studio Ultimate 以啟用 IntelliTrace 或階層互動分析 (TIP)，而且您不能兩者同時啟用。

    For more information, see [Debugging a Published Cloud Service with IntelliTrace and Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx) and [Testing the Performance of a Cloud Service](https://msdn.microsoft.com/library/azure/hh369930.aspx).

1. 選擇 **下一步** 若要檢視應用程式的 [摘要] 頁面。

## 發佈您的應用程式

1. 您可以選擇從您所選擇的設定建立發佈設定檔。 例如，您可能會建立一個設定檔用於測試環境，並建立另一個用於生產。 若要儲存此設定檔，請選擇 [ **儲存** 圖示。 此精靈會建立設定檔並將它儲存在 Visual Studio 專案。 若要修改的設定檔名稱，開啟 **目標設定檔** 清單，然後再選擇 **< 管理...> >**。

    ![發佈精靈的摘要畫面](./media/vs-azure-tools-publish-azure-application-wizard/IC749015.png)

    >[AZURE.NOTE] 發行設定檔會出現在 Visual Studio 中的 [方案總管] 中，設定檔設定寫入至副檔名為.azurePubxml 的檔案。 設定會儲存為 XML 標記的屬性。

1. 選擇 **發行** 要發行應用程式。 您可以監視中的程序狀態 **輸出** Visual Studio 視窗中的。

## 另請參閱

[設定從 Visual Studio 發佈雲端服務所需的服務](https://msdn.microsoft.com/library/azure/ff683668.aspx)

[作法：從 Visual Studio 將 Web 應用程式移轉並發佈至 Azure 雲端服務](https://msdn.microsoft.com/library/azure/hh420322.aspx)

[使用 Azure Tools 發佈雲端服務](https://msdn.microsoft.com/library/azure/ff683672.aspx)

[使用 IntelliTrace 和 Visual Studio 偵錯發佈的雲端服務](https://msdn.microsoft.com/library/azure/ff683671.aspx)

[測試雲端服務的效能](https://msdn.microsoft.com/library/azure/hh369930.aspx)



