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

<p>本主題顯示如何在 Azure 行動服務中從 Xamarin.Android 應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。</p>

本教學課程將引導您逐步完成以下在您的應用程式中啟用驗證的基本步驟：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [限制只有經驗證的使用者具有資料表的權限]
3. [新增驗證至應用程式]

本教學課程是以行動服務快速入門為基礎。 您也必須先完成 [開始使用行動服務] 教學課程。

完成本教學課程需要有 Xamarin.Android 和 Android SDK 4.2 或更新版本。

## 

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

## 

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. 

4. 從 [執行]**** 功能表，按一下 [開始偵錯]**** 來啟動應用程式。確認應用程式啟動之後會引發無法處理的例外狀況，狀態碼為 401 (未授權)。

     這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 _TodoItem_ 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

## 

1. 將下列屬性新增至 **ToDoActivity** 類別：

        private MobileServiceUser user;

2. 將下列方法加入至 **ToDoActivity** 類別：

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
    > [AZURE.NOTE] 如果您使用的身分識別提供者不是 Microsoft，請將傳給上述 **login** 方法的值變更為下列其中一個：_Facebook_、_Google_、_Twitter_ 或 _WindowsAzureActiveDirectory_。

3. 在 **OnCreate** 方法中，在具現化 `MobileServiceClient` 物件的程式碼後面加入下列這一行程式碼。

        await Authenticate();

    此呼叫會啟動驗證程序再非同步等候它。

4. 將 `OnCreate` 方法中的 **await Authenticate();** 後面的其餘程式碼移至新的 **CreateTable** 方法，如下所示：

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

5. 接著，在 **OnCreate** 中，就在步驟 2 新增的 **Authenticate** 呼叫後，呼叫新的 **CreateTable** 方法：

        await CreateTable();

6. 從 [執行]**** 功能表，按一下 [開始偵錯]**** 來啟動應用程式，並以您選擇的身分識別提供者登入。

    成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

## 取得完成的範例

務必以您自己的 Azure 設定更新 **applicationURL** 和 **applicationKey** 變數。

## 







[register your app for authentication and configure mobile services]: #register 
[restrict table permissions to authenticated users]: #permissions 
[add authentication to the app]: #add-authentication 
[next steps]: #next-steps 
[4]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-services-selection.png 
[5]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-service-uri.png 
[13]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-identity-tab.png 
[14]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-data-tables.png 
[15]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-change-table-perms.png 
[get started with mobile services]: partner-xamarin-mobile-services-android-get-started.md 
[authorize users with scripts]: mobile-services-javascript-backend-service-side-authorization.md 
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331328 

