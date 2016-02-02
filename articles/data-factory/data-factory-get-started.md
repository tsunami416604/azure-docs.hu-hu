<properties
    pageTitle="教學課程：在 Azure Data Factory 管線中使用複製活動"
    description="本教學課程向您說明如何使用 Azure Data Factory 管線中的複製活動，從 Azure Blob 複製資料到 Azure SQL Database。"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="11/02/2015"
    ms.author="spelluru"/>


# 教學課程：從 Azure Blob 複製資料到 Azure SQL

> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


本文中的教學課程可協助您快速開始使用 Azure Data Factory 服務。 在本教學課程中，您將建立 Azure Data Factory，並在 Data Factory 中建立管線，以將資料從 Azure Blob 儲存體複製到 Azure SQL Database。
> [AZURE.NOTE] Data Factory 服務的詳細概觀，請參閱 [簡介 Azure Data Factory ][data-factory-introduction] 文件。

## 教學課程的必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。 如果您沒有訂用帳戶，則只需要幾分鐘的時間就可以建立免費試用帳戶。 請參閱 [免費試用版 ][azure-free-trial] 文章以取得詳細資料。
- **Azure 儲存體帳戶**。 您將使用 blob 儲存體做 **來源** 本教學課程中的資料存放區。 如果您沒有 Azure 儲存體帳戶，請參閱 [建立儲存體帳戶 ][data-factory-create-storage] 份文件建立一個步驟。
- **Azure SQL Database**。 在本教學課程中，您將使用 Azure SQL Database 做為**目的地**資料存放區。 如果您沒有 Azure SQL database 可讓您在本教學課程，請參閱 [如何建立和設定 Azure SQL Database ][data-factory-create-sql-database] 建立一個。
- **SQL Server 2012/2014 或 Visual Studio 2013**。 您將使用 SQL Server Management Studio 或 Visual Studio，建立範例資料庫以及檢視資料庫中的結果資料。

### 收集 Azure 儲存體帳戶的帳戶名稱和帳戶金鑰

您需要有 Azure 儲存體帳戶的帳戶名稱和帳戶金鑰，才能進行這個教學課程。 記下 **帳戶名稱** 和 **帳戶金鑰** 您儲存體帳戶，依照下列指示:

1. 登入 [Azure 入口網站 ][azure-portal]。
2. 按一下左邊的 [瀏覽]**** 中樞，然後選取 [儲存體帳戶]****。
3. 在 [**儲存體帳戶**] 刀鋒視窗中，選取您想要在本教學課程中使用的 [**Azure 儲存體帳戶**]。
4. 在 [**儲存體**] 刀鋒視窗中，按一下 [**金鑰**] 磚。
5. 在 [**管理金鑰**] 刀鋒視窗中，按一下 [**儲存體帳戶名稱**] 文字方塊旁的 [**複製** (影像)] 按鈕，然後將它儲存/貼到某個位置 (例如：在文字檔中)。
6. 重複上述步驟，複製或記下 [主要存取金鑰]****。
7. 按一下 **X**，關閉所有刀鋒視窗。

### 收集 Azure SQL Database 的伺服器名稱、資料庫名稱和使用者帳戶

您需要有 Azure SQL 伺服器、資料庫和使用者的名稱，才能進行這個教學課程。 遵循下面的指示，記下 Azure SQL Database 的**伺服器**、**資料庫**和**使用者**名稱：

1. 在 **Azure 入口網站**中，按一下左邊的 [瀏覽]****，然後選取 [SQL Database]****。
2. 在 [**SQL Database**] 刀鋒視窗中，選取您想要在本教學課程中使用的**資料庫**。 記下**資料庫名稱**。
3. 在 [**SQL Database**] 刀鋒視窗中，按一下 [**屬性**]。
4. 記下 [伺服器名稱]**** 和 [伺服器系統管理員登入]**** 的值。
5. 按一下 **X**，關閉所有刀鋒視窗。

### 允許 Azure 服務存取 Azure SQL 伺服器

確定**開啟** Azure SQL 伺服器的 [允許存取 Azure 服務]**** 設定，讓 Data Factory 服務可以存取您的 Azure SQL 伺服器。 若要確認並開啟此設定，請執行下列作業：

1. 按一下左邊的 [瀏覽]**** 中樞，然後按一下 [SQL Server]****。
2. 選取**您的伺服器**，然後按一下 [**SQL Server**] 刀鋒視窗上的 [**設定**]。
3. 在 [**設定**] 刀鋒視窗中，按一下 [**防火牆**]。
4. 在 [**防火牆設定**] 刀鋒視窗中，對 [**允許存取 Azure 服務**] 按一下 [**開啟**]。
5. 按一下 **X**，關閉所有刀鋒視窗。

### 準備教學課程所需的 Azure Blob 儲存體和 Azure SQL 資料庫

現在，請執行下列步驟，準備本教學課程中的 Azure Blob 儲存體和 Azure SQL Database。

1. 啟動 [記事本]，並貼上下列文字，然後將它以 **emp.txt** 的形式儲存至您硬碟上的 **C:\ADFGetStarted** 資料夾。

        John, Doe
        Jane, Doe

2. 使用工具，例如 [Azure 儲存體總管](https://azurestorageexplorer.codeplex.com/) 建立 **adftutorial** 容器和上傳 **emp.txt** 檔案的容器。

    ![Azure 儲存體總管](./media/data-factory-get-started/getstarted-storage-explorer.png)
3. 使用以下 SQL 指令碼，在您的 Azure SQL Database 中建立 **emp** 資料表。

     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
     )
     GO
    
     CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

 **如果您有 SQL Server 2012/2014 安裝在電腦上:** 遵循指示從 [步驟 2: 連接到 SQL Database 管理 Azure SQL Database 的使用 SQL Server Management Studio ][sql-management-studio] 文件，以連接到您的 Azure SQL server 並執行 SQL 指令碼。 請注意，本文使用 [Azure 傳統入口網站](http://manage.windowsazure.com), ，而非 [Azure 入口網站](http://portal.azure.com), 、 Azure SQL server 的防火牆設定。

 **如果您必須安裝在電腦上的 Visual Studio 2013:** 中 [Azure 入口網站](http://portal.azure.com), ，按一下 [ **瀏覽** 左側，中樞 **SQL 伺服器**, ，選取您的資料庫，然後按一下 **Visual Studio 中開啟** 工具列按鈕，連線到您的 Azure SQL server 並執行指令碼。 如果不允許您的用戶端存取 Azure SQL Server，則必須將 Azure SQL Server 的防火牆設定成允許從您的電腦 (IP 位址) 存取。 請參閱上文的步驟，為 Azure SQL Server 設定防火牆。


執行下列動作：

- 按一下 [ [使用 Data Factory 編輯器](data-factory-get-started-using-editor.md) 頂端使用 Data Factory 編輯器是 Azure 傳統入口網站的一部分來執行本教學課程的連結。
- 按一下 [ [使用 PowerShell](data-factory-monitor-manage-using-powershell.md) 頂端使用 Azure PowerShell 執行教學課程連結。
- 按一下 [ [使用 Visual Studio](data-factory-get-started-using-vs.md) 頂端使用 Visual Studio 2013 執行本教學課程連結。





[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/ 
[azure-portal]: https://portal.azure.com/ 
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2 
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md 
[data-factory-introduction]: data-factory-introduction.md 
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account 
[data-factory-create-sql-database]: ../sql-database-get-started.md 

