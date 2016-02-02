<properties 
    pageTitle="將驗證加入 Universal Windows 8.1 應用程式 | Azure 行動服務"
    description="了解如何使用行動服務透過眾多識別提供者驗證 Windows 市集應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" 
    services="mobile-services" 
    documentationCenter="windows" 
    authors="ggailey777" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="11/22/2015" 
    ms.author="glenga"/>


# 將驗證加入 Universal Windows 8.1 應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

本主題說明如何從您的通用 Windows 8.1 App 在 Azure 行動服務中驗證使用者。 在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。 由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程會以行動服務快速入門為基礎。 您也必須先完成 [開始使用行動服務] 教學課程。
>[AZURE.NOTE]本教學課程說明如何在 Windows 市集與 Windows Phone 市集 8.1 應用程式中驗證使用者。 Windows Phone 8.0 或 Windows Phone Silverlight 8.1 應用程式中，請參閱這一版的 [開始使用行動服務中驗證](mobile-services-windows-phone-get-started-users.md)。

## <a name="register"></a> 註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a> 限制已驗證的使用者權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]
>[AZURE.NOTE] 當您使用 Visual Studio 工具將您的應用程式連接到行動服務時，此工具會分別針對個別用戶端平台需要產生共兩組 **MobileServiceClient** 定義。 這時您可以順勢將使用 `#if...#endif` 包裝的 **MobileServiceClient** 定義統一至單一未包裝的定義以供這兩個應用程式版本使用，藉此簡化產生的程式碼。 您不需要執行這項操作，如果您在 [Azure 傳統入口網站] 下載快速入門應用程式。

## <a name="add-authentication"></a> 將驗證新增至應用程式

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]

現在，由您信任的識別提供者驗證的任何使用者都能存取 *TodoItem* 資料表。 若要更善加保護使用者專屬的資料，您還必須實作授權。 若要進行，您要取得指定使用者的使用者識別碼，然後用來判斷使用者對於指定資源具備何種層級的存取權。

## <a name="tokens"></a>將授權權杖儲存在用戶端

先前範例所示範的標準登入，在每次應用程式啟動時，皆需要用戶端連絡身分識別提供者和行動服務。 這個方法不只效率不彰，而且如果同時有許多用戶試圖啟用您的應用程式時，還可能遇到使用量相關的問題。 更好的方法就是快取行動服務傳回的驗證權杖，然後嘗試在使用提供者形式登入前先使用此方法。
>[AZURE.NOTE]無論您使用用戶端管理或服務管理驗證，皆可以快取行動服務發行的權杖。 本教學課程使用服務管理驗證。

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>後續步驟

在下一個教學課程中， [的行動服務使用者服務端授權](mobile-services-javascript-backend-service-side-authorization.md), ，您將行動服務根據經驗證的使用者所提供的使用者識別碼值，並用來篩選行動服務所傳回的資料。

## 另請參閱

+ [增強型的使用者功能](http://go.microsoft.com/fwlink/p/?LinkId=506605)<br/>
你可以藉由呼叫您的行動服務身分識別提供者所維護的其他使用者資料 **user.getidentities ()** 伺服器指令碼中的函式。

+ [行動服務.NET 作法概念參考資料]<br/>深入了解如何使用行動服務與.NET 用戶端。





[register your app for authentication and configure mobile services]: #register 
[restrict table permissions to authenticated users]: #permissions 
[add authentication to the app]: #add-authentication 
[store authentication tokens on the client]: #tokens 
[next steps]: #next-steps 
[submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582 
[my applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039 
[live sdk for windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253 
[get started with mobile services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md 
[get started with authentication]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-users.md 
[get started with push notifications]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md 
[authorize users with scripts]: ../mobile-services-windows-store-dotnet-authorize-users-in-scripts.md 
[azure classic portal]: https://manage.windowsazure.com/ 
[mobile services .net how-to conceptual reference]: mobile-services-windows-dotnet-how-to-use-client-library.md 
[register your windows store app package for microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md 

