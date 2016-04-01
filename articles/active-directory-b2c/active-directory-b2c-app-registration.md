<properties
    pageTitle="Azure Active Directory B2C 預覽：應用程式註冊 | Microsoft Azure"
    description="如何向 Azure Active Directory B2C 註冊您的應用程式"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2015"
    ms.author="swkrish"/>

# Azure Active Directory B2C 預覽：如何註冊您的應用程式

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 必要條件

若要建置可接受取用者註冊與登入的應用程式，您必須先使用 Azure Active Directory B2C 租用戶註冊該應用程式。 取得自己的租用戶使用所述的步驟 [這裡](active-directory-b2c-get-started.md)。 若您遵循該文章中述及的所有步驟，則 B2C 功能刀鋒視窗應會釘選至「開始面板」。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 瀏覽至 B2C 功能刀鋒視窗

您可使用下列兩種方式之一，瀏覽至 B2C 功能刀鋒視窗。

### 1.直接在 Azure Preview 入口網站中瀏覽

如果您有釘選到您的開始面板 B2C 功能刀鋒視窗中，您會看見它只要登入 [Azure 預覽入口網站](https://portal.azure.com/) B2C 租用戶的全域系統管理員身分。

您也可以直接瀏覽至存取 B2C 功能分頁 [https://portal.azure.com/ {tenant}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com](https://portal.azure.com/{tenant}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com) 其中 **{tenant}** 要用在租用戶建立時 (例如，contosob2c) 和 B2C 租用戶的全域系統管理員身分登入名稱所取代。 您可將此連結加入書籤供日後參考 (請參閱下面附註)。

    > [AZURE.IMPORTANT]
    您必須是 B2C 租用戶的全域管理員，才能存取 B2C 功能刀鋒視窗。 其他租用戶的全域管理員或所有租用戶的使用者均無法存取。

### 2.透過 Azure 入口網站存取

登入 [Azure 入口網站](https://manage.windowsazure.com/) 訂用帳戶系統管理員身分 （這是相同的工作或學校帳戶或與您用來註冊 Azure 的 Microsoft 帳戶相同）。 瀏覽至左側的 Active Directory 擴充，然後按一下 B2C 租用戶。 在 **快速入門** ] 索引標籤 （第一個索引標籤隨即開啟），按一下 [ **管理 B2C 設定** 下 **管理**。 此動作將會在新的瀏覽器視窗或索引標籤中，開啟 B2C 功能刀鋒視窗。

您也可以找到 **管理 B2C 設定** 連結 (在 **B2C 管理** 一節) 上 **設定** ] 索引標籤。

## 註冊應用程式

1. 在 Azure 預覽入口網站上的 B2C 功能分頁中，按一下 **應用程式**。
2. 按一下 [ **+ 新增** 刀鋒視窗的頂端。
3.  **名稱** 應用程式將說明您的應用程式取用者。 例如，輸入「Contoso B2C app」。
4. 如果您正在撰寫的 web 應用程式，請開啟 **包括 web 應用程式 / web API** 切換至 **是**。  **回覆 Url** 是 Azure AD B2C 會傳回任何語彙基元應用程式所要求的端點。 例如，輸入 `https://localhost:44321/`。 如果您的應用程式包含必須保護的伺服器端元件 (API)，您會想要建立 （和複製） **應用程式密碼** 也藉由按一下 **產生金鑰** ] 按鈕。

    > [AZURE.NOTE]
    **應用程式密碼** 是重要的安全性認證。

5. 如果您正在撰寫行動應用程式，請開啟 **包含原生用戶端** 切換至 **是**。 複製預設關閉 **重新導向 URI** 為您自動建立。
6. 按一下 [ **建立** 註冊您的應用程式。
7. 按一下您剛才建立的應用程式，並複製全域唯一 **應用程式識別碼** ，您將在稍後的程式碼中使用。

## 建置快速啟動應用程式

您現已在 Azure AD B2C 註冊應用程式，完成其中一個快速啟動教學課程即可開始操作和執行。 以下是一些建議：

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]


