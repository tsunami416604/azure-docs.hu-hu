## 在模擬裝置上接收訊息

在本節中，您將修改在 [IoT 中心入門] 中建立的模擬裝置應用程式，以接收來自 IoT 中心的雲端對裝置訊息。

1. 在 Visual Studio 中，在 **SimulatedDevice** 專案中，加入下列方法加入 **程式** 類別。

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

    `ReceiveAsync` 方法會以非同步方式，在裝置收到訊息時，傳回收到的訊息。 它會傳回 *null* 塑造逾時期間 （在此情況下，會使用預設值為 1 分鐘） 後。 當發生這種情況時，我們想讓程式碼繼續等待新訊息。 所以加上 `if (receivedMessage == null) continue` 行。

    `CompleteAsync()` 的呼叫會通知 IoT 中心，說明已成功處理訊息，且可以安全地從裝置佇列中移除該訊息。 如果發生的狀況，所以無法完成訊息的處理程序的裝置應用程式，IoT 中心將會傳遞一次。就必須在裝置應用程式中的訊息處理邏輯 *等冪*, ，也就是接收多次相同的訊息應會產生相同的結果。 應用程式也可以暫時 `Abandon` 訊息，讓 IoT 中心將訊息保留在佇列中，以供之後取用；或是 `Reject` 訊息，以永久從佇列移除訊息。 請參閱 [IoT 中心開發人員指南][IoT Hub Developer Guide - C2D] 如需有關定域機組-裝置訊息生命週期。

> [AZURE.NOTE] 當使用 HTTP/1 而不 AMQP，做為傳輸， `ReceiveAsync` 會立即傳回。 使用 HTTP/1 的雲端到裝置訊息，其支援模式是間歇連接至裝置，且不常檢查訊息 (換言之，比每隔 25 分鐘更少)。 發出更多 HTTP/1 接收會導致 IoT 中心節流要求。 請參閱 [IoT 中心開發人員指南][IoT Hub Developer Guide - C2D] 如需有關支援 AMQP 和 HTTP/1 和節流的 IoT 中心之間的差異。

2. 新增下列方法中的 **Main** 方法就馬上 `Console.ReadLine()` 行 ︰

        ReceiveC2dAsync();

> [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，建議如 MSDN 文章 [暫時性錯誤處理] 所建議，實作重試原則 (例如指數型輪詢)。

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->


