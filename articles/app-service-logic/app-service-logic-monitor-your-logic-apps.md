<properties 
    pageTitle="在 Azure App Service 中監視 Logic Apps | Microsoft Azure" 
    description="如何查看邏輯應用程式的完成項目" 
    authors="stepsic-microsoft-com" 
    manager="dwrede" 
    editor="" 
    services="app-service\logic" 
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="stepsic"/>

# 監視邏輯應用程式

建立邏輯應用程式之後 (在步驟 [建立邏輯應用程式](app-service-logic-create-a-logic-app.md))，您可以看到 Azure 入口網站中執行的完整記錄。 若要檢視歷程記錄，請選取 **瀏覽**, ，請選取 **Web + 行動**, ，然後選取 **邏輯應用程式**。 便會顯示您訂用帳戶中所有邏輯應用程式的清單。 邏輯應用程式可以是 **啟用** 或 **已停用**。 **啟用** 邏輯應用程式代表觸發程序執行邏輯應用程式，以回應觸發程序事件。 **已停用** 邏輯應用程式無法執行以回應事件。

![概觀](./media/app-service-logic-monitor-your-logic-apps/overview.png)

出現邏輯應用程式分頁時，有兩個區段相當有用：

- **摘要**, ] 會告訴您最新狀態，而且還是編輯邏輯應用程式的進入點。
- **所有執行**, ，其中顯示此邏輯應用程式已的執行的清單。

## 檢視應用程式的執行

![所有規則](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

這份執行清單會顯示 **開始時間**, 、 **執行識別碼** （您可以使用此呼叫 REST API 時），而 **持續時間** 的特定執行。 選取任何資料列以查看該執行的詳細資料。

[詳細資料] 分頁會顯示包含執行中所有動作之執行時間和順序的圖表。 圖表下面是所有已執行動作的完整清單：  

![執行和動作](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

最後，針對特定的動作，您可以取得所有的資料傳遞至動作的動作中接收 **輸入** 和 **輸出** 區段。 選取連結以查看完整內容 (您也可以複製連結以下載內容)。 

另一個重要的資訊是 **追蹤識別碼**。 這個識別碼會在所有動作呼叫的標頭中進行傳遞。 如果在您自己的服務內已有記錄，我們建議您記錄此追蹤識別碼，您便可以使用此識別碼來交互參照自己的記錄檔。

## 檢視觸發程序記錄 

輪詢觸發程序會在某個時間間隔檢查 API，但不一定會開始執行，這視回應而定 (例如 `200` 表示執行，而 `202` 表示不執行)。 觸發程序記錄可讓您查看所有發生但未執行邏輯應用程式的呼叫 (`202` 回應)：  

![觸發程序歷程](./media/app-service-logic-monitor-your-logic-apps/triggerhistory.png)

每個觸發程序，您可以看到如果它 **引發**, ，如果未引發，或是它有某種形式的錯誤 (它 **失敗**)。 若要檢查觸發程序失敗的原因，請選取 **輸出** 連結。 如果它確實已引發，選取 **執行** 連結以查看引發之後發生了什麼事。

請注意，對於 *推送* 執行觸發程序， *不* 執行在這裡開始的時間，請參閱。 而是看到 *回呼註冊* ; 也就是邏輯應用程式註冊至回撥時呼叫。 如果推入觸發程序未運作，則註冊可能會發生問題 (您可以在 [輸出] 上看到)，但您可能需要特別調查該 API。

## 啟用版本控制

還有另一項功能目前無法在 UI 中 （即將推出），但可使用 [REST api](http://go.microsoft.com/fwlink/p/?LinkID=525617&clcid=0x409)。 更新邏輯應用程式的定義時，會儲存舊版的定義。 這是因為如果您已經有進行中的執行，該執行是參考執行啟動時已存在的邏輯應用程式版本。 執行正在進行時，您無法變更執行的定義。 版本記錄 REST API 可讓您存取這項資訊。
 

