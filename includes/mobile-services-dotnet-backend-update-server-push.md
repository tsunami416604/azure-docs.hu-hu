1. 在 Visual Studio 的 [方案總管] 中，展開行動服務專案中的 **Controllers** 資料夾。 開啟 TodoItemController.cs，並以下列程式碼更新 `PostTodoItem` 方法定義：

     public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
     {
         TodoItem current = await InsertAsync(item);
    
         // Create a WNS native toast.
         WindowsPushMessage message = new WindowsPushMessage();
    
         // Define the XML paylod for a WNS native toast notification 
         // that contains the text of the inserted item.
         message.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                              @"<toast><visual><binding template=""ToastText01"">" +
                              @"<text id=""1"">" + item.Text + @"</text>" +
                              @"</binding></visual></toast>";
         try
         {
             var result = await Services.Push.SendAsync(message);
             Services.Log.Info(result.State.ToString());
         }
         catch (System.Exception ex)
         {
             Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
         }
         return CreatedAtRoute("Tables", new { id = current.Id }, current);
     }

 此程式碼會在插入 todo 項目之後傳送推播通知 (含所插入項目的文字)。 發生錯誤時，程式碼會將錯誤記錄檔項目來檢視 **記錄** ] 索引標籤中的行動服務的 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
 >[AZURE.NOTE] 您可以使用範本通知傳送單一推播通知至多個平台的用戶端。 如需詳細資訊，請參閱 [支援從單一行動服務的多個裝置平台](../articles/mobile-services-how-to-use-multiple-clients-single-service.md#push)。

2. 將行動服務專案重新發佈至 Azure。







