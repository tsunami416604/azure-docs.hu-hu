<properties
    pageTitle="如何使用適用於 Mobile Apps 的 Node.js 後端伺服器 SDK | Azure App Service"
    description="了解如何使用適用於 Azure App Service Mobile Apps 的 Node.js 後端伺服器 SDK。"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="node"
    ms.topic="article"
    ms.date="12/02/2015"
    ms.author="adrianhall"/>

# 如何使用 Azure Mobile Apps Node.js SDK

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]
&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本文提供詳細的資訊及範例，說明如何在 Azure App Service Mobile Apps 中使用 Node.js 後端。

> [AZURE.NOTE] 此 SDK 僅供預覽。  因此，不建議您在生產環境中使用此 SDK。  這份文件中的範例使用 v2.0.0-beta1 的 [azure-mobile-apps]。

## <a name="Introduction"></a>簡介

Azure App Service Mobile Apps 可讓您將行動最佳化資料存取 Web API 新增至 Web 應用程式。  Azure 應用程式
提供 ASP.NET 和 Node.js web 應用程式服務行動應用程式 SDK。  此 SDK 提供下列作業：

- 資料存取的資料表作業 (讀取、插入、更新、刪除)
- 自訂 API 作業

這兩種作業提供整個 Azure 應用程式服務，包括社交身分識別所允許的所有身分識別提供者驗證
提供者，例如 Facebook、 Twitter、 Google 和 Microsoft，以及 Azure Active Directory 的企業身分識別。

您可以在每個使用案例中找到範例 [samples directory on GitHub]。

### <a name="howto-cmdline-basicapp"></a>作法：使用命令列建立基本 Node.js 後端

每個 Azure App Service Mobile App Node.js 後端都會以 ExpressJS 應用程式的形式啟動。  ExpressJS 是最受歡迎的 web 服務架構
適用於 Node.js。  您可以建立基本 [Express] 應用程式，如下所示 ︰

1. 在命令或 PowerShell 視窗中，為您的專案建立新的目錄。

        mkdir basicapp

2. 執行 npm init 以初始化封裝結構。

        cd basicapp
        npm init

    Npm init 命令會詢問一組問題以初始化專案。  請參閱以下的範例輸出

    ![npm init 輸出][0]

3. 從 npm 儲存機制安裝 express 和 azure-mobile-apps 資源庫。

        npm install --save express azure-mobile-apps

4. 建立 app.js 檔案以實作基本行動伺服器。

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table
        mobile.tables.add('TodoItem');

        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);

此應用程式建立簡單的行動裝置最佳化 WebAPI 與單一端點 (`/tables/TodoItem`) 提供
未驗證的存取基礎的 SQL 資料存放區使用動態結構描述。  它適合下列
用戶端程式庫快速入門 ︰

- [Android 用戶端快速入門]
- [iOS 用戶端快速入門]
- [Windows 市集用戶端快速入門]
- [Xamarin.iOS 用戶端快速入門]
- [Xamarin.Android 用戶端快速入門]
- [Xamarin.Forms 用戶端快速入門]


您可以找到這個基本應用程式的程式碼 [basicapp sample on GitHub]。

### <a name="howto-vs2015-basicapp"></a>作法：使用 Visual Studio 2015 建立 Node 後端

Visual Studio 2015 需要延伸模組才能在整合式開發環境 (IDE) 內開發 Node.js 應用程式。  若要啟動、 下載及安裝 [Node.js Tools 1.1 for Visual Studio]。  安裝 Node.js Tools for Visual Studio 後，請建立 Express 4.x 應用程式：

1. 開啟 **新的專案** 對話方塊 (從 **檔案** > **新增** > **專案...**)。

2. 展開 **範本** > **JavaScript** > **Node.js**。

3. 選取 **基本 Azure 的 Node.js Express 4 應用程式**。

4. 填入專案名稱。  按一下 [ *確定*。

    ![Visual Studio 2015 新專案][1]

5. 以滑鼠右鍵按一下 **npm** 節點，然後選取 **新安裝的 npm 封裝...**。

6. 建立第一個 Node.js 應用程式時，您可能必須重新整理 npm 目錄。  如有必要，系統將提示您-按一下 **重新整理**。

7. 輸入 _azure 行動應用程式_ 在搜尋方塊中。  按一下 [ **azure mobile apps 2.0.0** 套件，然後按一下 [ **安裝封裝**。

    ![安裝新的 npm 封裝][2]

8. 按一下 [ **關閉**。

9. 開啟 _app.js_ 檔案，以加入 Azure 行動應用程式 SDK 的支援。  在程式庫 require 陳述式底部的第 6 行，加入下列程式碼：

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    在其他 app.use 陳述式之後大約第 27 行，加入下列程式碼：

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use('mobile');

    儲存檔案。

10. 執行本機應用程式 （將在 http://localhost:3000/ 服務 API） 或發佈至 Azure。

### <a name="download-quickstart"></a>作法：使用 Git 下載 Node.js 後端快速入門程式碼專案

當您使用入口網站建立新的 Node.js 行動應用程式後端 **快速入門** 為您建立並部署到您的網站] 分頁中，新的 Node.js 專案。 您可以在入口網站中新增資料表和 API，並編輯 Node.js 後端的程式碼檔案。 您也可以使用各種部署工具的其中一個來下載後端專案，以便新增或修改資料表和 API，然後重新發佈專案。 如需詳細資訊，請參閱 [Azure App Service Deployment Guide]。 下列程序會使用 Git 儲存機制下載的快速入門專案程式碼。

1. 如果尚未安裝 Git，請先安裝。 安裝 Git 所需的步驟會因作業系統而有所不同。 請參閱 [安裝 Git](http://git-scm.com/book/en/Getting-Started-Installing-Git) 如需作業系統特定的發佈和安裝指引。

2. 請依照 [啟用 web 應用程式儲存機制](../app-service-web/web-sites-publish-source-control.md#Step4) 來啟用您的後端網站，部署使用者名稱和密碼記下 Git 儲存機制。

3. 在您的行動應用程式後端的分頁中，記下 **Git 複製 URL** 設定。

4.  在 Git 感知命令列工具中使用 Git 複製 URL 執行 `git clone` 命令，並在需要時輸入您的密碼，如下列範例所示：

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. 瀏覽至本機目錄 (在上述範例中為 /todolist)，並注意專案檔案已下載。 在 /tables 子資料夾中，您會發現 todoitem.json 檔案 (其定義資料表的權限) 和 todoitem.js 檔案 (其定義資料表的 CRUD 作業指令碼)。

6. 在您變更專案檔案之後，請執行下列命令來新增、認可變更，然後將變更上傳至網站：

        $ git commit -m "updated the table script"
        $ git push origin master

    當您將新檔案加入至專案時，您必須先執行 `git add .` 命令。

每次有一組新的認可推送至網站時，就會重新發佈網站。

### <a name="howto-publish-to-azure"></a>作法：將您的 Node.js 後端發佈至 Azure

Microsoft Azure 提供許多將 Azure App Service Mobile Apps Node.js 後端發佈至 Azure 服務的機制。  其中包括使用整合至 Visual Studio 中的部署工具、命令列工具，和以原始檔控制為基礎的連續部署選項。  如需有關本主題的詳細資訊，請參閱 [Azure App Service Deployment Guide]。

Azure App Service 提供 Node.js 應用程式方面的具體建議，您應該在部署之前先檢閱：

- 如何 [specify the Node Version]
- 如何 [use Node modules]

## <a name="TableOperations"></a>資料表作業

azure-mobile-apps Node.js Server SDK 提供將儲存在 Azure SQL Database 中的資料表公開為 WebAPI 的機制。  提供的作業有五種。

| 作業 | 說明 |
| --------- | ----------- |
| GET /tables/{_tablename_ | 取得資料表中的所有記錄 |
| GET /tables/{_tablename_/:id | 取得資料表中的特定記錄 |
| POST /tables/{_tablename_ | 在資料表中建立新記錄 |
| 修補程式 /tables/{_tablename_/:id | 更新資料表中的現有記錄 |
| 刪除 /tables/{_tablename_/:id | 刪除資料表中的記錄 |

支援這個 WebAPI [OData] 並擴充資料表結構描述，以支援 [offline data sync]。

### <a name="howto-dynamicschema"></a>作法：使用動態結構描述定義資料表

資料表必須先經過定義才能使用。  資料表可用靜態結構描述來定義 (開發人員在結構描述中定義資料行)，或以動態方式定義 (SDK 會根據傳入的要求控制結構描述)。 此外，開發人員可將 Javascript 程式碼新增至定義，以控制 WebAPI 的特定層面。

根據最佳作法，您應在資料表目錄中的 Javascript 檔案內定義每個資料表，然後使用 tables.import() 方法匯入資料表。
擴充基本應用程式後，會調整 app.js 檔案：

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

在 ./tables/TodoItem.js 中定義資料表：

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

資料表依預設會使用動態結構描述。  若要全域關閉動態結構描述，請將應用程式設定 **MS_DynamicSchema** Azure 入口網站中設為 false。

您可以找到完整的範例中 [todo sample on GitHub]。

### <a name="howto-staticschema"></a>作法：使用靜態結構描述定義資料表

您可以將資料行明確定義為要透過 WebAPI 公開。  azure-mobile-apps Node.js SDK 會自動將離線資料同步所需的任何其他資料行新增至您所提供的清單。  例如，快速入門用戶端應用程式需要具有兩個資料行的資料表：文字 (字串) 和完整 (布林值)。  這可以定義在資料表定義 JavaScript 檔案中 (位於資料表目錄中)，如下所示：

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

如果您以靜態方式定義資料表，則您還必須呼叫 tables.initialize() 方法，以在啟動時建立資料庫結構描述。  Tables.initialize() 方法會傳回 [Promise] -這用來確保 web 服務不會服務要求之前正在初始化資料庫。

### <a name="howto-sqlexpress-setup"></a>作法：以 SQL Express 作為本機電腦上的開發資料存放區

Azure Mobile Apps AzureMobile Apps Node SDK 提供三種現成可用的資料提供選項：SDK 提供三種現成可用的資料提供選項：

- 使用 **記憶體** 提供一個範例中非持續性存放區的驅動程式
- 使用 **mssql** 開發提供的 SQL Express 的資料存放區的驅動程式
- 使用 **mssql** 用於生產環境中提供的 Azure SQL Database 資料存放區的驅動程式

使用 Azure 行動應用程式 Node.js SDK [mssql Node.js package] 來建立及使用 SQL Express 和 SQL 資料庫的連線。  要使用此封裝，您必須在 SQL Express 執行個體上啟用 TCP 連線。

> [AZURE.TIP] 記憶體驅動程式不提供一組完整的測試。  如果您想要測試您的後端在本機，
我們建議使用 SQL Express 的資料存放區和 mssql 驅動程式。

1. 下載並安裝 [Microsoft SQL Server 2014 Express]。  請確實安裝 SQL Server 2014 Express with Tools 版。  除非您明確
需要 64 位元支援 32 位元版本執行時，會耗用較少的記憶體。

2. 執行 SQL Server 2014 組態管理員。

  1. 展開 **SQL Server 網路組態** 左邊樹狀結構] 功能表中的節點。
  2. 按一下 [ **SQLEXPRESS 的通訊協定**。
  3. 以滑鼠右鍵按一下 **TCP/IP** ，然後選取 **啟用**。  按一下 [ **確定** 在快顯對話方塊。
  4. 以滑鼠右鍵按一下 **TCP/IP** ，然後選取 **屬性**。
  5. 按一下 [ **IP 位址** ] 索引標籤。
  6. 尋找 **IPAll** 節點。  在 **TCP 連接埠** 欄位中，輸入 **1433年**。

         ![Configure SQL Express for TCP/IP][3]
  7. 按一下 [ **確定**。  按一下 [ **確定** 在快顯對話方塊。
  8. 按一下 [ **SQL Server 服務** 左邊樹狀結構功能表中。
  9. 以滑鼠右鍵按一下 **SQL Server (SQLEXPRESS)** ，然後選取 **重新啟動**
  10. 關閉 SQL Server 2014 組態管理員。

3. 執行 SQL Server 2014 Management Studio 並連接到您的本機 SQL Express 執行個體

  1. 您在 [物件總管] 中的執行個體上按一下滑鼠右鍵，然後選取 **屬性**
  2. 選取 **安全性** 頁面。
  3. 請確定 **SQL Server 和 Windows 驗證模式** 已選取
  4. 按一下 [ **[確定]**

        ![設定 SQL Express 驗證][4]

  5. 展開 **安全性** > **登入** 物件總管] 中
  6. 以滑鼠右鍵按一下 **登入** ，然後選取 **新登入...**
  7. 輸入登入名稱。  選取 **SQL Server 驗證**。  輸入密碼，然後輸入相同密碼 **確認密碼**。  請注意，密碼必須符合 Windows 複雜性需求。
  8. 按一下 [ **[確定]**

        ![將新的使用者新增至 SQL Express][5]

  9. 以滑鼠右鍵按一下新的登入，然後選取 **屬性**
  10. 選取 **伺服器角色** 頁面
  11. 核取方塊旁 **dbcreator** 伺服器角色
  12. 按一下 [ **[確定]**
  13. 關閉 SQL Server 2015 Management Studio

請確實記下您選取的使用者名稱和密碼。  您可能需要根據您特定的資料庫需求指派其他伺服器角色或權限。

Node.js 應用程式會讀取 **SQLCONNSTR_MS_TableConnectionString** 環境變數，以讀取此資料庫的連接字串。  您可以將它設定在您的環境中。  例如，您可以使用 PowerShell 來設定此環境變數：

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

請注意，您必須透過 TCP/IP 連線存取資料庫，並提供連線的使用者名稱和密碼。

### <a name="howto-config-localdev"></a>作法：設定專案以在本機上進行開發

Azure 行動應用程式會讀取名為的 JavaScript 檔案 _azureMobile.js_ 從本機檔案系統。  您不應該使用此檔案在生產環境中設定 Azure 行動應用程式 SDK-應用程式中使用的設定 [Azure Portal] 改。   _AzureMobile.js_ 檔案應該匯出的組態物件。  最常見的設定如下：

- 資料庫設定
- 診斷記錄設定
- 替代 CORS 設定

範例 _azureMobile.js_ 檔案實作上面所列的資料庫設定如下 ︰

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

我們建議您將加入 _azureMobile.js_ 到您 _.gitignore_ 檔案 （或其他原始程式碼控制忽略檔案） 來防止密碼從
儲存在雲端中。  永遠在應用程式設定中設定實際執行設定 [Azure Portal]。

### <a name="howto-appsettings"><a>設定行動應用程式的應用程式設定

在大部分的設定 _azureMobile.js_ 檔案中有對等應用程式設定 [Azure Portal]。  使用下列清單來設定您
在應用程式設定的應用程式 ︰

| 應用程式設定                 | _azureMobile.js_ 設定  | 說明                               | 有效值                                |
| :-------------------------- | :------------------------ | :---------------------------------------- | :------------------------------------------ |
| **MS_MobileAppName**        | 名稱                      | 應用程式的名稱                       | 字串                                      |
| **MS_MobileLoggingLevel**   | logging.level             | 要記錄的訊息的最小記錄層級      | error、warning、info、verbose、debug、silly |
| **MS_DebugMode**            | debug                     | 啟用或停用偵錯模式              | true、false                                 |
| **MS_TableSchema**          | data.schema               | SQL 資料表的預設結構描述名稱        | 字串 (預設值：dbo)                       |
| **MS_DynamicSchema**        | data.dynamicSchema        | 啟用或停用偵錯模式              | true、false                                 |
| **MS_DisableVersionHeader** | version (設定為未定義)| 停用 X-ZUMO-Server-Version 標頭 | true、false                                 |
| **MS_SkipVersionCheck**     | skipversioncheck          | 停用用戶端 API 版本檢查     | true、false                                 |

設定「應用程式設定」：

1. 登入 [Azure Portal]。
2. 選取 **所有資源** 或 **應用程式服務** 然後按一下 [行動應用程式的名稱。
3. 如果沒有的話，按一下 [設定] 刀鋒視窗會開啟預設值- **設定**。
4. 按一下 [ **應用程式設定** 一般功能表中。
5. 捲動至 [應用程式設定] 區段。
6. 如果您的應用程式設定已經存在，請按一下應用程式設定的值來編輯該值。
7. 如果您的應用程式設定不存在，請在 [機碼] 方塊中輸入「應用程式設定」及在 [值] 方塊中輸入值。
8. 完成後，按一下 **儲存**。

變更大部分的應用程式設定都需要重新啟動服務。

### <a name="howto-use-sqlazure"></a>做法：使用 SQL Database 做為您的實際執行資料存放區

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

無論是何種 Azure App Service 應用程式類型，以 SQL Database 作為資料存放區的程序都是相同的。 如果您尚未執行，請依照下列步驟建立新的行動應用程式後端。

1. 登入 [Azure Portal]。

2. 右上方的 [] 視窗中，按一下 [ **+ 新增** 按鈕 > **Web + 行動** > **行動應用程式**, ，然後提供您的行動應用程式後端的名稱。

3. 在 **資源群組** 方塊中，輸入您的應用程式相同的名稱。

4. 系統將會選取預設應用程式服務方案。  如果您想要變更您的應用程式服務計劃，您只要按一下應用程式服務計劃 > **+ 新建**。  為新的應用程式服務方案命名，並選取適當位置。  按一下 [定價層]，並選取適當的服務定價層。 選取 **檢視所有** 更定價選項，例如檢視 **免費** 和 **共用**。  一旦您選取定價層，按一下 **選取** ] 按鈕。  回到 **應用程式服務方案** 刀鋒視窗中，按一下 [ **確定**。

5. 按一下 [ **建立**。 如此即會建立您稍後將部署伺服器專案的行動應用程式後端。  佈建行動應用程式後端可能需要幾分鐘。  一旦佈建行動應用程式後端時，入口網站將會開啟 **設定** 行動應用程式後端的刀鋒視窗。

行動應用程式後端建立後，您可以選擇將現有的 SQL Database 連接到您的行動應用程式後端，或建立新的 SQL Database。  在這一節中，您將建立新的 SQL Database。

> [AZURE.NOTE] 如果您已在相同的位置做為新的行動裝置應用程式後端資料庫，則您可以改選 **使用現有的資料庫** ，然後選取該資料庫。 不建議您使用位在不同位置的資料庫，因為這會需要額外的頻寬成本和產生更高的延遲。

6. 在新的行動應用程式後端中，按一下 [ **設定** > **行動應用程式** > **資料** > **+ 新增**。

7. 在 **加入資料連接** 刀鋒視窗中，按一下 [ **SQL 資料庫-必要的設定** > **建立新的資料庫**。  輸入新資料庫中的名稱 **名稱** 欄位。

8. 按一下 [ **伺服器**。  在 **新伺服器** 刀鋒視窗中，輸入唯一的伺服器名稱中 **伺服器名稱** 欄位，並提供適合 **伺服器管理員登入** 和 **密碼**。  請確定 **允許 azure 服務存取伺服器** 已核取。  按一下 [ **確定**。

    ![建立 Azure SQL Database][6]

9. 在 **新的資料庫** 分頁上，按一下 **確定**。

10. 回到 **加入資料連接** 分頁中，選取 **連接字串**, ，輸入登入和密碼，只提供建立資料庫時。  如果您使用現有的資料庫，請提供該資料庫的登入認證。  在輸入後，按一下 [ **確定**。

11. 回到 **加入資料連接** 刀鋒視窗中，按一下 **確定** 來建立資料庫。

<!--- END OF ALTERNATE INCLUDE -->

建立資料庫可能需要幾分鐘的時間。  使用 **通知** 區來監視部署的進度。  在資料庫成功部署之前，請勿繼續進行。  成功部署後，將會在您行動後端的 [應用程式設定] 中建立 SQL 資料庫執行個體的連接字串。  您可以看到這個應用程式設定中的 **設定** > **應用程式設定** > **連接字串**。

### <a name="howto-tables-auth"></a>做法：需經過驗證才能存取資料表

如果您想要使用與資料表端點的應用程式服務驗證，您必須設定應用程式中的服務驗證 [Azure Portal] 第一次。  如需
在 Azure App Service 中設定驗證的詳細檢閱組態指南 》，您想要使用的身分識別提供者 ︰

- [如何設定 Azure Active Directory 驗證]
- [如何設定 Facebook 驗證]
- [如何設定 Google 驗證]
- [如何設定 Microsoft 驗證]
- [如何設定 Twitter 驗證]

每個資料表都有一個存取屬性可用來控制對資料表的存取。  下列範例說明以靜態方式定義且需要驗證的資料表。

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

存取屬性接受三種值

  - *匿名* 表示允許用戶端應用程式讀取未經驗證的資料
  - *驗證* 指示用戶端應用程式必須傳送有效的驗證權杖要求
  - *停用* 表示這個資料表目前已停用

如果未定義存取屬性，則會允許未經驗證的存取。

### <a name="howto-tables-disabled"></a>做法：停用對特定資料表作業的存取權

除了會出現在資料表上以外，存取屬性也可用來控制個別作業。  共有四種作業：

  - *讀取* 是 RESTful GET 作業的資料表
  - *插入* 是在資料表上的 RESTful POST 作業
  - *更新* 資料表 RESTful 修補作業
  - *刪除* 資料表符合 rest 限制的刪除作業

比方說，您可能會想要提供未經驗證的唯讀資料表。  這可由以下資料表定義來提供：

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>做法：調整與資料表作業搭配使用的查詢

資料表作業的常見需求是提供受限制的資料檢視。  例如，您可以提供的資料表
標記已驗證的使用者識別碼，使用者只能讀取或更新自己的記錄。  以下資料表定義
會提供這項功能 ︰

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    }

    module.exports = table;

正常執行的查詢作業，會有可使用 Where 子句來調整的查詢屬性。    查詢屬性
 [QueryJS] 用來將 OData 查詢轉換成可以處理資料後端的物件。  簡單的相等案例
（如上述），可以使用對應。 加入特定的 SQL 子句，相對而言也較簡單：

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>做法：設定資料表上的虛刪除

虛刪除並不會實際刪除記錄。  它會將已刪除的資料行設定為 true，而將記錄標示為已在資料庫中刪除。  Azure Mobile Apps SDK 會自動從結果中移除已虛刪除的記錄，除非 Mobile Client SDK 使用 IncludeDeleted()。  若要為資料表設定虛刪除，請在資料表定義檔中設定 softDelete 屬性。  可能的範例如下：

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Turn on Soft Delete
    table.softDelete = true;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

您將必須建立清除記錄的機制 - 從用戶端應用程式、透過 WebJob 或透過自訂機制。

### <a name="howto-tables-seeding"></a>做法：在您的資料庫中植入資料

在建立新的應用程式時，您可能會想要在資料表中植入資料。  這可做為資料表定義 JavaScript 檔案中完成
頁面：

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

請務必注意，只有在 Azure Mobile Apps SDK 所建立的資料表中，才可執行資料植入。  如果資料表已經
存在於資料庫中沒有資料插入資料表。  如果啟用動態結構描述後，然後將會從推斷結構描述
植入的資料。

建議您明確呼叫 initialize () 方法，以在服務開始執行時建立資料表。

## <a name="CustomAPI"></a>自訂 API

除了透過 /tables 端點的資料存取 API 以外，Azure Mobile Apps 也可提供自訂 API 涵蓋範圍。  中所定義的自訂 Api
資料表定義類似的方式，而且可以存取所有相同功能，包括驗證。

如果您想要使用自訂 API 的應用程式服務驗證，您必須設定應用程式中的服務驗證 [Azure Portal] 第一次。  如需
在 Azure App Service 中設定驗證的詳細檢閱組態指南 》，您想要使用的身分識別提供者 ︰

- [如何設定 Azure Active Directory 驗證]
- [如何設定 Facebook 驗證]
- [如何設定 Google 驗證]
- [如何設定 Microsoft 驗證]
- [如何設定 Twitter 驗證]

### <a name="howto-customapi-basic"></a>做法：定義簡單的自訂 API

定義自訂 API 的方法與資料表 API 很類似。

1. 建立 **api** 目錄
2. 建立中的 API 定義 JavaScript 檔案 **api** 目錄。
3. 使用匯入方法來匯入 **api** 目錄。

以下是根據我們先前使用的基本應用程式範例所做的原型 API 定義。

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

讓我們來看，它會傳回伺服器的日期使用簡單的 API _Date.now()_ 方法。  以下是 api/date.js 檔案：

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

每個參數都是標準 RESTful 動詞之一 - GET、POST、PATCH 或 DELETE。  方法是一種標準 [ExpressJS Middleware] 傳送所需的輸出的函式。

### <a name="howto-customapi-auth"></a>做法：需經過驗證才能存取自訂 API

Azure Mobile Apps SDK 對於資料表端點和自訂 API 會使用相同的方式實作驗證。  若要將驗證新增至前一節中所開發的 API，加入 **存取** 屬性 ︰

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // All methods must be authenticated.
    api.access = 'authenticated';

    module.exports = api;

您也可以指定特定作業的驗證：

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // The GET methods must be authenticated.
    api.get.access = 'authenticated';

    module.exports = api;

對於需要驗證的自訂 API，必須使用資料表端點所使用的相同權杖。

## <a name="Debugging"></a>偵錯與疑難排解

Azure App Service 提供數個適用於 Node.js 應用程式的偵錯和疑難排解技術。
這些技術全都可以使用。

- [監視 Azure App Service]
- [在 Azure App Service 中啟用診斷記錄]
- [在 Visual Studio 中疑難排解 Azure App Service]

### <a name="howto-diagnostic-logs"></a>做法：寫入至 Azure Mobile Apps 診斷記錄

Node.js 應用程式可存取多種不同的診斷記錄工具。  就內部而言，會使用 Azure 行動應用程式 Node.js SDK [Winston] 的診斷記錄。  啟用此選項會自動啟用偵錯模式，或是設定 **MS_DebugMode** 應用程式設定為 true，在 [Azure Portal]。  產生的記錄檔時，會出現在 [診斷記錄檔上，在 [Azure Portal]。

## <a name="in-portal-editing"></a>入口網站中的程式碼編輯經驗

Azure 入口網站中的特殊工具可讓您輕鬆地使用 Node.js 後端專案，而不需下載程式碼專案。 入口網站中的簡單資料表和簡單 API，可讓您直接在入口網站中建立及使用資料表和自訂 API。 您甚至可以直接在入口網站中使用 Visual Studio Team Services "Monaco" 編輯器，來編輯資料表作業和 API 指令碼。

### <a name="work-easy-tables"></a>做法：在 Azure 入口網站中使用簡單資料表

當您按一下 **簡單資料表** 後端站台設定中加入新的資料表或修改或刪除現有的資料表。 您也可以查看資料表中的資料。

![使用簡單資料表](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

下列命令可用於資料表的命令列：

+ **變更權限** -修改的權限讀取、 插入、 更新和刪除資料表上的作業。 選項包括允許匿名存取、要求驗證，或停用作業的所有存取。 這可修改 table.json 專案程式碼檔案。
+ **編輯指令碼** -Visual Studio Team Services 編輯器中開啟資料表指令碼檔案。
+ **管理結構描述** -新增或刪除資料行或變更資料表的索引。
+ **清除資料表** -截斷現有的資料表不會刪除所有資料列，但保留結構描述變更。
+ **刪除資料列** -刪除的個別資料列。
+ **串流記錄檢視** -連線到您的網站串流記錄服務。

###<a name="work-easy-apis"></a>做法：在 Azure 入口網站中使用簡單 API

當您按一下 **簡單 Api** 後端站台設定中加入新的自訂 API 端點或修改或刪除現有的 API 端點。

![使用簡單 API](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

在入口網站中，您可以變更指定之 HTTP 動作的存取權限、在 Visual Studio Team Services 編輯器中編輯 API 指令碼檔案，或檢視資料流記錄。

###<a name="online-editor"></a>做法：在 Visual Studio Team Services 中編輯程式碼

Azure 入口網站可讓您在 Visual Studio Team Services 中編輯 Node.js 後端指令碼檔案，而不需將專案下載到本機電腦。 若要在線上編輯器中編輯指令碼檔案：

1. 在您的行動應用程式後端] 分頁中，按一下 [ **所有設定** > 是 **簡單資料表** 或 **簡單 Api**, 、 資料表或 API，然後按一下 [ **編輯指令碼**。 將會在 Visual Studio Team Services 編輯器中開啟指令碼檔案。

    ![Visual Studio Team Services 程式碼編輯器](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)

2. 在線上編輯器中變更程式碼檔案。 當您輸入資料時，會自動儲存變更。

您也可以在網站上，從編輯器執行程式碼。


<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ../../includes/media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png

<!-- URLs -->
[Android Client QuickStart]: app-service-mobile-android-get-started.md
[iOS Client QuickStart]: app-service-mobile-ios-get-started.md
[Xamarin.iOS Client QuickStart]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android Client QuickStart]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms Client QuickStart]: app-service-mobile-xamarin-forms-get-started.md
[Windows Store Client QuickStart]: app-service-mobile-windows-store-dotnet-get-started.md
[HTML/Javascript Client QuickStart]: app-service-html-get-started.md
[offline data sync]: app-service-mobile-offline-data-sync.md
[How to configure Azure Active Directory Authentication]: app-service-mobile-how-to-configure-active-directory-authentication.md
[How to configure Facebook Authentication]: app-service-mobile-how-to-configure-facebook-authentication.md
[How to configure Google Authentication]: app-service-mobile-how-to-configure-google-authentication.md
[How to configure Microsoft Authentication]: app-service-mobile-how-to-configure-microsoft-authentication.md
[How to configure Twitter Authentication]: app-service-mobile-how-to-configure-twitter-authentication.md
[Azure App Service Deployment Guide]: ../app-service-web/web-site-deploy.md
[Monitoring an Azure App Service]: ../app-service-web/web-sites-monitor.md
[Enable Diagnostic Logging in Azure App Service]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Toubleshoot an Azure App Service in Visual Studio]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[specify the Node Version]: ../nodejs-specify-node-version-azure-apps.md
[use Node modules]: ../nodejs-use-node-mobiles-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/

[Azure Portal]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston


