<properties 
   pageTitle="開始使用資料湖存放區 | Azure" 
   description="使用入口網站來建立資料湖存放區帳戶，並在資料湖存放區中執行基本作業" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="11/13/2015"
   ms.author="nitinme"/>

# 使用 Azure 入口網站開始使用 Azure 資料湖分析

> [AZURE.SELECTOR]
- [使用入口網站](data-lake-store-get-started-portal.md)
- [使用 PowerShell](data-lake-store-get-started-powershell.md)
- [使用 .NET SDK](data-lake-store-get-started-net-sdk.md)
- [使用 Azure CLI](data-lake-store-get-started-cli.md)
- [使用 Node.js](data-lake-store-manage-use-nodejs.md)

了解如何使用 Azure 入口網站建立 Azure 資料湖存放區帳戶並執行基本作業，例如建立資料夾、上傳和下載資料檔案、刪除您的帳戶等等。如需詳細資料湖存放區的詳細資訊，請參閱 [概觀的 Azure 資料湖市集](data-lake-store-overview.md)。

## 必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="signup"></a>啟用您的 Azure 訂用帳戶以使用資料湖存放區公開預覽版

您必須先要求啟用您的 Azure 訂用帳戶以使用資料湖存放區公開預覽版。 依照下列步驟執行。

1. 登入新 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [ **新增**, ，按一下 [ **資料 + 儲存體**, ，然後按一下 [ **Azure 資料湖市集**。
3. 在 **新資料湖商店** 刀鋒視窗中，按一下 [ **註冊以預覽**。 閱讀資訊，然後按 **確定**。 您的訂用帳戶已啟用公開預覽版之後，您會收到一封電子郵件。

    ![註冊以取得公開預覽版](./media/data-lake-store-get-started-portal/preview-signup.png "Create a new Azure Data Lake account")

## 建立 Azure 資料湖存放區帳戶

1. 登入新 [Azure 入口網站](https://portal.azure.com)。

2. 按一下 [ **新增**, ，按一下 [ **資料 + 儲存體**, ，然後按一下 [ **Azure 資料湖市集**。 閱讀資訊 **Azure 資料湖存放區** 刀鋒視窗中，然後再按一下 **建立** 在刀鋒視窗的左下角。

3. 在 **新資料湖商店** 刀鋒視窗中，提供值，如螢幕擷取畫面下方所示 ︰

    ![建立新的 Azure 資料湖存放區帳戶](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Create a new Azure Data Lake account")

    - **訂閱**。 選取您想要建立新資料湖存放區帳戶所在的訂用帳戶。
    - **資源群組**。 選取現有的資源群組，或按一下 **建立資源群組** 建立一個。 資源群組是保留應用程式相關資源的容器。 如需詳細資訊，請參閱 [Azure 中的資源群組](resource-group-overview.md#resource-groups)。
    - **位置**︰ 選取您要建立資料湖存放區帳戶的位置。

4. 選取 **釘選到開始面板** 如果您希望可以從儀表板存取資料湖存放區帳戶。

5. 按一下 [ **建立**。 如果您選擇將帳戶釘選到開始面板，您會回到開始面板，並且可以看到資料湖存放區帳戶佈建的進度。 一旦佈建資料湖存放區帳戶，帳戶刀鋒視窗就會出現。

6. 展開 **Essentials** 下拉式清單，請參閱資料湖儲存帳號的相關資訊，例如資源群組，它是位置等的一部分。按一下 [ **快速入門** 資料湖儲存區的相關圖示，請參閱 < 其他資源連結。

    ![您的 Azure 資料湖存放區帳戶](./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Your Azure Data Lake account")

## <a name="createfolder"></a>在 Azure 資料湖存放區帳戶中建立資料夾

您可以在您的 Azure 資料湖存放區帳戶下建立資料夾，用於管理與存放資料。

1. 開啟您剛建立的資料湖存放區帳戶。 從左窗格中，按一下 **瀏覽**, ，按一下 [ **資料湖存放區**, ，然後從 [資料湖存放區] 分頁中，按一下您要在其下建立資料夾的帳戶名稱。 如果您將帳戶釘選到開始面板，請按一下該帳戶磚。

2. 在您資料湖存放區帳戶] 分頁中，按一下 [ **資料總管**。

    ![在資料湖存放區帳戶中建立資料夾](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Create folders in Data Lake Store account")

3. 在您資料湖存放區帳戶] 分頁中，按一下 [ **新資料夾**, ，輸入新資料夾的名稱，然後按一下 **確定**。
    
    ![在資料湖存放區帳戶中建立資料夾](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Create folders in Data Lake Store account")
    
    新建立的資料夾會列在 **資料總管** 刀鋒視窗。 您可以建立任何層級的巢狀資料夾。

    ![在資料湖帳戶中建立資料夾](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Create folders in Data Lake account")


## <a name="uploaddata"></a>將資料上傳至 Azure 資料湖存放區帳戶

您可以直接在根層級將資料上傳至 Azure 資料湖存放區帳戶，或上傳至您在帳戶內建立的資料夾。 在下列螢幕擷取畫面，請依照下列檔案上傳至子資料夾中的步驟 **資料總管** 刀鋒視窗。 在這個螢幕擷取畫面中，檔案會上傳到階層連結所示的子資料夾 (以紅色方塊標示)。

如果您要尋找要上傳的一些範例資料，您可以取得 **政策救護車資料** 資料夾從 [Azure 資料湖 Git 儲存機制](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData)。

![上傳資料](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Upload data")


## <a name="properties"></a>儲存資料的屬性和可用的動作

按一下新加入的檔案，以開啟 **屬性** 刀鋒視窗。 此刀鋒視窗提供檔案相關聯的屬性，以及您可以對檔案執行的動作。 您也可以在 Azure 資料湖存放區帳戶中複製檔案的完整路徑，如以下的螢幕擷取畫面中紅色方塊的反白顯示。

![資料上的屬性](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Properties on the data")

* 按一下 [ **預覽** 若要預覽的檔案，請直接從瀏覽器。 您也可以指定預覽的格式。 按一下 [ **預覽**, ，按一下 **格式** 中 **檔案預覽** 刀鋒視窗中，然後在 **檔案預覽格式** 刀鋒視窗中指定選項，例如數字的資料列顯示，若要使用，分隔符號，即可使用等的編碼方式。

  ![檔案預覽格式](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "File preview format")

* 按一下 [ **下載** 檔案下載到您的電腦。

* 按一下 [ **重新命名檔案** 重新命名檔案。

* 按一下 [ **刪除檔案** 刪除檔案。


## 保護您的資料

您可以在您的 Azure 資料湖存放區帳戶中使用 Azure Active Directory 和存取控制 (ACL) 保護儲存的資料。 如需如何進行這項指示，請參閱 [Azure 資料湖存放區中的資料保護](data-lake-store-secure-data.md)。


## 刪除 Azure 資料湖存放區帳戶

若要刪除的 Azure 資料湖市集帳戶，從資料湖存放區] 分頁，請按一下 [ **刪除**。 為了確認此動作，將提示您輸入您要刪除的帳戶名稱。 輸入帳戶的名稱，然後按一下 **刪除**。

![刪除資料湖帳戶](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Delete Data Lake account")

## 其他建立資料湖存放區帳戶的方法

- [使用 PowerShell 開始使用資料湖存放區](data-lake-store-get-started-powershell.md)
- [使用 .NET SDK 開始使用資料湖存放區](data-lake-store-get-started-net-sdk.md)
- [使用 Azure CLI 開始使用資料湖存放區](data-lake-store-get-started-cli.md)


## 後續步驟

- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
- [搭配 Data Lake 存放區使用 Azure Data Lake 分析](data-lake-analytics-get-started-portal.md)
- [搭配資料湖存放區使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)


