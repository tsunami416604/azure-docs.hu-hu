## 在模擬裝置接收訊息

在本節中，您將修改在 [IoT 中心入門] 中建立的模擬裝置應用程式，以接收來自 IoT 中心的雲端對裝置訊息。

1. 在 Visual Studio 的 **SimulatedDevice** 專案中，將下列方法加入 [程式]**** 類別。

     private static async void ReceiveC2dAsync()
     {
         Console.WriteLine("\nReceiving cloud to device messages from service");
         while (true)
         {
             Message receivedMessage = await deviceClient.ReceiveAsync();
             if (receivedMessage == null) continue;
    
             Console.ForegroundColor = ConsoleColor.Yellow;
             Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
             Console.ResetColor();
    
             await deviceClient.CompleteAsync(receivedMessage);
         }
     }

 `ReceiveAsync` 方法以非同步方式傳回已接收的訊息時，它會傳送到裝置。 它會在可指明的逾時期間過後傳回 *null* (在本例中，使用的是預設值 1 分鐘)。 當發生這種情況時，我們想讓程式碼繼續等待新訊息。 這是因為 `如果 (receivedMessage = = null) 繼續` 列。

 若要呼叫 `CompleteAsync()` IoT 中心會通知已成功處理訊息，並，它可以安全地從佇列中移除裝置。 如果因故導致裝置應用程式無法完成訊息處理作業，IoT 中心會再傳遞一次；因此裝置應用程式中的訊息處理邏輯必須是「等冪」**，換言之，接收多次相同的訊息應該會產生相同的結果。 應用程式也會暫時可以 `放棄` 訊息，必須保留供未來取用; 佇列中的訊息的 IoT 中心或 `拒絕` 永遠會從佇列中移除訊息的訊息。 請參閱 [IoT 中心開發人員指南 ][iot hub developer guide - c2d] 如需有關定域機組-裝置訊息生命週期。

> [AZURE.NOTE] 當使用 HTTP/1 而不 AMQP，做為傳輸， `ReceiveAsync` 會立即傳回。 使用 HTTP/1 的雲端到裝置訊息，其支援模式是間歇連接至裝置，且不常檢查訊息 (換言之，比每隔 25 分鐘更少)。 發出更多 HTTP/1 接收會導致 IoT 中心節流要求。 請參閱 [IoT 中心開發人員指南 ][iot hub developer guide - c2d] 如需有關支援 AMQP 和 HTTP/1 和節流的 IoT 中心之間的差異。

2. 新增下列方法中的 **Main** 方法就馬上 `console.readline ()` 行:

        ReceiveC2dAsync();


> [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，建議如 MSDN 文章 [暫時性錯誤處理] 所建議，實作重試原則 (例如指數型輪詢)。







[iot hub developer guide - c2d]: iot-hub-devguide.md#c2d 

