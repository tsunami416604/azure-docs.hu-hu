<properties 
    pageTitle="如何為您的應用程式服務應用程式設定 Azure Active Directory 驗證" 
    description="了解如何為您的應用程式服務應用程式設定 Azure Active Directory 驗證。" 
    authors="mattchenderson" 
    services="app-service\mobile" 
    documentationCenter="" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-mobile" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="11/20/2015" 
    ms.author="mahender"/>

# 如何設定 App Service 應用程式使用 Azure Active Directory 登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]
&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何設定 Azure 應用程式服務，以使用 Azure Active Directory 做為驗證提供者。 

> [AZURE.NOTE] 本主題示範如何使用的應用程式服務驗證 / 授權功能。 這會取代大部分應用程式的 App Service 閘道器。 如果使用閘道，請參閱 [alternative method]。 該小節中使用閘道器所產生的差異都列在注意事項中。


## <a name="express"> </a>設定 Azure Active Directory 使用快速設定

13. 在 [Azure portal], ，瀏覽至您的應用程式。 按一下 [ **設定**, ，然後 **驗證/授權**。

14. 如果驗證 / 授權功能未啟用，切換到 **上**。

15. 按一下 [ **Azure Active Directory**, ，然後按一下 [ **Express** 下 **管理模式**。

16. 按一下 [ **確定** Azure Active Directory 中註冊應用程式。 這樣會建立新的註冊。 如果您想要改為選擇現有的註冊，請按一下 **選取現有的應用程式** ，然後搜尋先前已建立您的租用戶中註冊的名稱。 按一下以選取它，然後按一下 [註冊 **確定**。 然後按一下 [ **確定** Azure Active Directory 設定] 刀鋒視窗上。

    ![][0]
    
    App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。 您必須在應用程式程式碼中授權使用者。 

17. (選擇性)若要限制存取您的站台由 Azure Active Directory 驗證的使用者，請設定 **不會驗證要求時要採取的動作** 至 **Azure Active Directory**。 這會要求所有要求都需經過驗證，且所有未經驗證的要求都會重新導向至 Azure Active Directory 以進行驗證。

17. 按一下 [ **儲存**。 

現在，您已可在應用程式中使用 Azure Active Directory 進行驗證。

## <a name="advanced"> </a>(替代方法)手動設定 Azure Active Directory 使用進階設定
您也可以選擇手動提供組態設定。 如果您想使用的 AAD 租用戶不同於您登入 Azure 所用的租用戶，這會是較佳的解決方案。 若要完成組態，您必須先在 Azure Active Directory 中建立註冊，接著必須提供一些註冊詳細資料給 App Service。

### <a name="register"> </a>向 Azure Active Directory 註冊您的應用程式

1. 登入 [Azure portal], ，並瀏覽至您的應用程式。 複製您 **URL**。 您將使用此資訊設定 Azure Active Directory 應用程式。

3. 登入 [Azure classic portal] 並瀏覽至 **Active Directory**。

    ![][2] 

4. 選取您的目錄，然後選取 **應用程式** 頂端的索引標籤。 按一下 [ **新增** 底部建立新的應用程式註冊。 

5. 按一下 [ **加入我的組織正在開發的應用程式**。

6. 在 [新增應用程式精靈] 中，輸入 **名稱** 您的應用程式，然後按一下 [  **Web 應用程式和/或 Web API** 型別。 接著，按一下以繼續。

7. 在 **登入 URL** 方塊中，貼上您先前複製的應用程式 URL。 輸入該相同的 URL 中 **應用程式識別碼 URI** 方塊。 接著，按一下以繼續。

8. 按一下 [新增應用程式之後, **設定** ] 索引標籤。 編輯 **回覆 URL** 下 **單一登入** 要加上路徑，您的應用程式的 URL _/.auth/login/aad/callback_。 例如，`https://contoso.azurewebsites.net/.auth/login/aad/callback`。 請確實使用 HTTPS 配置。

    ![][3]
    
    
    > [AZURE.NOTE]
    If you are using the App Service Gateway instead of the App Service Authentication / Authorization feature, your Reply URL instead uses the gateway URL with the _/signin-aad_ path.


9. 按一下 [ **儲存**。 然後將複製 **用戶端識別碼** 應用程式。 稍後您會設定您的應用程式使用此資訊。

10. 在底部命令列中，按一下 [ **檢視端點**, ，然後將複製 **同盟中繼資料文件** URL 和文件或瀏覽器中瀏覽至它的下載。

11. 根目錄內 **EntityDescriptor** 項目，應該有 **entityID** 表單的屬性 `https://sts.windows.net/` 後面跟著 GUID 特定租用戶 (稱為 「 租用戶識別碼 」)。 複製此值 – 會成為您 **簽發者 URL**。 稍後您會設定您的應用程式使用此資訊。

### <a name="secrets"> </a>將 Azure Active Directory 資訊新增至您的應用程式

> [AZURE.NOTE]
如果您使用 App Service 閘道器，請忽略此章節，並改為在入口網站中瀏覽至您的閘道器。 選取 **設定**, ，**識別**, ，然後 **Azure Active Directory**。 ClientID 中貼上，並新增至租用戶識別碼 **允許的租用戶** 清單。 按一下 [ **儲存**。


13. 回到 [Azure portal], ，瀏覽至您的應用程式。 按一下 [ **設定**, ，然後 **驗證/授權**。

14. 如果未啟用驗證和授權功能，移至切換 **上**。

15. 按一下 [ **Azure Active Directory**, ，然後按一下 [ **進階** 下 **管理模式**。 貼入您先前取得的用戶端識別碼和簽發者 URL 值。 然後按一下 [ **確定**。

    ![][1]
    
    App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。 您必須在應用程式程式碼中授權使用者。 

17. (選擇性)若要限制存取您的站台由 Azure Active Directory 驗證的使用者，請設定 **不會驗證要求時要採取的動作** 至 **Azure Active Directory**。 這會要求所有要求都需經過驗證，且所有未經驗證的要求都會重新導向至 Azure Active Directory 以進行驗證。

17. 按一下 [ **儲存**。 

現在，您已可在應用程式中使用 Azure Active Directory 進行驗證。 

## (選擇性步驟) 設定原生用戶端應用程式

Azure Active Directory 也可讓您註冊更能控制權限對應的原生用戶端。 如果您想要執行使用文件庫，例如登入則需此 **Active Directory Authentication Library**。

1. 瀏覽至 **Active Directory** 中 [Azure classic portal]。

2. 選取您的目錄，然後選取 **應用程式** 頂端的索引標籤。 按一下 [ **新增** 底部建立新的應用程式註冊。 

3. 按一下 [ **加入我的組織正在開發的應用程式**。

4. 在 [新增應用程式精靈] 中，輸入 **名稱** 您的應用程式，然後按一下 [  **原生用戶端應用程式** 型別。 接著，按一下以繼續。

5. 在 **重新導向 URI** 方塊中，輸入您的網站 _/.auth/login/done_ 端點會使用 HTTPS 配置。 這個值應該會類似於 _https://contoso.azurewebsites.net/.auth/login/done_。

6. 一旦新增原生應用程式之後，按一下 [ **設定** ] 索引標籤。 尋找 **用戶端識別碼** 並記下此值。

7. 將頁面向下的捲動 **其他應用程式的權限** 區段，然後按一下 **新增應用程式**。

8. 搜尋先前註冊的 Web 應用程式，再按一下加號圖示。 然後按一下核取標記以關閉對話方塊。

9. 在您剛才加入的新項目，開啟 **委派的權限** 下拉式清單中選取 **存取 (appName)**。 然後按一下 [ **儲存**。

您現在已設定了可以存取您 App Service 應用程式的原生用戶端應用程式。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md
[alternative method]:#advanced
