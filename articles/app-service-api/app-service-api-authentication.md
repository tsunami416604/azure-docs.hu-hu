<properties
    pageTitle="Azure App Service 中的 API Apps 驗證與授權 | Microsoft Azure"
    description="了解 Azure App Service 針對 API Apps 所提供的驗證和授權服務。"
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/04/2015"
    ms.author="tdykstra"/>

# Azure App Service 中的 API Apps 驗證與授權

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概觀 

本文說明 Azure App Service 中 API Apps 的驗證和授權處理選項。 

下圖說明 App Service 驗證的幾個重要特性：

* 它會前置處理傳入的 API 要求，提供您幾個選項讓您決定要在自有程式碼中進行多少驗證工作。 
* 它支援五個驗證提供者 ︰ Azure Active Directory、 Facebook、 Google、 Twitter 和 Microsoft 帳戶。
* 它適用於使用者與服務主體的驗證。 
* 它在 API Apps、Web Apps 和 Mobile Apps 的作用都相同。

![](./media/app-service-api-authentication/api-apps-overview.png)

## 前置處理傳入的要求

App Service 可以防止匿名 HTTP 要求進入 API 應用程式，或在具有權杖的要求進入 API 應用程式前先對它們進行驗證。 您可以對 API 應用程式設定下列三個選項的其中一個：

1. 只允許通過驗證的要求進入 API 應用程式。

    如果 App Service 收到來自瀏覽器的匿名要求，便會將其重新導向至登入頁面。 

    如果您事先知道所要使用的驗證提供者 (Google、Twitter 等) 便適用此動作，您可以設定 App Service 來為您處理登入程序。  或者，您也可以指定 App Service 將匿名要求重新導向至自有 URL。 然後您就可以讓使用者選擇驗證提供者。

    使用此選項時，您不需要在應用程式中撰寫任何驗證程式碼，並且因為 HTTP 標頭中已提供最重要的宣告，所以授權程序會變得相當簡單。

2. 允許所有要求進入 API 應用程式，但只讓通過驗證的要求生效，並在 HTTP 標頭中傳遞驗證資訊。

    此選項可讓您更彈性地處理匿名要求，並輕鬆地撰寫需要存取最常見宣告的程式碼。 不同於選項 1，如果您想要防止匿名使用者使用 API，則必須撰寫程式碼。 

3. 允許所有要求進入 API，不對要求中的驗證資訊採取任何動作。

    這個選項會將驗證和授權工作全部交由應用程式程式碼來處理。

在 [Azure 入口網站](https://portal.azure.com/), ，選取您要的選項 **驗證 / 授權** 刀鋒視窗。

![](./media/app-service-api-authentication/authblade.png)

使用選項 1 和 2，請開啟 **應用程式服務驗證**, ，然後在 **時要採取的動作要求的驗證不** 下拉式清單中，選擇 **登入** 或 **允許要求 （無動作）**。  如果您選擇 **登入**, ，您必須選擇驗證提供者，並設定該提供者。

![](./media/app-service-api-authentication/actiontotake.png)
 
## 不限語言

App Service 驗證處理程序是在要求進入 API 應用程式之前進行，這表示不管 API 應用程式是以任何語言或架構所撰寫，都能適用驗證功能。  您可以根據 ASP.NET、Java、Node.js 或任何 App Service 所支援的架構建立 API。

App Service 會在 HTTP 要求的授權標頭中傳遞 JWT 權杖，以任何語言或架構撰寫的程式碼都可以從權杖中取得所需的資訊。 此外，App Service 會透過設定某些特殊標頭 (如下所示)，讓您更容易地存取最常使用的宣告：

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
在 .NET API 中，您可以使用 `Authorize` 屬性，而且如果您需要更精細的授權，也能輕易地根據宣告來撰寫程式碼，因為它已為您在 .NET 類別中填入宣告資訊。

## <a id="internal"></a> 服務帳戶驗證

您也可以在諸如從某個 API 應用程式呼叫另一個 API 應用程式的內部案例中，使用 App Service 驗證。 在此案例中，您可以使用服務帳戶認證 (而非使用者認證) 來進行驗證。 服務帳戶也就是 *服務主體* 中 Azure Active Directory，以及驗證使用這類帳戶亦稱為服務對服務案例。 

在服務對服務案例中，您可以使用 Azure Active Directory 保護所呼叫的 API 應用程式，並在呼叫 API 應用程式時提供 AAD 服務主體授權權杖。 透過提供用戶端識別碼和用戶端密碼，您就可以向 AAD 應用程式要求此權杖。 不需要特殊的僅 Azure 適用的程式碼，例如在處理行動服務 Zumo 權杖時為 true。 本教學課程所涵蓋的此案例中使用 ASP.NET API 應用程式的範例 [API 應用程式的服務主體驗證](app-service-api-dotnet-service-principal-auth.md)。

如果您想要處理服務對服務案例，但不要使用 App Service 驗證，請使用用戶端憑證或基本驗證。 在 Azure 中的用戶端憑證的相關資訊，請參閱 [方式來設定 TLS 相互驗證的 Web 應用程式](../app-service-web/app-service-web-configure-tls-mutual-auth.md)。 如需如何在 ASP.NET 中設定基本驗證資訊，請參閱 [ASP.NET Web API 2 中的驗證篩選](http://www.asp.net/web-api/overview/security/authentication-filters)。

從應用程式服務邏輯應用程式至 API 應用程式的服務帳戶驗證是特殊案例所說明 [使用您的自訂 API 與邏輯應用程式的應用程式服務上主控](../app-service-logic/app-service-logic-custom-hosted-api.md)。

## 詳細資訊

如需 Azure App Service 中的驗證和授權服務的詳細資訊，請參閱 [擴充應用程式服務驗證 / 授權](/blog/announcing-app-service-authentication-authorization/)。

## 後續步驟

本文說明了 App Service API 應用程式的驗證和授權功能。 

如果您要遵照入門教學課程系列適用於 ASP.NET 和 API 應用程式，這些功能會在下一個教學課程中，試用 [應用程式服務 API 應用程式中的使用者驗證](app-service-api-dotnet-user-principal-auth.md)。

如需使用 Azure App Service 中的節點和 Java 的詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/) 和 [Java 開發人員中心](/develop/java/)。


