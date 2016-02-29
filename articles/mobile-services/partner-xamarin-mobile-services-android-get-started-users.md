<properties
    pageTitle="開始使用驗證 (Xamarin.Android) - 行動服務"
    description="了解如何在 Xamarin.Android 的 Azure 行動服務應用程式中使用驗證。"
    services="mobile-services"
    documentationCenter="xamarin"
    manager="dwrede"
    authors="lindydonna"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/30/2015" 
    ms.author="donnam"/>

# 在您的行動服務應用程式中新增驗證

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

<p>本主題顯示如何在 Azure 行動服務中從 Xamarin.Android 應用程式驗證使用者。 在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。 由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。</p>

本教學課程將引導您逐步完成以下在您的應用程式中啟用驗證的基本步驟：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [限制只有經驗證的使用者具有資料表的權限]
3. [新增驗證至應用程式]

本教學課程是以行動服務快速入門為基礎。 您也必須先完成 [開始使用行動服務] 教學課程。

完成本教學課程需要有 Xamarin.Android 和 Android SDK 4.2 或更新版本。

##<a name="register"></a>註冊您的應用程式以驗證與設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. 在 Xamarin Studio 中，開啟您完成教學課程時所建立的專案 [開始使用行動服務]。

4. 從 **執行** ] 功能表上，按一下 [ **開始偵錯** 來啟動應用程式; 確認應用程式啟動後，會引發未處理的例外狀況，狀態碼 401 (未經授權)。

     這是因為應用程式會嘗試驗證的使用者身分存取行動服務，但 _TodoItem_ 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

1. 新增下列屬性至 **ToDoActivity** 類別:

        private MobileServiceUser user;

2. 將下列方法加入 **ToDoActivity** 類別:

        private async Task Authenticate()
        {
            try
            {
                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.MicrosoftAccount);
                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
            }
            catch (Exception ex)
            {
                CreateAndShowDialog(ex, "Authentication failed");
            }
        }

    這會建立新的方法來處理驗證程序。 使用者透過 Microsoft 帳戶登入來驗證。 將出現對話方塊來顯示已驗證的使用者的識別碼。 必須通過驗證才能繼續。

    > [AZURE.NOTE] 如果您使用 Microsoft 以外的識別提供者，變更值傳遞至 **登入** 上述方法有下列幾種: _Facebook_, ，_Google_, ，_Twitter_, ，或 _WindowsAzureActiveDirectory_。

3. 在 **OnCreate** 方法，會具現化的程式碼後面加入下列程式碼行 `MobileServiceClient` 物件。

        await Authenticate();

    此呼叫會啟動驗證程序再非同步等候它。

4. 移動之後的其餘程式碼 `await Authenticate();` 中 **OnCreate** 至新 **CreateTable** 方法，如下:

        private async Task CreateTable()
        {

            await InitLocalStoreAsync();

            // Get the Mobile Service Table instance to use
            toDoTable = client.GetSyncTable<ToDoItem>();

            textNewToDo = FindViewById<EditText>(Resource.Id.textNewToDo);

            // Create an adapter to bind the items with the view
            adapter = new ToDoItemAdapter(this, Resource.Layout.Row_List_To_Do);
            var listViewTodo = FindViewById<ListView>(Resource.Id.listViewToDo);
            listViewTodo.Adapter = adapter;

            // Load the items from the Mobile Service
            await RefreshItemsFromTableAsync();
        }

5. 接著，呼叫新 **CreateTable** 方法中的 **OnCreate** 之後 **驗證** 加入在步驟 2 中的呼叫:

        await CreateTable();


6. 從 **執行** ] 功能表上，按一下 [ **開始偵錯** 來啟動應用程式，並以您選擇的身分識別提供者登入。

    成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

## 取得完成的範例
下載 [已完成的範例專案]。 請務必更新 **applicationURL** 和 **applicationKey** 變數，以您自己的 Azure 設定。

## <a name="next-steps"></a>後續步驟

在下一個教學課程中，[以指令碼授權使用者，] 所採取的使用者 ID 值行動服務根據經驗證的使用者所提供，並使用它來篩選行動服務所傳回的資料。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-service-uri.png

[13]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Get started with Mobile Services]: partner-xamarin-mobile-services-android-get-started.md
[Authorize users with scripts]: mobile-services-javascript-backend-service-side-authorization.md
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331328

