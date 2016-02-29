<properties
    pageTitle="建立邏輯應用程式 | Microsoft Azure"
    description="了解如何建立連接 SaaS 服務的邏輯應用程式"
    authors="stepsic-microsoft-com"
    manager="dwrede"
    editor=""
    services="app-service\logic"
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/21/2015"
    ms.author="stepsic"/>

# 建立連接 SaaS 服務的新邏輯應用程式

| 快速參考 |
| --------------- |
| [Logic Apps 定義語言](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [Logic Apps 連接器文件](https://azure.microsoft.com/documentation/articles/app-service-logic-connectors-list/) |
| [Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) |

本主題示範如何在幾分鐘，您可以開始使用 [應用程式服務邏輯應用程式](app-service-logic-what-are-logic-apps.md)。 我們將逐步解說可將一組您感興趣的推文傳送到 Dropbox 資料夾的工作流程。

若要使用此案例，您需要：

- Azure 訂用帳戶
- Twitter 帳戶
- Dropbox 帳戶

<!--- TODO: Add try it now information here -->

## 取得連接器

首先，您必須建立兩個連接器，您將使用: [Dropbox 連接器](app-service-logic-connector-dropbox.md) 和 [Twitter 連接器](app-service-logic-connector-twitter.md)。 由於 Twitter API 的限制，我們也需要向 Twitter 註冊免費應用程式。 若要建立這些項目：

1. 登入 Azure 入口網站。

2. 按一下 [  [Marketplace ](https://portal.azure.com/#blade/HubsExtension/GalleryFeaturedMenuItemBlade/selectedMenuItemId/apiapps) 主畫面並搜尋 Twitter 的 (或 [按一下這裡](https://portal.azure.com/#create/microsoft_com.TwitterConnector.0.2.2))。

3. 選取 **Twitter 連接器** 按一下 **建立**。 您會看到一個包含所有設定的檢視。 您可以保留做為名稱 **Twitter 連接器**。  
4. 選取 **封裝設定**-此時，您將需要輸入從 Twitter 應用程式的資訊。  您可以使用下列步驟設定免費的應用程式：
    1. 移至 [Twitter 應用程式註冊頁面](http://apps.twitter.com)。
    2. 建立新的應用程式。
    3. 提供名稱和描述。  您可以輸入網站的任何 URL，以及回呼 URL 的任何 URL (不要留白)。
    4. 註冊之後，複製 **消費者金鑰** 從 Twitter 到 **clientId** 在 Azure 中，欄位和 **消費者密鑰** 從 Twitter 到 **clientSecret。**
    5. 按一下 [ **確定** 回到 [API 設定 Azure 的窗格中。

5. 輸入方案名稱在 **建立新的應用程式服務方案**。

    >[AZURE.NOTE]本節中的步驟假設您正在建立新的應用程式服務方案。 如果您使用現有的應用程式服務方案，按一下 **選取現有**, 選取現有的計劃，然後跳至最後這一節中的步驟。 您需要一個方案來主控您的所有應用程式。

6.  選取 **定價層** 新方案。

    >[AZURE.NOTE]根據預設，會顯示建議邏輯應用程式的方案。 按一下 [ **檢視所有** 來查看所有可用的方案。 當您在免費層次中執行邏輯應用程式時，您只能每小時執行，並每個月最多使用 1000 個動作。

7. 建立 **資源群組** 流程。

    資源群組會當做您應用程式的容器使用。 所有的應用程式資源會存在於相同的資源群組。

8. 如果您有多個 Azure 訂閱，請選擇您即將使用的訂閱。

9. 選擇 **位置** 執行邏輯應用程式。

    ![建立 API 應用程式檢視](./media/app-service-logic-create-a-logic-app/gallery.png)

10. 按一下 [ **建立**。 佈建步驟可能需要一兩分鐘的時間。

11. 現在重複此程序 [Dropbox](https://portal.azure.com/#create/microsoft_com.DropboxConnector.0.2.2)。

## 啟動邏輯應用程式

現在，您需要建立新的邏輯應用程式：

1. 按一下 [ **+ 新增** 在左下角的畫面，展開 **Web + 行動**, ，然後按一下 [ **邏輯應用程式**。

    這會顯示 [建立邏輯應用程式] 檢視，您可以在這裡提供一些基本設定當做開始。

    ![建立邏輯應用程式檢視](./media/app-service-logic-create-a-logic-app/createlogicapp.png)

2. 在 **名稱**, ，輸入有意義的名稱，邏輯應用程式。

3. 選擇 **應用程式服務方案** 您建立的連接器時使用。 這會自動為您選擇位置、訂用帳戶和資源群組。

此程式碼的基本設定，但不要按 **建立** 還。 接下來，您會將觸發程序和動作加入工作流程。

## 加入觸發程序

觸發程序是可讓您的邏輯應用程式執行的項目。 接下來，您將新增一個週期性觸發程序，它會在預先定義的排程上啟動您的工作流程。

1. 仍在 **建立邏輯應用程式** 檢視中，按一下 **觸發程序和動作**。

    這會顯示全螢幕設計工具，其中顯示您的流程，以及一些可從中開始的範本。
    
2. 本教學課程中我們 **從頭建立**。  如果範本很實用，請一律加以使用。
    
    現在右手邊是可能包含觸發程序的所有服務清單。

3. 在頂端區段中，按一下 [ **循環**。

    這會新增一個您可以在其中指定週期性設定的方塊。

    ![週期性](./media/app-service-logic-create-a-logic-app/recurrence.png)

4.  選擇週期性 **頻率** 和 **間隔** (例如每 1 小時)，然後按一下綠色的核取記號。

現在，您將把動作加入流程。

## 加入 Twitter 動作

動作是工作流程的執行項目。 您可以擁有任意數目的動作，而且您可以將它們分組，以便將資訊從一個動作傳遞到下一個動作。

1. 在右窗格中，按一下 [ **Twitter 連接器**。

2. 它會載入之後，請按一下 **授權**, 、 登入您的 Twitter 帳戶，並按一下 [ **授權應用程式**。

    這會授與連接器存取您 Twitter 帳戶的權限。 Twitter 連接器所提供的可能作業清單便會隨即顯示。

    ![動作](./media/app-service-logic-create-a-logic-app/actions.png)

    > [AZURE.NOTE]  **授權** 按鈕用來連線到 SaaS 服務，例如 Twitter 的 OAUTH 安全性。 詳細說明在 OAUTH [OAUTH 安全性](app-service-logic-oauth-security.md)。

3. 按一下 [ **搜尋推文**, ，接著在 **指定查詢**, ，輸入類似 `#MicrosoftAzure` 按一下綠色的核取記號。

    ![Twitter 搜尋](./media/app-service-logic-create-a-logic-app/twittersearch.png)

Twitter 連接器現在便是工作流程的一部分。

## 加入 Dropbox 動作並建立應用程式

最後一個步驟是加入將推文上傳至 Dropbox 檔案的動作。

1. 在右窗格中，按一下 [ **Dropbox 連接器**。

2. 佈建完成後，按一下 [ **授權**, ，登入您的 Dropbox 帳戶和 **允許**。

    ![授權 Dropbox 連接器](./media/app-service-logic-create-a-logic-app/authorize.png)

    這會授與連接器存取您 Dropbox 帳戶的權限。 Dropbox 連接器所提供的可能作業清單便會隨即顯示。

4. 按一下 [ **上傳檔案**。  

    這會顯示 Dropbox 連接器設定，您必須有了這些設定後才能將資料從 Twitter 搜尋傳遞至 Dropbox。

    ![Dropbox](./media/app-service-logic-create-a-logic-app/dropbox.png)

3. 在 **FilePath** 欄位中，輸入 `/tweet.txt`

4. 在 **內容** 欄位中，按一下 `...` 按鈕，然後按一下 **推文文字** 選項。

    這會將值 `@first(body('twitterconnector')).TweetText` 輸入文字方塊中。 這個產生的值包含下列部分：

    內容部分                               | 說明
    ------------------------------------------ | ------------
     `@`                                       | 指出您正在輸入函數，而不是實際值。
    `actions('twitterconnector').outputs.body` | 取得 Twitter 連接器查詢所傳回的推文。
    `first()`                                  | 搜尋推文動作會傳回一份清單，但您只想要上傳一個檔案
    `.TweetText`                               | 選取推文訊息屬性。

5. 按一下綠色核取符號來儲存連接器設定。

5. 現在設計已完成，請按一下 **程式碼檢視** 左上角的設計工具中，並請注意，這是 JSON 程式碼定義的工作流程，您只是建立在設計工具中。 我們將討論此主題中的 [下一步] 更多的程式碼 [使用邏輯應用程式功能]。

6. 按一下 [ **確定** 按鈕位於螢幕底部，然後按一下 [ **建立** ] 按鈕。

    如此即會建立新的邏輯應用程式。

## 在建立後管理邏輯應用程式

現在，您的邏輯應用程式已啟動並執行。 每次排定的工作流程執行時，它會檢查與特定的雜湊標記推文。 當它找到符合的推文時，它就會將該推文放入 Dropbox。 最後，您將看到如何停用應用程式，或看到它的執行狀況。

1. 按一下 [ **瀏覽** 螢幕，然後選取左側 **邏輯應用程式**。

2. 按一下您剛剛建立的新邏輯應用程式，以查看目前狀態和一般資訊。

3. 若要編輯新的邏輯應用程式，請按一下 [ **觸發程序和動作**。

5. 若要關閉應用程式，請按一下 [ **停用** 命令列中。

不到 5 分鐘的時間，您便能夠設定在雲端中執行的簡單邏輯應用程式。 若要深入了解如何使用邏輯應用程式功能，請參閱 [使用邏輯應用程式功能]。 若要了解邏輯應用程式定義本身，請參閱 [撰寫邏輯應用程式定義](app-service-logic-author-definitions.md)。

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Use logic app features]: app-service-logic-use-logic-app-features.md

