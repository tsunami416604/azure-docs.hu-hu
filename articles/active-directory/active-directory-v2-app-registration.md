<properties
    pageTitle="應用程式模型 v2.0 | Microsoft Azure"
    description="如何啟用登入及整合應用程式與應用程式模型 v2.0 向 Microsoft 註冊應用程式。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="dastrock"/>

# 應用程式模型 v2.0 預覽版本：如何向 Microsoft 註冊應用程式

若要建置同時接受 MSA 與 Azure AD 登入的應用程式，您必須先向 Microsoft 註冊應用程式。  您將無法使用任何現有的應用程式搭配 Azure AD 或 MSA - 立刻建立一個全新的應用程式。

> [AZURE.NOTE]
    此資訊適用於 v2.0 應用程式模型公開預覽版本。  如需如何公開上市的 Azure AD 整合服務，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

## 請造訪 Microsoft 應用程式註冊入口網站
首要之務先瀏覽至 [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com)。  這是新的應用程式註冊入口網站，可供您管理有關 Microsoft 應用程式的所有一切。

使用個人或公司或學校的 Microsoft 帳戶進行登入。  如果您沒有任何帳戶，請註冊新的個人帳戶。 請繼續進行，這不需要很長的時間 - 我們會在此等候。

完成了嗎？ 您現在應該看一下您的 Microsoft 應用程式清單，該清單有可能空白。  讓我們改變這點。

<!-- TODO: Verify strings here -->
按一下 [ **新增應用程式**, ，並提供它的名稱。  在入口網站將會指派您的應用程式
稍後在程式碼中，您將使用全域唯一應用程式識別碼。  如果您的應用程式包括伺服器端元件，需要存取權杖的呼叫 Api
(認為 ︰ Office、 Azure 或第 3 方)，您會想要建立 * * 應用程式
密碼 * * 這裡。
<!-- TODO: Link for app secrets -->

接下來，加入您的 app 將使用的平台。
- 針對 web 架構應用程式，提供 **重新導向 URI** 傳送登入訊息的位置。
- 針對行動應用程式，複製系統自動為您建立的預設重新導向 URI。

(選擇性) 您可以在 [設定檔] 區段中自訂登入頁面的外觀及操作方式。  請務必按一下 **儲存** 之後再繼續。

## 建置快速入門應用程式
有的 Microsoft 應用程式之後，您可以完成我們的快速入門
若要取得最多和執行應用程式模型 v2.0 的教學課程。  以下是一些
建議 ︰

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]


