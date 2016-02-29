
1. 在 Visual Studio 方案總管] 中，依序展開 **控制器** 行動服務專案資料夾中的。 開啟 TodoItemController.cs。 在檔案頂端新增下列 `using` 陳述式：

        using System;
        using System.Collections.Generic;

2. 以下列程式碼更新 `PostTodoItem` 方法定義：  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

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

    此程式碼會在插入 Todo 項目之後傳送推播通知 (含所插入項目的文字)。 發生錯誤時，程式碼會將錯誤記錄檔項目來檢視 **記錄** ] 索引標籤中的行動服務的 [Azure 傳統入口網站](https://manage.windowsazure.com/)。

3. 將您的行動服務專案重新發佈至 Azure。
