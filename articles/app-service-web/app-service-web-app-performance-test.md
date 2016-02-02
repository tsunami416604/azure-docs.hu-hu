<properties
   pageTitle="測試 Azure Web 應用程式的效能 |Microsoft Azure"
   description="執行 Azure Web 應用程式效能測試，檢查您的應用程式如何處理使用者負載。測量回應時間及尋找可能表示問題的失敗狀況。"
   services="app-service\web"
   documentationCenter=""
   authors="ecfan"
   manager="douge"
   editor="jimbe"/>

<tags
   ms.service="app-service-web"
   ms.workload="web"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/11/2015"
   ms.author="estfan; manasma"/>


# 測試 Azure Web 應用程式在低負載下的效能

在您啟動 Web 應用程式或將更新部署至生產環境之前，請先檢查該應用程式的效能。 
如此一來，您即可更妥善地評估您的應用程式是否已準備好發行。 感覺更多
確認在尖峰使用期間或在下一步行銷推播您的應用程式可以處理的流量。

在公開預覽期間，您可以在 Azure 入口網站中免費測試您的應用程式的效能。
這些測試會模擬您的應用程式在特定期間內的使用者負載並測量您的應用程式的回應。 例如，您的測試結果顯示您的應用程式指定的數字的回應速度 
使用者。 也會顯示多少要求失敗，這可能表示您的應用程式有問題。

![尋找您的 Web 應用程式中的效能問題][testoverview]

## 開始之前

*   您將需要 [Azure 訂用帳戶 ][azuresubscription],， 
如果您還沒有。 了解如何 
[申請 Azure 帳戶的免費 ][azurefreetrial]。

*   您將需要 [Visual Studio Team Services (VSTS) ][whatisvsts] 
若要保留您的效能測試記錄的帳戶。 
建立新的帳戶，當您設定您的效能測試 
或使用現有的帳戶，如果您的帳戶擁有者。 
[還有什麼做與 Visual Studio Team Services 帳戶?](#VSTSAccount)

*   部署您的應用程式以便在非生產環境中進行測試。 
讓您的應用程式使用生產環境中所用方案以外的 App Service 方案。 
這樣一來，您不會影響任何現有的客戶或讓您的應用程式在生產環境中變慢。

## 設定和執行效能測試

0.  登入 [Azure 入口網站 ][azureportal]。 
若要使用您所擁有的 Visual Studio Team Services 帳戶 
帳戶擁有者身分登入。

0.  移至您的 Web 應用程式。

    ![請移至 ][webapp]

0.  移至 [效能測試]****。

    ![移至 ][expandedtools]

0.  現在您將連結 [Visual Studio Team Services (VSTS) ][whatisvsts] 
若要保留您的效能測試記錄的帳戶。

    如果您已經有 VSTS 帳戶，請選取該帳戶。 如果沒有，請建立新帳戶。

    ![選取現有的 VSTS 帳戶，或建立新的帳戶][existingnewvstsaccount]

0.  建立您的效能測試。 設定詳細資料並執行測試。 
您可以在測試執行時即時觀看結果。

    例如，假設我們有在去年節日特賣時發放優待券的應用程式。 
    這個活動持續了 15 分鐘，尖峰負載為 100 位並行客戶。 
    我們希望今年的客戶數變成兩倍。 我們也想要改善 
    藉由減少頁面的客戶滿意度載入從 5 秒的時間為 2 秒。 
    因此，我們將以 250 名使用者測試更新後應用程式的效能達 15 分鐘之久。

    我們將我們的應用程式上模擬負載，藉由產生虛擬使用者 (客戶) 
    使用者一次造訪我們的網站。 此命令會顯示我們多少 
    要求已經失敗或回應速度較慢。

    ![設定、設定和執行效能測試][newtest]

     *  系統會自動加入您的 Web 應用程式的預設 URL。 
     您可以變更此 URL 以測試其他頁面 (僅限 HTTP GET 要求)。

     *  若要模擬本機的情況，並減少延遲， 
     選取最靠近使用者的位置產生負載。

    以下是進行中的測試。 在第一次的幾分鐘， 
    我們的頁面載入速度比我們想要的。

    ![使用即時資料的進行中效能測試][testrunning]

    在測試完成之後，我們了解在頁面載入速度 
    之後第一分鐘。 這可協助識別我們可能想要 
    開始進行疑難排解問題。

    ![完成的效能測試會顯示結果，包括失敗的要求][testdone]

歡迎您提供的意見反應。 針對疑問或問題， 
請與我們連絡 <vsoloadtest@microsoft.com>

## 問答集

#### 問：我可持續執行測試的時間是否有所限制？

答：是，您最多可以在 Azure 入口網站中執行一小時的測試。

#### 問：我可執行效能測試的時間有多少？

答: 過後公用預覽版本中，您會收到 20000 虛擬使用者分鐘 (VUMs) 
釋出每個月的 Visual Studio Team Services 帳戶。 
VUM 是數字的 multipled 虛擬使用者數目 
測試中的分鐘數。 如果您的需求超過可用的限制， 
您可以購買更多的時間，並只支付您所使用。

#### 問：哪裡可以檢查到目前為止我已使用多少 VUM？

答：您可以在 Azure 入口網站中檢查此數量。

![移至 VSTS 帳戶][vstsaccount]

![檢查已使用的 VUM][checktesttime]

<a name="VSOAccount"></a>
#### 問：Visual Studio Team Services 帳戶還有什麼用途？

答: 若要尋找您的新帳戶，請移至 `https://{accountname}.visualstudio.com`。 
共用程式碼、 建置、 測試、 追蹤工作和交付軟體 – 全都在雲端 
使用任何工具或語言。 深入了解如何 [Visual Studio Team Services ][whatisvsts] 
功能和服務，幫助您的小組共同作業更容易，並持續部署。




[webapp]: ./media/app-service-web-app-performance-test/azure-np-web-apps.png 
[testoverview]: ./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png 
[expandedtools]: ./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png 
[existingnewvstsaccount]: ./media/app-service-web-app-performance-test/azure-np-no-vso-account.png 
[newtest]: ./media/app-service-web-app-performance-test/azure-np-new-performance-test.png 
[testrunning]: ./media/app-service-web-app-performance-test/azure-np-running-perf-test.png 
[testdone]: ./media/app-service-web-app-performance-test/azure-np-perf-test-done.png 
[vstsaccount]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts.png 
[checktesttime]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png 
[azureportal]: https://portal.azure.com 
[azuresubscription]: https://account.windowsazure.com/subscriptions 
[azurefreetrial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F 
[whatisvsts]: https://www.visualstudio.com/products/what-is-visual-studio-online-vs 

