
1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **資料** 標籤，然後按一下 **TodoItem** 資料表。 
 
2. 在 **todoitem**, ，按一下 [ **指令碼** 索引標籤並選取 **插入**。
   
    這會顯示函式中發生插入時所叫用 **TodoItem** 資料表。

3. 下列程式碼，來取代 insert 函數，然後按一下 [ **儲存**:

        function insert(item, user, request) {
        // Define a simple payload for a GCM notification.
        var payload = {
            "data": {
                "message": item.text
            }
        };      
        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.gcm.send(null, payload, {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse, payload);
                        request.respond();
                        },              
                    error: function (pushResponse) {
                        console.log("Error Sending push:", pushResponse);
                        request.respond(500, { error: pushResponse });
                        }
                    });
                },
            error: function(err) {
                console.log("request.execute error", err)
                request.respond();
            }
          });
        }

    這會註冊新的 insert 指令碼，並使用 [gcm 物件](http://go.microsoft.com/fwlink/p/?LinkId=282645) 在插入成功後，傳送推播通知給所有註冊的裝置。 

