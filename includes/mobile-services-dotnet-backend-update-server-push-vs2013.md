這些步驟會建立新的自訂 [ApiController](http://go.microsoft.com/fwlink/p/?LinkId=512673) ，推播通知傳送至應用程式。 您可以實作此相同程式碼中的 [TableController](http://msdn.microsoft.com/library/azure/dn643359.aspx) 或是後端服務裡。 

1. 在 Visual Studio 方案總管] 中以滑鼠右鍵按一下行動服務專案的 Controllers 資料夾中，展開 **新增**, ，然後按一下 [ **新增建構項目**。

    這會顯示 [新增 Scaffold] 對話方塊。

2. 展開 **Azure 行動服務** 按一下 **Azure 行動服務自訂控制器**, ，然後按一下 [ **新增**, ，提供 **控制器名稱** 的 `NotifyAllUsersController`, ，然後按一下 [ **新增** 一次。

    ![Web API Add Scaffold dialog](./media/mobile-services-dotnet-backend-update-server-push-vs2013/add-custom-api-controller.png)

    這會建立名為的新空白控制器類別 **NotifyAllUsersController**。 

3. 在新的 NotifyAllUsersController.cs 專案檔案，新增下列 **使用** 陳述式 ︰

        using Newtonsoft.Json.Linq;
        using System.Threading.Tasks;

4. 在控制器上新增以下實作 POST 方法的程式碼：

        public async Task<bool> Post(JObject data)
        {
            try
            {
                // Define the XML paylod for a WNS native toast notification 
                // that contains the value supplied in the POST request.
                string wnsToast = 
                    string.Format("<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<toast><visual><binding template=\"ToastText01\">" + 
                    "<text id=\"1\">{0}</text></binding></visual></toast>", 
                    data.GetValue("toast").Value<string>());

                // Define the WNS native toast with the payload string.
                WindowsPushMessage message = new WindowsPushMessage();
                message.XmlPayload = wnsToast;

                // Send the toast notification.
                await Services.Push.SendAsync(message);
                return true;
            }
            catch (Exception e)
            {
                Services.Log.Error(e.ToString());
            }
            return false;
        }

    >[AZURE.NOTE]這個 POST 方法可以呼叫任何具有應用程式索引鍵，這並不安全的用戶端。 為了保護端點，請將 `[AuthorizeLevel(AuthorizationLevel.User)]` 屬性套用至方法或類別，以要求驗證。 


