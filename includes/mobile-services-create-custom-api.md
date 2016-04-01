

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **行動電話服務**, ，然後選取您的行動服務。

2. 按一下 [ **API** 索引標籤，然後再按一下 **建立**。 這會顯示 **建立新的自訂 API** ] 對話方塊。

3. 型別 _completeall_ 中 **API 名稱**, ，然後按一下核取按鈕以建立新的 API。

    > [AZURE.TIP] 有了預設權限，應用程式金鑰的任何人都可以呼叫自訂 API。 不過，應用程式金鑰並不會被視為安全的認證，因為它不是以安全的方式散佈或儲存。 請考慮限制只有經過驗證的使用者才能存取，以提供進階的安全性。

4. 按一下 [ **completeall** API 資料表中。

5. 按一下 [ **指令碼** ] 索引標籤上，使用下列程式碼，取代現有的程式碼，然後按一下 [ **儲存**。    此程式碼使用 [mssql object] 存取 **todoitem** 資料表，直接以設定 `complete` 旗標上的所有項目。 因為 **exports.post** 函式時，用戶端傳送 POST 要求來執行作業。 系統會以整數值將變更的列數傳回給用戶端。


        exports.post = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "UPDATE todoitem SET complete = 1 " +
                "WHERE complete = 0; SELECT @@ROWCOUNT as count";
            mssql.query(sql, {
                success: function(results) {
                    if(results.length == 1)
                        response.send(200, results[0]);
                }
            })
        };


> [AZURE.NOTE]  [要求](http://msdn.microsoft.com/library/windowsazure/jj554218.aspx) 和 [回應](http://msdn.microsoft.com/library/windowsazure/dn303373.aspx) 會實作物件提供給自訂 API 函數 [Express.js 程式庫](http://go.microsoft.com/fwlink/p/?LinkId=309046)。 

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[mssql object]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx


