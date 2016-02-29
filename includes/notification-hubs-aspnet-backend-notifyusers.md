## 建立 WebAPI 專案

新的 ASP.NET WebAPI 後端將會在後續各節中建立，而且有三個主要用途：

1. **驗證用戶端**: 將稍後加入訊息處理常式，以驗證用戶端要求，並將使用者與要求產生關聯。
2. **用戶端通知註冊**: 之後，您將加入一個控制器來處理新的註冊，用戶端裝置接收通知。 已驗證的使用者名稱會自動加入至註冊作為 [標記](https://msdn.microsoft.com/library/azure/dn530749.aspx)。
3. **將通知傳送至用戶端**: 之後，您也會加入一個控制器來提供方法讓使用者觸發安全推送至裝置與標記相關聯的用戶端。 

下列步驟說明如何建立新的 ASP.NET WebAPI 後端： 


> [AZURE.NOTE] **重要**: 之前開始本教學課程，請確定您已安裝最新版本的 NuGet 封裝管理員。 若要檢查版本，請啟動 Visual Studio。 從 **工具** ] 功能表上，按一下 [ **擴充功能和更新**。 搜尋 **NuGet Package Manager for Visual Studio 2013**, ，並確定您已安裝 2.8.50313.46 版或更新版本。 否則的話，請解除安裝，然後重新安裝 NuGet Package Manager。
> 
> ![][B4]

> [AZURE.NOTE] 請確定您已安裝 Visual Studio [Azure SDK](http://azure.microsoft.com/downloads/) 供網站部署。

1. 啟動 Visual Studio 或 Visual Studio Express。 按一下 [ **伺服器總管** 並登入您的 Azure 帳戶。 Visual Studio 將需要您登入，才能在您的帳戶上建立網站資源。
2. 在 Visual Studio 中，按一下 [ **檔案**, ，然後按一下 [ **新增**, ，然後 **專案**, ，展開 **範本**, ，**Visual C#**, ，然後按一下 [ **Web** 和 **ASP.NET Web 應用程式**, ，輸入名稱 **AppBackend**, ，然後按一下 [ **確定**。 
    
    ![][B1]

3. 在 **新增 ASP.NET 專案** ] 對話方塊中，按一下 [ **Web API**, ，然後按一下 [ **確定**。

    ![][B2]

4. 在 **設定的 Microsoft Azure Web 應用程式** ] 對話方塊中，選擇 [訂用帳戶，和 **應用程式服務方案** 您已經建立。 您也可以選擇 **建立新的應用程式服務方案** 建立一個從 [] 對話方塊。 在此教學課程中您不需要資料庫。 一旦您選取您的應用程式服務計劃，按一下 [ **確定** 以建立專案。

    ![][B5]



## 向 WebAPI 後端驗證用戶端

在本節中，您將建立新的訊息處理常式類別，名為 **AuthenticationTestHandler** 為新的後端。 這個類別衍生自 [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) 並加入為訊息處理常式，以便處理進入後端的所有要求。 



1. 在 [方案總管] 中，以滑鼠右鍵按一下 **AppBackend** 專案，然後按一下 **新增**, ，然後按一下 [ **類別**。 將新類別 **AuthenticationTestHandler.cs**, ，然後按一下 **新增** 以產生類別。 這個類別會用來驗證使用者使用 *基本驗證* 起見。 請注意，您的應用程式可以使用任何驗證結構描述。

2. 在 AuthenticationTestHandler.cs 中，加入下列 `using` 陳述式：

        using System.Net.Http;
        using System.Threading;
        using System.Security.Principal;
        using System.Net;
        using System.Web;

3. 在 AuthenticationTestHandler.cs 中，以下列程式碼取代 `AuthenticationTestHandler` 類別定義。 

    下列三個條件都成立時，這個處理常式將授權要求:
    * 包含要求 *授權* 標頭。 
    * 要求使用 *基本* 驗證。 
    * 使用者名稱字串和密碼字串都是相同的字串。

    否則，將會拒絕此要求。 這不是真正的驗證和授權方法。 這只是本教學課程中一個非常簡單的範例。

    如果要求訊息是由驗證並授權 `AuthenticationTestHandler`, ，基本驗證使用者會附加至目前的要求上 [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx)。 HttpContext 中的使用者資訊將用於另一個控制器 (RegisterController) 稍後新增 [標記](https://msdn.microsoft.com/library/azure/dn530749.aspx) 至通知註冊要求。

        public class AuthenticationTestHandler : DelegatingHandler
        {
            protected override Task<HttpResponseMessage> SendAsync(
            HttpRequestMessage request, CancellationToken cancellationToken)
            {
                var authorizationHeader = request.Headers.GetValues("Authorization").First();
    
                if (authorizationHeader != null && authorizationHeader
                    .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
                {
                    string authorizationUserAndPwdBase64 =
                        authorizationHeader.Substring("Basic ".Length);
                    string authorizationUserAndPwd = Encoding.Default
                        .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                    string user = authorizationUserAndPwd.Split(':')[0];
                    string password = authorizationUserAndPwd.Split(':')[1];
    
                    if (verifyUserAndPwd(user, password))
                    {
                        // Attach the new principal object to the current HttpContext object
                        HttpContext.Current.User =
                            new GenericPrincipal(new GenericIdentity(user), new string[0]);
                        System.Threading.Thread.CurrentPrincipal =
                            System.Web.HttpContext.Current.User;
                    }
                    else return Unauthorized();
                }
                else return Unauthorized();
    
                return base.SendAsync(request, cancellationToken);
            }
    
            private bool verifyUserAndPwd(string user, string password)
            {
                // This is not a real authentication scheme.
                return user == password;
            }
    
            private Task<HttpResponseMessage> Unauthorized()
            {
                var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
                var tsc = new TaskCompletionSource<HttpResponseMessage>();
                tsc.SetResult(response);
                return tsc.Task;
            }
        }

    > [AZURE.NOTE] **安全性注意事項**: `AuthenticationTestHandler` 類別不提供真正的驗證。 它僅可用於模仿基本驗證而且並不安全。 您必須在生產應用程式和服務中實作安全的驗證機制。               

4. 在結尾處新增下列程式碼 `Register` 方法中的 **app_start/webapiconfig.cs** 類別，以註冊訊息處理常式:

        config.MessageHandlers.Add(new AuthenticationTestHandler());

5. 儲存您的變更。

## 使用 WebAPI 後端註冊通知

在本節中，我們會將新的控制器加入至 WebAPI 後端來處理要求，以使用通知中樞的用戶端程式庫，為使用者和裝置註冊通知。 控制器將會對已由 `AuthenticationTestHandler` 驗證並附加至 HttpContext 的使用者，新增使用者標記。 此標記會有以下字串格式：`"username:<actual username>"`。


 

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **AppBackend** 專案，然後按一下 **管理 NuGet 封裝**。

2. 在左側，按一下 [ **線上**, ，並搜尋 **Microsoft.Azure.NotificationHubs** 中 **搜尋** 方塊。

3. 在 [結果] 清單中，按一下 [ **Microsoft Azure 通知中樞**, ，然後按一下 [ **安裝**。 請完成安裝，然後關閉 [NuGet Package Manager] 視窗。

    這會加入到使用 Azure 通知中心 SDK 參考 <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification 中樞 NuGet 封裝</a>.

4. 我們現在會建立新的類別檔案，表示即將傳送的不同安全通知。 在完整的實作中，通知會儲存在資料庫中。 為了簡單起見，本教學課程會將它們儲存在記憶體中。 在 [方案總管] 中，以滑鼠右鍵按一下 **模型** 資料夾中，按一下 [ **新增**, ，然後按一下 [ **類別**。 將新類別 **Notifications.cs**, ，然後按一下 [ **新增** 以產生類別。 

    ![][B6]

5. 在 Notifications.cs 中，將下列 `using` 陳述式新增在檔案頂端：

        using Microsoft.Azure.NotificationHubs;

6. 取代 `Notifications` 類別具有下列定義，並確定使用的連接字串 (含完整存取權) 您的通知中心和中樞名稱來取代兩個預留位置 (可在 [Azure 傳統入口網站](http://manage.windowsazure.com)):

        public class Notifications
        {
            public static Notifications Instance = new Notifications();
        
            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>", 
                                                                             "<hub name>");
            }
        }



7. 接下來我們將建立新的控制器命名為 **RegisterController**。 在 [方案總管] 中，以滑鼠右鍵按一下 **控制器** 資料夾，然後按一下 [ **新增**, ，然後按一下 [ **控制器**。 按一下 [ **Web API 2 Controller--Empty** 項目，並接著按一下 **新增**。 將新類別 **RegisterController**, ，然後按一下 [ **新增** 一次，以產生控制器。

    ![][B7]

    ![][B8]

8. 在 RegisterController.cs 中，加入下列 `using` 陳述式：

        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.NotificationHubs.Messaging;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

9. 在 `RegisterController` 類別定義中加入下列程式碼。 請注意，在此程式碼中，我們會為已附加至 HttpContext 的使用者新增使用者標記。 我們新增的訊息篩選器 `AuthenticationTestHandler` 會驗證此使用者並附加至 HttpContext。 您也可以新增選擇性檢查，以驗證使用者是否有權註冊所要求的標籤。

        private NotificationHubClient hub;

        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }

        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            string newRegistrationId = null;
            
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }

            if (newRegistrationId == null) 
                newRegistrationId = await hub.CreateRegistrationIdAsync();

            return newRegistrationId;
        }

        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }

            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;

            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);

            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }

            return Request.CreateResponse(HttpStatusCode.OK);
        }

        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }

        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }

10. 儲存您的變更。

## 從 WebAPI 後端傳送通知

在本節中，您會加入新的控制器，以便用戶端裝置使用 ASP.NET WebAPI 後端中的 Azure 通知中樞服務管理程式庫，根據使用者名稱標記傳送通知。


1. 建立另一個名為的新控制器 **NotificationsController**。 您所建立的方式建立 **RegisterController** 上一節。

2. 在 NotificationsController.cs 中，加入下列 `using` 陳述式：

        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

3. 將下列方法加入 **NotificationsController** 類別。

    此程式碼會傳送以平台通知服務 (PNS) `pns` 參數為基礎的通知類型。 值 `to_tag` 用來設定 *username* 標記的訊息。 此標記必須符合作用中通知中樞註冊的使用者名稱標記。 通知訊息是取自 POST 要求主體，並針對目標 PNS 格式化。 

    根據您的支援裝置用來接收通知的平台通知服務 (PNS)，使用不同的格式可支援不同的通知。 比方說 Windows 裝置上，您可以使用 [快顯通知的 WNS](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) 另一個的 PNS 並不直接支援的。 因此您的後端必須針對您想要支援的裝置 PNS，將通知格式化為支援的通知。 然後使用適當的傳送 API [NotificationHubClient 類別](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx)

        public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
        {
            var user = HttpContext.Current.User.Identity.Name;
            string[] userTag = new string[2];
            userTag[0] = "username:" + to_tag;
            userTag[1] = "from:" + user;

            Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
            HttpStatusCode ret = HttpStatusCode.InternalServerError;

            switch (pns.ToLower())
            {
                case "wns":
                    // Windows 8.1 / Windows Phone 8.1
                    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                                "From " + user + ": " + message + "</text></binding></visual></toast>";
                    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                    break;
                case "apns":
                    // iOS
                    var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                    break;
                case "gcm":
                    // Android
                    var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);
                    break;
            }

            if (outcome != null)
            {
                if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                    (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
                {
                    ret = HttpStatusCode.OK;
                }
            }

            return Request.CreateResponse(ret);
        }


4. 按下 **F5** 以執行應用程式，並確保工作的準確性，到目前為止。 應用程式應即啟動網頁瀏覽器，並顯示 ASP.NET 首頁。 

##發佈新的 WebAPI 後端

1. 為了可以從所有裝置存取此應用程式，我們現在可以將它部署到 Azure 網站。 以滑鼠右鍵按一下 **AppBackend** 專案，然後選取 **發行**。

2. 選取 **Microsoft Azure Web Apps** 作為發行目標。

    ![][B15]

3. 使用您的 Azure 帳戶登入，並選取現有或新的 Web 應用程式。

    ![][B16]

4. 請記下的 **目的地 URL** 屬性 **連接** ] 索引標籤。 我們將此 URL 作為您 *後端端點* 稍後在本教學課程。 按一下 [ **發行**。

    ![][B18]


[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG

