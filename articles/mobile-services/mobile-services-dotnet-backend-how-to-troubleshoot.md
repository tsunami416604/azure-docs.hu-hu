<properties
    pageTitle="行動服務 .NET 後端的疑難排解 | Microsoft Azure"
    description="了解如何使用 .NET 後端診斷和修正行動服務的問題"
    services="mobile-services"
    documentationCenter=""
    authors="wesmc7777"
    manager="dwrede"
    editor="mollybos"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="12/01/2015" 
    ms.author="wesmc;ricksal"/>

# 行動服務 .NET 後端的疑難排解

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


使用行動服務進行開發通常是簡單輕鬆的工作，但即便如此，有時可能還是會出錯。 本教學課程將提供相關技術，協助您排解行動服務 .NET 後端可能發生的常見問題。

1. [HTTP 偵錯](#HttpDebugging)
2. [執行階段偵錯](#RuntimeDebugging)
3. [分析診斷記錄](#Logs)
4. [偵錯雲端組件解析](#AssemblyResolution)
5. [實體架構移轉的疑難排解](#EFMigrations)

<a name="HttpDebugging"></a>
## HTTP 偵錯

使用行動服務開發應用程式時，您通常會將行動服務用戶端 SDK 用於您所使用的平台 (Windows 市集、iOS、Android 等)。 但有時候，往下了解 HTTP 層級並觀察在網路上執行的原始呼叫，可能會有所幫助。 此方法在偵錯連線和序列化問題時特別有用。 透過行動服務 .NET 後端，您可以將此方法與 Visual Studio 本機和遠端偵錯搭配使用 (下一節會有詳細說明)，以了解 HTTP 呼叫在教用您的服務程式碼之前所建立的路徑。

您可以使用任何 HTTP 偵錯程式來傳送和檢查 HTTP 流量。 [Fiddler](http://www.telerik.com/fiddler) 是基於此目的經常使用的開發人員工具。 為了讓開發人員的工作更輕鬆，行動服務會會搭售 Web 型 HTTP 偵錯程式 (也稱為測試用戶端)，而減少使用外部工具的需求。 當裝載您的行動服務在本機，它將會在類似的 URI [http://localhost:59233](http://localhost:59233) 和 URI 時在雲端中主控，將表單的 [http://todo-list.azure-mobile.net](http://todo-list.azure-mobile.net)。 無論在何處主控服務，下列步驟的會以相同方式運作：

1. 開始使用行動服務伺服器專案中開啟 **Visual Studio 2013 Update 2** 或更新版本。 如果您沒有帳戶，您可以建立依序選取 **檔案**, ，**新增**, ，**專案**, ，然後選取 **定域機組** 節點，然後 **Microsoft Azure 行動服務** 範本。
2. 叫用 **F5**, ，以建置並執行專案。 在 [開始] 頁面中，選取 **試試看**。

    >[AZURE.NOTE]
    > 如果是在本機主控服務，則在按一下連結後將會導向至下一頁。 但如果是在雲端中主控，則系統會提示您提供一組認證。 這是為了確保讓未經授權的使用者無法存取您 API 和裝載的相關資訊。 若要檢視頁面，您必須登入 **使用者名稱空白** 和 **應用程式金鑰** 做為密碼。 應用程式金鑰是使用 Azure 傳統入口網站中瀏覽至 **儀表板** 您的行動服務] 索引標籤，然後選取 **管理金鑰**。
    >
    > ![Authentication prompt to access help page][HelpPageAuth]

3. 您所檢視的頁面 (稱為「說明頁面」) 會顯示您的行動服務啟用的 HTTP API 完整清單。 選取其中一個 Api (如果您開始使用行動服務專案範本在 Visual Studio 中，您應該會看到 **GET tables/TodoItem**)。

    ![Help page][HelpPage]

4. 結果頁面會顯示任何文件與 JSON 範例要求和回應的 API 所預期。 選取 **試試看** ] 按鈕。

    ![Test page for an API][HelpPageApi]

5. 這是測試用戶端，可讓您傳送 HTTP 要求以試用您的 API。 選取 **傳送**。

    ![Test client][TestClient]

6. 您將會在瀏覽器視窗中看見您的行動服務傳回的 HTTP 回應。

    ![Test client with HTTP response][TestClientResponse]

現在，您已可在 Web 瀏覽器中瀏覽行動服務所公開的不同 HTTP API。

<a name="RuntimeDebugging"></a>
## 執行階段偵錯

.NET 後端的主要功能之一是在本機偵錯服務程式碼，以及偵錯在雲端環境中即時執行的程式碼。 請遵循下列步驟：

1. 開啟您想要在偵錯的行動服務專案 **Visual Studio 2013 Update 2** 或更新版本。
2. 設定符號載入。 瀏覽至 **偵錯** 功能表，然後選取 **選項和設定**。 請確認 **啟用 Just My Code** 未選取， **啟用來源伺服器支援** 已核取。

    ![Configure symbol loading][SymbolLoading]

3. 選取 **符號** 左方節點以及將參考加入 [SymbolSource] 伺服器使用的 URI [http://srv.symbolsource.org/pdb/Public](http://srv.symbolsource.org/pdb/Public)。 行動服務 .NET 後端的符號會以最新的版本啟用。

    ![Configure symbol server][SymbolServer]

4. 在程式碼片段中要進行偵錯之處，設定一個中斷點。 例如在中設定中斷點 **getalltodoitems （)** 方法 **TodoItemController** 所附的 Visual Studio 中的行動服務專案範本。
5. 服務的偵錯在本機進行按 **F5**。 第一次載入可能會比較慢，因為 Visual Studio 必須為行動服務 .NET 後端下載符號。
6. 如先前關於 HTTP 偵錯的章節所說明，您可以使用測試用戶端將 HTTP 要求傳送至您設定中斷點之處。 例如，您可以傳送要求以 **getalltodoitems （)** 方法藉由選取 **GET tables/TodoItem** 在說明頁面上，然後選取 **試試看** ，然後 **傳送**。
7. Visual Studio 應會在您設定時，中斷點中斷，而且應該提供附有原始程式碼的完整堆疊追蹤 **呼叫堆疊** Visual Studio 視窗中的。

    ![Hitting a breakpoint][Breakpoint]

8. 現在您可以將服務發行至 Azure，且我們將能夠以前述方式使用偵錯。 以滑鼠右鍵按一下它在發行專案 **方案總管] 中** ，然後選取 **發行**。
9. 在 **設定** ] 索引標籤的 [發行精靈選取 **偵錯** 組態。 這樣可以確保相關的偵錯符號都會隨您的程式碼發行。

    ![Publish debug][PublishDebug]

10. 服務已成功發佈後，開啟 **伺服器總管** 展開 **Azure** 和 **行動電話服務** 節點。 視需要登入。
11. 以滑鼠右鍵按一下您剛剛發行到的行動服務，然後選取 **附加偵錯工具**。

    ![連結偵錯工具][AttachDebugger]

12. 如同在步驟 6 中執行的動作，傳送 HTTP 要求，以叫用您已在其中設定中斷點的方法。 這次請使用 Azure 主控之行動服務的說明頁面和測試用戶端。
13. Visual Studio 會在中斷點上中斷。

現在，當您在 Azure 中進行本機開發會根據即時行動服務開發時，您已可存取 Visual Studio 偵錯程式的完整功能。

<a name="Logs"></a>
## 分析診斷記錄

您的行動服務處理來自客戶的要求時，會產生各種實用的診斷資訊，並擷取任何發生的例外狀況。 除此之外，您可以藉由運用檢測您的控制器程式碼，以其他記錄 [**記錄**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.apiservices.log.aspx) 上提供 [**服務**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.services.aspx) 屬性的每個 [**TableController**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx)。

在本機偵錯時，記錄會顯示在 Visual Studio **輸出** 視窗。

![Logs in Visual Studio Output window][LogsOutputWindow]

您將服務發行至 Azure 之後，在雲端中執行的服務執行個體的記錄檔都可以透過 Visual Studio 中的行動服務上按一下滑鼠右鍵 **伺服器總管** ，然後選取 [ **檢視記錄檔**。

![Logs in Visual Studio Server Explorer][LogsServerExplorer]

相同的記錄檔中也有 Azure 傳統入口網站上 **記錄** 您的行動服務] 索引標籤。

![登入 Azure 傳統入口網站][LogsPortal]

<a name="AssemblyResolution"></a>
## 偵錯雲端組件解析

當您將行動服務發行至 Azure 時，此服務會由行動服務主控環境載入，以確保能夠順利地升級和修補至主控控制器程式碼的 HTTP 管線。 這包括所參考的所有組件 [.NET 後端 NuGet 封裝](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22): 小組須持續更新服務，才能使用這些組件的最新版本。

它有時可能是因為版本控制衝突參考 *不同主要版本* 的必要組件 (不同 *次要* 允許版本)。 這常發生在 NuGet 提示您升級至行動服務 .NET 後端所使用的其中一個封裝的最新版本時。

>[AZURE.NOTE] 行動服務是目前只有與 ASP.NET 5.1 相容;目前不支援 ASP.NET 5.2。 在部署之後，將您的 ASP.NET NuGet 封裝升級至 5.2.* 可能會產生錯誤。

如果您升級了任何此類封裝，當您將更新的服務發行至 Azure 時，您將會看見指出衝突的警告頁面：

![Help page indicating assembly loading conflict][HelpConflict]

此外還會有如下的例外狀況訊息記錄在您的服務記錄中：

    Found conflicts between different versions of the same dependent assembly 'Microsoft.ServiceBus': 2.2.0.0, 2.3.0.0. Please change your project to use version '2.2.0.0' which is the one currently supported by the hosting environment.

這個問題很容易更正：回復為必要組件的支援版本，然後重新發行您的服務即可。

<a name="EFMigrations"></a>
## 實體架構移轉的疑難排解

使用附有 SQL Database 的行動服務 .NET 後端時，會以 Entity Framework (EF) 作為資料存取技術，讓您能夠查詢資料庫及保存物件。 EF 代表開發人員處理的一個重點是如何資料庫資料行 (也稱為 *結構描述*) 變更為程式碼變更中指定的模型類別。 這個程序稱為 [Code First 移轉](http://msdn.microsoft.com/data/jj591621)。

移轉可能會很複雜，且資料庫狀態與 EF 模型必須保持同步，才能成功。 如需有關如何處理移轉您的行動服務和可能發生之錯誤的指示，請參閱 [如何對.NET 後端行動服務進行資料模型變更](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)。

<!-- IMAGES -->

[HelpPageAuth]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/6.png
[HelpPage]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/7.png
[HelpPageApi]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/8.png
[TestClient]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/9.png
[TestClientResponse]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/10.png
[SymbolLoading]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/1.png
[SymbolServer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/2.png
[Breakpoint]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/3.png
[PublishDebug]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/4.png
[AttachDebugger]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/5.png
[LogsOutputWindow]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/11.png
[LogsServerExplorer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/12.png
[LogsPortal]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/13.png
[HelpConflict]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/14.png


<!-- Links -->
[SymbolSource]:http://symbolsource.org

