<properties
    pageTitle="Azure Active Directory B2C 預覽︰頁面 UI 自訂協助程式工具 | Microsoft Azure"
    description="用來示範 Azure Active Directory B2C 中頁面 UI 自訂功能的協助程式工具"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="curtand"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2015"
    ms.author="swkrish"/>


# Azure Active Directory B2C 預覽︰用來示範頁面使用者介面 (UI) 自訂功能的協助程式工具

這篇文章是附隨於 [主要 UI 自訂文件](active-directory-b2c-reference-ui-customization.md) Azure Active Directory B2C 中。以下步驟說明如何使用我們提供的 HTML & CSS 內容，練習網頁 UI 自訂功能。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 取得 Azure AD B2C 租用戶

您可以自訂任何項目之前，您必須 [取得 Azure AD B2C 租用戶](active-directory-b2c-get-started.md) 如果沒有的話。

## 建立註冊原則

範例內容，我們提供了自訂中的兩個頁面 [註冊原則](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy): [IDP 選取] 頁面上](active-directory-b2c-reference-ui-customization.md#identity-provider-selection-page) 和 [本機帳戶註冊網頁](active-directory-b2c-reference-ui-customization.md#local-account-sign-up-page)。 當 [建立您的註冊原則](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy), ，新增本機帳戶 (電子郵件地址)、 Facebook 及 Google + 為 **身分識別提供者**。 我們的範例 HTML 內容只接受這些 IDP。

## 註冊應用程式

您必須 [註冊應用程式](active-directory-b2c-app-registration.md) B2C 租用戶可以用來執行您的原則中。 註冊應用程式之後，有幾個選項可實際執行註冊原則：

- 建置一個快速入門應用程式列出 Azure AD B2C [這裡](active-directory-b2c-overview.md#getting-started)。
- 使用預先建置 [Azure AD B2C 遊樂場](https://aadb2cplayground.azurewebsites.net) 應用程式。 如果您選擇使用遊樂場，您必須註冊應用程式中使用租用戶 B2C **重新導向 URI** `https://aadb2cplayground.azurewebsites.net/`
- 使用 **立即執行** 上您的原則中的按鈕 [Azure 預覽入口網站](https://portal.azure.com)。

## 自訂您的原則

若要自訂原則的外觀和風格，您必須先使用 Azure AD B2C 的特定慣例建立 HTML 和 CSS 檔案。 接著可以將靜態內容上載至公開可用的位置，供 Azure AD B2C 存取。 這可以是您自己專屬的 Web 伺服器、Azure Blob 儲存體、Azure CDN，或任何其他的靜態資源裝載提供者。 唯一的條件是您的內容必須可透過 HTTPS 取得，且可使用 CORS 來存取。 一旦將靜態內容公開在網路上，您就可以編輯原則來指向這個位置，讓使用者看到此內容。  [主要 UI 自訂文件](active-directory-b2c-reference-ui-customization.md) 詳細說明 Azure AD B2C 自訂功能的運作方式。

基於本教學課程的目的，我們已經建立一些範例內容並裝載於 Azure Blob 儲存體。 範例內容以虛構的公司 "Contoso B2C" 為例，示範如何以非常基本的方式自訂佈景主題。 若要在您自己的原則中試用一下，請遵循下列步驟：

1. 在登入您的租用戶 [Azure 預覽入口網站](https://portal.azure.com) 和瀏覽至 B2C 功能分頁。
2. 按一下 [註冊原則]****，然後按一下您的註冊原則 (例如 "b2c_1_sign_up")。
3. 按一下 [頁面 UI 自訂]****，然後按一下 [身分識別提供者選取頁面]****。
4. 將 [使用自訂範本]**** 開關切換到 [是]****。 在 **自訂網頁 URI** 欄位中，輸入 `https://contosob2c.blob.core.windows.net/static/Index.html`。 按一下 [確定]****。
5. 按一下 [本機帳戶註冊頁面]****。 將 [使用自訂範本]**** 開關切換到 [是]****。 在 **自訂網頁 URI** 欄位中，輸入 `https://contosob2c.blob.core.windows.net/static/EmailVerification.html`。 按兩次 [確定]****，關閉 UI 自訂刀鋒視窗。
6. 按一下 [儲存]****。

現在可以試試您自訂的原則。 想要的話，您可以使用自己的應用程式或 AAD B2C 遊樂場，也可以直接按一下原則刀鋒視窗中的 [立即執行]**** 命令。 在下拉式清單中選取您的應用程式，以及適當的重新導向 URI。 按一下 [立即執行]**** 按鈕。 新的瀏覽器索引標籤隨即開啟，您可以使用已備妥的新內容，在您的應用程式中體驗一次使用者註冊過程！

## 將範例內容上傳至 Azure Blob 儲存體

如果想要使用 Azure Blob 儲存體來裝載您的網頁內容，您可以建立自己的儲存體帳戶，並使用我們的 B2C 協助程式工具來上傳檔案。

#### 建立儲存體帳戶

1. 登入 [Azure 預覽入口網站](https://portal.azure.com/)。
2. 按一下 [+新增]**** -> [資料 + 儲存體]**** -> [儲存體帳戶]****。 您需要有  Azure 訂用帳戶才能建立 Azure Blob 儲存體帳戶。 您可以註冊免費試用版 [這裡](https://azure.microsoft.com/pricing/free-trial/)。
3. 提供儲存體帳戶的 [名稱]**** (例如 "contoso")，並且為 [定價層]****、[資源群組]**** 和 [訂用帳戶]**** 挑選適當的選項。 確定您已核取 [釘選到「開始面板」]**** 選項。 按一下 [建立]****。
4. 回到「開始面板」，按一下您剛建立的儲存體帳戶。
5. 在 [摘要]**** 區段之下，按一下 [容器]****，然後按一下 [+新增]****。
6. 提供容器的 [名稱]**** (例如 "b2c")，並選取 [Blob]**** 作為 [存取類型]****。 按一下 [確定]****。
7. 您建立的容器將會出現在 [Blob]**** 刀鋒視窗的清單中。 記下 URL 的容器。比方說，看起來應該像 `https://contoso.blob.core.windows.net/b2c`。 關閉 [Blob]**** 刀鋒視窗。
8. 在儲存體帳戶刀鋒視窗中，按一下 [金鑰]****，並記下 [儲存體帳戶名稱]**** 和 [主要存取金鑰]**** 欄位的值。

> [AZURE.NOTE]
    [主要存取金鑰]**** 是重要的安全性認證。

#### 下載協助程式工具和範例檔案

您可以下載 [Azure Blob 儲存體協助程式工具和範例檔案的.zip 為](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) 或從 GitHub 複製:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

此儲存機制包含 `sample_templates\contoso` 目錄，其中包含範例 HTML、 CSS 和映像。 為了讓這些範本參考您自己的 Azure Blob 儲存體帳戶，您必須編輯 HTML 檔案。 開啟 `Index.htnml` 和 `EmailValidation.html` 並取代任何執行個體 `https://localhost` 您自己的容器，您在上述步驟中複製的 url。 必須使用的 HTML 檔案的絕對路徑，因為 HTML 在此情況下，會由 Azure AD 中的網域之下 `https://login.microsoftonline.com`。

#### 上傳範例檔案

在相同的儲存機制，將解壓縮 `B2CAzureStorageClient.zip` 並執行 `B2CAzureStorageClient.exe` 檔案內。 這個程式只是將您指定的目錄中的所有檔案上傳至您的儲存體帳戶，並允許透過 CORS 存取這些檔案。 如果您遵循上述步驟操作，HTML 和 CSS 檔案現在會指向您的儲存體帳戶。 請注意，儲存體帳戶的名稱前面的部分 `windows.net`, ，例如: `contoso`。 您可以確認該內容已上傳正確方法是嘗試存取 `https://{storage-account-name}.blob.core.windows.net/{container-name}/Index.html` 在瀏覽器上。 也使用 [http://test-cors.org/](http://test-cors.org/) 藉此確定內容已啟用 CORS (尋找 XHR 狀態: 200 結果)。

#### 再次自訂您的原則

既然已經將範例內容上載至您自己的儲存體帳戶，您必須編輯註冊原則來參考它。重複步驟，從 [」 來自訂您的原則 」](#customize-your-policy) 節，這次使用您自己的儲存體帳戶 Url。比方說，讓您 `Index.html` 檔案將會 `< url 的-您的容器 > /Index.html`。

現在，您可以使用 [立即執行]**** 按鈕或您自己的應用程式，再次執行您的原則。結果看起來應該幾乎完全相同 - 您在兩個案例中都使用相同的範例 HTML 和 CSS。不過，您的原則現在會參考您自己的 Azure Blob 儲存體執行個體，您可以自由編輯和重新上傳檔案。如需有關自訂 HTML 和 CSS 的詳細資訊，請參閱 [主要 UI 自訂文件](active-directory-b2c-reference-ui-customization.md)。





