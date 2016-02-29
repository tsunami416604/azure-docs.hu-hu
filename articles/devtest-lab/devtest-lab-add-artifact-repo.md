    <properties
    pageTitle="Add a Git artifact repository to your DevTest Lab | Microsoft Azure"
    description="Add a GitHub or Visual Studio Team Services Git repository for your custom artifacts to your lab"
    services="devtest-lab,virtual-machines,visual-studio-online"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/01/2015"
    ms.author="tarcher"/>

# 將 Git 構件儲存機制加入研發/測試實驗室

## 概觀

根據預設，研發/測試實驗室包含來自官方 Azure 研發/測試實驗室構件儲存機制的構件。 您可以將 Git 構件儲存機制加入您的實驗室，以包含小組建立的構件 。 儲存機制可以裝載在 [GitHub](https://github.com) 或上 [Visual Studio 小組服務 」 (VSTS)](https://visualstudio.com)。

- 若要了解如何建立 GitHub 存放庫，請參閱 [GitHub Bootcamp](https://help.github.com/categories/bootcamp/)。
- 若要了解如何建立小組服務專案，而就可以給儲存機制，請參閱 [連線到 Visual Studio 的小組服務](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)。

下列螢幕擷取畫面顯示在 GitHub 中包含成品的儲存機制的可能外觀的範例:  
![GitHub 構件儲存機制範例](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## 將 GitHub 構件儲存機制加入您的實驗室

若要將 GitHub 構件儲存機制加入您的實驗室，您必須先從構件儲存機制取得 HTTPS 複製 URL 和個人存取權杖，然後將該資訊輸入您的實驗室

### 取得 GitHub 儲存機制複製 URL 和個人存取權杖

1. 在 GitHub 存放庫，其中包含 team 成品首頁上，儲存 **HTTPS 複製 url** 以供日後使用。

1. 點選 [設定檔影像在右上角，然後選取 **設定**。

1. 在 **的個人設定** 上左點選功能表 **個人的存取權杖**。

1. 請點一下 [ **產生新的語彙基元**。

1. 上 **新的個人存取語彙基元** 頁上，輸入 **語彙基元描述**, ，接受預設項目，在 **選取範圍**, ，然後選擇 [ **產生語彙基元**。

1. 儲存產生的權杖，因為您稍後需要用到。

1. 您現在可以關閉 GitHub。   

###將您的實驗室連接至 GitHub 儲存機制

1. 若要登入 [Azure 預覽入口網站](http://portal.azure.com)。

1. 請點一下 [ **瀏覽**, ，然後點選 [ **DevTest 實驗室** 清單中。

1. 從實驗室清單中，點選所需的實驗室。   

1. 在測試環境的防禦，點選 [ **設定**。

1. 在測試環境的 **設定** 刀，點選 **成品儲存機制**。

1. 在 **成品儲存機制** 刀:

    1. 輸入 **名稱** 的儲存機制。
    1. 輸入已儲存 **就可以給複製 Url**。
    2. 輸入 **資料夾路徑** 成品儲存機制包含成品中。
    3. 輸入已儲存 **個人存取語彙基元** 成品儲存機制。
    4. 請點一下 [ **儲存**。

在您的存放庫中的成品現在會列在 **新增成品** 刀。

## 將 Visual Studio Git 構件儲存機制加入您的實驗室

若要將 Visual Studio Git 構件儲存機制加入您的實驗室，您必須先從構件儲存機制取得 HTTPS 複製 URL 和個人存取權杖，然後將該資訊輸入您的實驗室。

### 在構件專案的 Visual Studio 網頁上

1. 開啟您的小組集合首頁 (例如 `https://contoso-web-team.visualstudio.com`)，然後點選構件專案。

2. 在 [專案] 首頁上點選 [ **程式碼**。

1. 若要檢視 [專案中的 [複製 URL， **程式碼** 頁面上，請點一下 [ **複製品**。

1. 儲存 URL，因為您稍後在本教學課程中將需要此資訊。

1. 若要建立 「 個人存取權杖，請點選 **我的設定檔** 由使用者帳戶的下拉式選單] 功能表。

1. 在設定檔資訊頁面交流 **安全性**。

1. 在 **安全性** 索引標籤上，請點一下 [ **新增**。

1. 在 **建立個人的存取語彙基元** 頁面:

    1. 輸入 **描述** 語彙基元。
    2. 選取 [ **180 天** 從 **[到期日** 清單。
    3. 選擇 **所有可存取的帳戶** 從 **帳戶** 清單。
    4. 選擇 **所有領域** 選項。
    5. 選擇 **建立語彙基元**。

1. 當完成時，新的語彙基元會出現在 **個人的存取權杖** 清單。 請點一下 [ **複製語彙基元** ，然後儲存語彙基元的值，因為它會短時間內使用。

### 在研發/測試實驗室中

1. 在測試環境的刀，點選 **設定**。

    ![選擇設定](./media/devtest-lab-add-artifact-repo/devtestlab-add-artifacts-repo-open-dtl-settings.png)

1. 在 **設定** 刀，點選 **成品儲存機制**。

1. 在 **成品儲存機制** 刀

    1. 輸入顯示 **名稱** 的儲存機制。
    1. 輸入已儲存 **就可以給複製 Url**。
    2. 輸入 **資料夾路徑** 成品儲存機制包含成品中。
    3. 輸入已儲存 **個人存取語彙基元** 成品儲存機制。
    4. 請點一下 [ **儲存**。

