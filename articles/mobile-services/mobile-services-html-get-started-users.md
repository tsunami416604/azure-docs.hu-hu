<properties 
    pageTitle="將驗證新增至 HTML/JavaScript 應用程式 | Microsoft Azure" 
    description="了解如何使用行動服務透過眾多識別提供者驗證 HTML 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft 帳戶。" 
    services="mobile-services" 
    documentationCenter="" 
    authors="ggailey777" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="javascript" 
    ms.topic="article" 
    ms.date="11/30/2015" 
    ms.author="glenga"/>

# 在您的行動服務應用程式中新增驗證 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

本主題顯示如何從 HTML 應用程式 (包含 PhoneGap 或 Cordova 應用程式) 驗證 Azure 行動服務中的使用者。  在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。 由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。  

本教學課程會以行動服務快速入門為基礎。 您還必須先完成本教學課程 [Get started with Mobile Services]。 

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. 在應用程式目錄中，啟動下列命令檔案從 **伺服器** 子資料夾。

    + **啟動 windows** （Windows 電腦） 
    + **launch-mac.command** （Mac OS X 電腦）
    + **launch-linux.sh** （Linux 電腦）

    >[AZURE.NOTE]在 Windows 電腦上，輸入 `R` PowerShell 要求您確認您想要執行指令碼時。 因為指令碼是從網際網路中下載，所以您的網頁瀏覽器可能會警告您不要執行指令碼。 發生此情況時，您必須要求瀏覽器繼續載入指令碼。

    如此會在本機電腦上啟動網頁伺服器來裝載新的應用程式。

2. 在網頁瀏覽器中開啟 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> 以啟動應用程式。 

    資料載入失敗。 這是因為應用程式會嘗試驗證的使用者身分存取行動服務，但 _TodoItem_ 資料表現在需要驗證。

3. (選用) 開啟網頁瀏覽器的指令碼偵錯工具，重新載入頁面。 確認發生拒絕存取錯誤。 

接下來，您要將應用程式更新為在要求行動服務的資源之前允許驗證。

##<a name="add-authentication"></a>將驗證新增至應用程式

>[AZURE.NOTE]因為登入執行快顯視窗中，您應該叫用 **登入** 方法從按鈕的 click 事件。 否則，許多瀏覽器會阻擋登入視窗。

1. 開啟專案檔案 index.html，找出 H1 元素，在它下面加入下列程式碼片段：

        <div id="logged-in">
            You are logged in as <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            You are not logged in.
            <button>Log in</button>
        </div>

    這樣可讓您從網頁登入行動服務。

2. 在 page.js 檔案中，找出檔案最尾端呼叫 refreshTodoItems 函數的那一行程式碼，換成下列程式碼： 
    
        function refreshAuthDisplay() {
            var isLoggedIn = client.currentUser !== null;
            $("#logged-in").toggle(isLoggedIn);
            $("#logged-out").toggle(!isLoggedIn);

            if (isLoggedIn) {
                $("#login-name").text(client.currentUser.userId);
                refreshTodoItems();
            }
        }

        function logIn() {
            client.login("facebook").then(refreshAuthDisplay, function(error){
                alert(error);
            });
        }

        function logOut() {
            client.logout();
            refreshAuthDisplay();
            $('#summary').html('<strong>You must login to access data.</strong>');
        }

        // On page init, fetch the data and set up event handlers
        $(function () {
            refreshAuthDisplay();
            $('#summary').html('<strong>You must login to access data.</strong>');          
            $("#logged-out button").click(logIn);
            $("#logged-in button").click(logOut);
        });

    這會建立一組函數來處理驗證程序。 使用者透過 Facebook 登入來驗證。 如果您使用的身分識別提供者不是 Facebook，請將傳給上述 **login** 方法的值變更為以下其中一個：*microsoftaccount*、*facebook*、*twitter*、*google*或 *aad*。

    >[AZURE.IMPORTANT]在 PhoneGap 應用程式中，您也必須將下列外掛程式加入專案 ︰
    ><ul><li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git</code></li>
    ><li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git</code></li></ul>

9. 返回正在執行應用程式的瀏覽器，重新整理頁面。 

       When you are successfully logged-in, the app should run without errors, and you should be able to query Mobile Services and make updates to data.

    >[AZURE.NOTE]當您使用 Internet Explorer 時，您可能在登入後會出現錯誤 ︰ <code>Cannot reach window opener. It may be on a different Internet Explorer zone</code>。 這是因為快顯是在不同於 localhost (內部網路) 的安全性區域 (網際網路) 中執行。 這只會在開發期間使用 localhost 時影響到應用程式。 解決方法是開啟 [網際網路選項]**** 的 [安全性]**** 索引標籤，依序按一下 [近端內部網路]****、[網站]****，然後停用 [自動偵測內部網路]****。 完成測試時，請記得恢復此設定。

## <a name="next-steps"> </a>後續步驟

在下一個教學課程中， [Authorize users with scripts], ，您將行動服務根據經驗證的使用者所提供的使用者識別碼值，並用來篩選行動服務所傳回的資料。 深入了解如何使用行動服務與 HTML/JavaScript [Mobile Services HTML/JavaScript How-to Conceptual Reference]

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->

[4]: ./media/mobile-services-html-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-html-get-started-users/mobile-service-uri.png
[13]: ./media/mobile-services-html-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Get started with Mobile Services]: mobile-services-html-get-started.md
[Authorize users with scripts]: mobile-services-javascript-backend-service-side-authorization.md
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md
 

