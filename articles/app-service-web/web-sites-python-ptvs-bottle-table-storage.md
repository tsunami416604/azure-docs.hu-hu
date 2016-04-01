<properties 
    pageTitle="Azure 上使用 Python Tools 2.2 for Visual Studio 的 Bottle 和 Azure 資料表儲存體" 
    description="了解如何使用 Python Tools for Visual Studio 建立 Bottle 應用程式，藉此將資料儲存在 Azure 資料表儲存體中，並將 Web 應用程式部署到 Azure App Service Web Apps。" 
    services="app-service\web" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="11/18/2015"
    ms.author="huvalo"/>


# Azure 上使用 Python Tools 2.2 for Visual Studio 的 Bottle 和 Azure 資料表儲存體 

在本教學課程中，我們將使用 [Python Tools for Visual Studio] 來建立簡單的民調 web 應用程式使用其中一個 PTVS 範例範本。 本教學課程也會提供做為 [視訊](https://www.youtube.com/watch?v=GJXDGaEPy94)。

民調 Web 應用程式會為其儲存機制定義一個抽象概念，讓您輕鬆地切換不同類型的儲存機制 (記憶體內部、Azure 資料表儲存體、MongoDB)。

我們將學習如何建立 Azure 儲存體帳戶、 如何設定 web 應用程式使用 Azure 資料表儲存體，以及如何將 web 應用程式發佈 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)s。

請參閱 [Python Developer Center] 如需文件開發的 Azure 應用程式服務 Web 應用程式使用 PTVS 使用 Bottle、 Flask 和 Django web 架構，來使用 MongoDB、 Azure 資料表儲存體、 MySQL 和 SQL 資料庫的服務。 雖然本文著重於應用程式服務，開發時的步驟很類似 [Azure Cloud Services]。

## 必要條件

 - Visual Studio 2013 或 2015
 - [Python Tools 2.2 for Visual Studio]
 - [Python Tools 2.2 for Visual Studio 範例 VSIX]
 - [Azure SDK Tools for VS 2013] 或 [Azure SDK Tools for VS 2015]
 - [Python 2.7 32-bit] 或 [Python 3.4 32-bit]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；無需承諾。

## 建立專案

在這一節中，我們將使用範例範本建立 Visual Studio 專案。 我們將建立虛擬環境並安裝必要的套件。 然後將使用預設記憶體內部存放庫，在本機執行應用程式。

1.  在 Visual Studio 中，選取 **檔案**, ，**新的專案**。

1.  之下可取得 PTVS 範例 VSIX 專案範本中 **Python**, ，**範例**。 選取 **Polls Bottle Web Project** 按一下 [確定] 以建立專案。

    ![新專案對話](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleNewProject.png)

1.  系統會提示您安裝外部套件。 選取 **虛擬環境安裝**。

    ![外部套件對話方塊](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleExternalPackages.png)

1.  選取 **Python 2.7** 或 **Python 3.4** 做為基底解譯器。

    ![新增虛擬環境對話方塊](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAddVirtualEnv.png)

1.  按 `F5` 確認應用程式可運作。 根據預設，應用程式會使用記憶體內部儲存機制，而不需要進行任何設定。 當 Web 伺服器停止時，所有資料都會遺失。

1.  按一下 [ **Create Sample Polls**, ，然後按一下某項民調並投票。

    ![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleInMemoryBrowser.png)

## 建立 Azure 儲存體帳戶

若要使用儲存體作業，您需要 Azure 儲存體帳戶。 您可以依照下列步驟來建立儲存體帳戶。

1.  登入 [Azure 入口網站](https://portal.azure.com/)。

2. 按一下 [ **新增** 圖示上面保留的入口網站，然後按一下 [ **資料 + 儲存體** > **儲存體帳戶**。  按一下 [ **建立** ] 按鈕，然後為儲存體帳戶指定唯一名稱，並建立新 [資源群組](../resource-group-overview.md) 它。

    <!-- ![New Button](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzurePlusNew.png) -->

    建立儲存體帳戶後， **通知** 按鈕便會閃爍綠色 **成功** 儲存體帳戶的刀鋒視窗會開啟，顯示它屬於您所建立的新資源群組。

    <!-- ![Quick Create](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureStorageCreate.png) -->

5. 按一下 [ **設定** 儲存體帳戶的刀鋒視窗的一部分。 記下帳戶名稱和金鑰。

    我們在下一節中將需要此資訊來設定您的專案。

## 設定專案

在這一節中，我們會將應用程式設定為使用我們剛才建立的儲存體帳戶。 然後會在本機執行此應用程式。

1.  在 Visual Studio 中，以滑鼠右鍵按一下您的專案節點，在 [方案總管]，然後選取 **屬性**。 按一下 [ **偵錯** ] 索引標籤。

    ![專案偵錯設定](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageProjectDebugSettings.png)

1.  設定環境變數中的應用程式所需的值 **偵錯伺服器命令**, ，**環境**。

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    這會將環境變數時您 **開始偵錯**。 如果您想要時設定的變數您 **啟動但不偵錯**, ，設定相同的值在 **執行伺服器命令** 以及。

    此外，您也可以使用 Windows 控制台定義環境變數。 如果想要避免將認證儲存在原始碼 / 專案檔案中，這是比較好的選項。 請注意，您必須重新啟動 Visual Studio，新的環境值才可用於應用程式。

1.  實作 Azure 資料表儲存體儲存機制的程式碼位於 **models/azuretablestorage.py**。 請參閱 [documentation] 如需有關如何從 Python 使用表格服務。

1.  使用 `F5` 執行應用程式。 用來建立的民調 **Create Sample Polls** 以及投票所提交的資料將會在 Azure 資料表儲存體中序列化。

    > [AZURE.NOTE] Python 2.7 虛擬環境，可能會在 Visual Studio 中導致例外狀況中斷。  按下 `F5` 以繼續載入 Web 專案。 

1.  瀏覽至 **有關** 頁面，確認應用程式使用 **Azure 資料表儲存體** 儲存機制。

    ![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageAbout.png)

## 探索 Azure 資料表儲存體

使用 Visual Studio 中的 [伺服器總管] 即可輕易檢視和編輯儲存體資料表。 在這一節中，我們將使用 [伺服器總管] 來檢視民調應用程式資料表的內容。

> [AZURE.NOTE] 這需要 Microsoft Azure Tools 安裝，也就是可做為屬於 [Azure SDK for .NET]。

1.  開啟 **伺服器總管**。 展開 **Azure**, ，**儲存體**, ，儲存體帳戶，然後 **資料表**。

    <!-- ![Server Explorer](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png) -->

1.  按兩下 **民調** 或 **選擇** 資料表以檢視資料表的內容中的文件視窗，以及新增/移除/編輯實體。

    <!-- ![Table Query Results](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png) -->

## 將 Web 應用程式發佈至 Azure App Service

Azure .NET SDK 提供簡單的方法將 Web 應用程式部署至 Azure App Service。

1.  在 **方案總管] 中**, ，以滑鼠右鍵按一下專案節點，然後選取 **發行**。

    <!-- ![Publish Web Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png) -->

1.  按一下 [ **Microsoft Azure Web Apps**。

1.  按一下 [ **新增** 建立新的 web 應用程式。

1.  填寫下列欄位，然後按一下 **建立**。
    -   **Web 應用程式名稱**
    -   **App Service 方案**
    -   **資源群組**
    -   **區域**
    -   保留 **資料庫伺服器** 設 **沒有資料庫**

    <!-- ![Create Web App on Microsoft Azure Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonCreateWebSite.png) -->

1.  接受所有其他預設值，然後按一下 [ **發行**。

1.  您的 Web 瀏覽器將會自動開啟到已發佈的 Web 應用程式。 如果您瀏覽到關於] 頁面上，您會看到它使用 **記憶體中** 儲存機制，不 **Azure 資料表儲存體** 儲存機制。

    這是因為因此它會使用預設值中指定的環境變數不會在 Azure App Service Web 應用程式執行個體上設定 **settings.py**。

## 設定 Web Apps 執行個體

在本節中，我們將設定 Web Apps 執行個體的環境變數。

1.  在 [Azure Portal], ，開啟 web 應用程式的刀鋒視窗中，依序按一下 **瀏覽** > **Web 應用程式** > 您的 web 應用程式名稱。

1.  在 web 應用程式的刀鋒視窗中，按一下 [ **所有設定**, ，然後按一下 [ **應用程式設定**。

    <!-- ![Top Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteTopMenu.png) -->

1.  向下捲動至 **應用程式設定** 區段，並設定的值 **REPOSITORY\_NAME**, ，**STORAGE\_NAME** 和 **STORAGE\_KEY** 中所述 **設定專案** 一節。

    <!-- ![App Settings](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png) -->

1. 按一下 [ **儲存**, ，然後 **重新啟動** 最後 **瀏覽**。

    <!-- ![Bottom Menu](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureBottomMenu.png) -->

1.  您應該會看到 web 應用程式運作不如預期，使用 **Azure 資料表儲存體** 儲存機制。

    恭喜！

    ![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureBrowser.png)

## 後續步驟

請遵循下列連結以深入了解 Python Tools for Visual Studio、Bottle 和 Azure 資料表儲存體。

- [Python Tools for Visual Studio 說明文件]
  - [Web 專案]
  - [雲端服務專案]
  - [在 Microsoft Azure 上進行遠端偵錯]
- [Bottle 說明文件]
- [Azure 儲存體]
- [Azure SDK for Python]
- [如何從 Python 使用資料表儲存體服務]

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱 ︰ [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Python Developer Center]: /develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs.md
[documentation]: ../storage-python-how-to-use-table-storage.md
[How to Use the Table Storage Service from Python]: ../storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Azure Portal]: https://portal.azure.com
[Azure SDK for .NET]: http://azure.microsoft.com/downloads/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=624025
[Python Tools 2.2 for Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkId=624025
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[Bottle Documentation]: http://bottlepy.org/docs/dev/index.html
[Remote Debugging on Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Web Projects]: http://go.microsoft.com/fwlink/?LinkId=624027
[Cloud Service Projects]: http://go.microsoft.com/fwlink/?LinkId=624028
[Azure Storage]: http://azure.microsoft.com/documentation/services/storage/
[Azure SDK for Python]: https://github.com/Azure/azure-sdk-for-python
 


