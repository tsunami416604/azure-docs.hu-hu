1. 在 Visual Studio 中，更新 `PostTodoItem` 方法定義為下列程式碼:

     public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
     {
         TodoItem current = await InsertAsync(item);
    
         // Get the settings for the server project.
         HttpConfiguration config = this.Configuration;
         MobileAppSettingsDictionary settings =
             this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
    
         // Get the Notification Hubs credentials for the Mobile App.
         string notificationHubName = settings.NotificationHubName;
         string notificationHubConnection = settings
             .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;
    
         // Create the notification hub client.
         NotificationHubClient hub = NotificationHubClient
             .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);
    
         // Get the current user SID and create a tag for the current user.
         var claimsPrincipal = this.User as ClaimsPrincipal;
         string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
         string userTag = "_UserId:" + sid;
    
         // Define push message text.
         var messageText = string.Format("New item added: {0}", item.Text);
    
         // Build a dictionary for the template.
         var notification = new Dictionary<string, string> { { "message", messageText } };
    
         try
         {
             // Send a template notification to the user ID.
             await hub.SendTemplateNotificationAsync(notification, userTag);
         }
         catch (System.Exception)
         {
             throw new HttpResponseException(System.Net.HttpStatusCode.InternalServerError);
         }
         return CreatedAtRoute("Tables", new { id = current.Id }, current);
     }

 此程式碼只會將範本通知傳送給以目前使用者 SID 標記的註冊。 請注意 SID 值可以變更，因為它衍生自驗證提供者的權杖。

2. 發佈伺服器專案。






