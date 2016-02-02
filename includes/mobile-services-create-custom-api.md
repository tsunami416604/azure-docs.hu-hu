
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **行動電話服務**, ，然後選取您的行動服務。

2. 按一下 [API] ****索引標籤，然後按一下 [建立]****。 這樣做會顯示 [Create a new custom API]**** 對話方塊。

3. 在 [API 名稱] ****中輸入 completeall__，然後按一下 [檢查] 按鈕以建立新的 API。
    > [AZURE.TIP] 有了預設權限，具備應用程式金鑰的任何人都可以呼叫自訂 API。 不過，應用程式金鑰並不會被視為安全的認證，因為它不是以安全的方式散佈或儲存。 請考慮限制只有經過驗證的使用者才能存取，以提供進階的安全性。

4. 按一下 API 資料表中的 [completeall]****。

5. 按一下 [指令碼]**** 索引標籤，以下列程式碼取代現有的程式碼，然後按一下 [儲存]****。 此程式碼會使用 [mssql 物件] 來存取 **todoitem** 資料表，直接以設定 `完整` 所有項目上的旗標。 因為使用了 **exports.post** 函數，所以用戶端會傳送 POST 要求以執行此操作。 系統會以整數值將變更的列數傳回給用戶端。

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



> [AZURE.NOTE] [要求](http://msdn.microsoft.com/library/windowsazure/jj554218.aspx) 和 [回應](http://msdn.microsoft.com/library/windowsazure/dn303373.aspx) 會實作物件提供給自訂 API 函數 [Express.js 程式庫](http://go.microsoft.com/fwlink/p/?LinkId=309046)。 







[mssql object]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx 

