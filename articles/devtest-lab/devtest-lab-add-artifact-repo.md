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

# 將 Git 構件儲存機制加入研測實驗室

## 概觀

根據預設，研測實驗室包含來自官方 Azure 研測實驗室構件儲存機制的構件。 您可以將 Git 構件儲存機制加入您的實驗室，以包含小組建立的構件 。 儲存機制可裝載於 [GitHub](https://github.com) 或 [Visual Studio Team Services (VSTS)](https://visualstudio.com)。

- 若要了解如何建立 GitHub 儲存機制，請參閱 [GitHub 有](https://help.github.com/categories/bootcamp/)。
- 若要了解如何使用 Git 儲存機制中建立小組專案，請參閱 [連接到 Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)。

下列螢幕擷取畫面顯示範例包含成品的儲存機制 GitHub 中看起來如下 ︰  
![GitHub 構件儲存機制範例](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## 將 GitHub 構件儲存機制加入您的實驗室

若要將 GitHub 構件儲存機制加入您的實驗室，您必須先從構件儲存機制取得 HTTPS 複製 URL 和個人存取權杖，然後將該資訊輸入您的實驗室

### 取得 GitHub 儲存機制複製 URL 和個人存取權杖

1. 在首頁上的 GitHub 儲存機制，其中包含小組成品，儲存 **HTTPS 複製 url** 供日後使用。

1. 點選 [設定檔中的映像的右上角，然後選取 **設定**。

1. 在 **個人設定** 左點選功能表 **個人的存取權杖**。

1. 點選 **產生新權杖**。

1. 在 **新的個人存取權杖** 頁面上，輸入 **語彙基元描述**, ，接受預設項目中的 **選取範圍**, ，然後選擇 [ **產生語彙基元**。

1. 儲存產生的權杖，因為您稍後需要用到。

1. 您現在可以關閉 GitHub。   

###將您的實驗室連接至 GitHub 儲存機制

1. 登入 [Azure 預覽入口網站](http://portal.azure.com)。

1. 點選 **瀏覽**, ，然後點選 [ **DevTest 實驗室** 從清單中。

1. 從實驗室清單中，點選所需的實驗室。   

1. 在測試環境的刀鋒視窗中，點選 [ **設定**。

1. 在測試環境的 **設定** 刀鋒視窗中，點選 **成品儲存機制**。

1. 在 **成品儲存機制** 刀鋒視窗中 ︰

    1. 輸入 **名稱** 做為儲存機制。
    1. 輸入儲存 **Git 複製 Url**。
    2. 輸入 **資料夾路徑** 包含成品的成品儲存機制中。
    3. 輸入儲存 **個人存取權杖** 成品儲存機制。
    4. 點選 **儲存**。

您的儲存機制中的成品現在列在 **新增成品** 刀鋒視窗。

## 將 Visual Studio Git 構件儲存機制加入您的實驗室

若要將 Visual Studio Git 構件儲存機制加入您的實驗室，您必須先從構件儲存機制取得 HTTPS 複製 URL 和個人存取權杖，然後將該資訊輸入您的實驗室。

### 在構件專案的 Visual Studio 網頁上

1. 開啟您的小組集合首頁 (例如 `https://contoso-web-team.visualstudio.com`)，然後點選構件專案。

2. 在專案的首頁上，點選 **程式碼**。

1. 若要檢視的專案上的複製 URL， **程式碼** 頁面上，點選 **複製**。

1. 儲存 URL，因為您稍後在本教學課程中將需要此資訊。

1. 若要建立個人的存取權杖，請點選 **我的設定檔** 從使用者帳戶下拉式選單。

1. 設定檔資訊] 頁面 tap **安全性**。

1. 在 **安全性** 索引標籤上，點選 **新增**。

1. 在 **建立個人的存取權杖** 頁面 ︰

    1. 輸入 **描述** 語彙基元。
    2. 選取 **180 天** 從 **[到期日** 清單。
    3. 選擇 **可存取的所有帳戶** 從 **帳戶** 清單。
    4. 選擇 **所有領域** 選項。
    5. 選擇 **建立權杖**。

1. 完成時，新的權杖會出現在 **個人的存取權杖** 清單。 點選 **複製語彙基元** 會短時間內使用，然後儲存語彙基元的值。

### 在研測實驗室中

1. 在您的實驗室刀鋒視窗中，點選 **設定**。

    ![選擇設定](./media/devtest-lab-add-artifact-repo/devtestlab-add-artifacts-repo-open-dtl-settings.png)

1. 在 **設定** 刀鋒視窗中，點選 **成品儲存機制**。

1. 在 **成品儲存機制** 刀鋒視窗

    1. 輸入顯示 **名稱** 做為儲存機制。
    1. 輸入儲存 **Git 複製 Url**。
    2. 輸入 **資料夾路徑** 包含成品的成品儲存機制中。
    3. 輸入儲存 **個人存取權杖** 成品儲存機制。
    4. 點選 **儲存**。


