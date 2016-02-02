<properties 
    pageTitle="串流記錄和主控台" 
    description="串流記錄和主控台概觀" 
    authors="btardif" 
    manager="wpickett" 
    editor="" 
    services="app-service\web" 
    documentationCenter=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="11/12/2015" 
    ms.author="byvinyal"/>


# 串流記錄和主控台

### 串流記錄

Microsoft Azure 入口網站提供整合式串流記錄檢視器可讓您檢視追蹤事件 
即時 Azure App Service web 應用程式。

設定此功能只需要一些簡單的步驟：

- 在程式碼中撰寫追蹤
- 從 Azure 入口網站中啟用應用程式診斷
- 在 Web 應用程式刀鋒視窗上按一下串流記錄部分

### 如何在程式碼中撰寫追蹤

在程式碼中撰寫追蹤很簡單。 在 C# 中可輕易撰寫下列程式碼：

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

Trace 類別位於 System.Diagnostics 命名空間。

您可以在 node.js 應用程式中，撰寫此程式碼以取得相同的結果：

`````````````````````````
console.log("My trace statement").
`````````````````````````

### 如何啟用和檢視串流記錄

![][browsesitesscreenshot]
可依每個網站為基礎啟用診斷。 從 [入口網站](https://portal.azure.com) 按一下
 **瀏覽 (1)** 左側的功能表列上的按鈕，然後按一下 **Web 應用程式 (2)** 前往 **清單 (3)** 的 
 您的 web 應用程式。

按一下要設定的 Web 應用程式名稱以便進行巡覽。

![][diagnosticslogs]
然後按一下 [ **設定 (1)** > **診斷記錄 (2)** 開啟 **上** 
**的應用程式記錄 (Filesystem)(3)**  **層級** 選項可讓您變更的嚴重性 
要擷取之追蹤的層級。 您應該設定為 **Verbose** 如果您才剛 
取得熟悉功能，因此這可確保記錄追蹤陳述式。

按一下刀鋒頂端的 [儲存]****，隨後可準備檢視記錄。

**附註:** 高 **嚴重性層級** 記錄檔和多個追蹤耗用更多的資源 
就會出現。 請確定使用的這項功能時，這會設定為適當的層級 
高流量 / 生產網站。

![][streaminglogsscreenshot]
若要檢視串流記錄從入口網站按一下內 **工具 (1)** > **記錄 Stream(2)**。 如果您的應用程式 
會主動撰寫追蹤陳述式則您應在結果視窗中近乎即時看到它們。

## 主控台

Azure 入口網站提供能夠存取您 Web 應用程式環境的主控台。 您可以瀏覽您的網站 
應用程式的檔案系統並執行的 powershell/cmd 指令碼。 您會受到相同的權限集 
執行 web 應用程式程式碼執行主控台命令時。 您將無法存取受保護 
目錄或執行需要提高權限的指令碼。

![][consolescreenshot]
若要取得主控台，請如上節所述瀏覽至 Web 應用程式。 
按一下 [**工具**] > [**主控台**]，主控台隨即開啟。

若要熟悉主控台，請嘗試以下類似的基本命令：



`````````````````````````
dir
`````````````````````````
`````````````````````````
cd
`````````````````````````





[diagnosticslogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png 
[browsesitesscreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png 
[streaminglogsscreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png 
[consolescreenshot]: ./media/web-sites-streaming-logs-and-console/console.png 

