## 從模擬裝置傳送互動式訊息

在本節中，您會修改模擬裝置應用程式，將裝置對雲端的互動式訊息傳送至 IoT 中心。

1. 在 Visual Studio 中，在 **SimulatedDevice** 專案中，加入下列方法加入 **程式** 類別。
   
        private static async void SendDeviceToCloudInteractiveMessagesAsync()
        {
            while (true)
            {
                var interactiveMessageString = "Alert message!";
                var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
                interactiveMessage.Properties["messageType"] = "interactive";
                interactiveMessage.MessageId = Guid.NewGuid().ToString();

                await deviceClient.SendEventAsync(interactiveMessage);
                Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

                Thread.Sleep(10000);
            }
        }

    這個方法是非常類似於 `SendDeviceToCloudMessagesAsync()` 方法建立的 [開始使用 IoT 中心]，唯一的差異在於， `MessageId` 系統屬性和使用者的屬性稱為 `messageType` 現在設定。
    `MessageId` 屬性設為全域唯一識別碼 (guid)，用於刪除重複接收的訊息。 `messageType` 屬性用於區別來自資料點訊息的互動。 這項資訊是在訊息屬性中傳遞，不是在訊息主體中傳遞，所以後端中的事件處理器就不必只為執行路由而還原序列化整個訊息。

> [AZURE.NOTE] 很重要， `MessageId`, 用於互動式訊息使用重複資料刪除時，請在裝置中，因為因間歇性的網路通訊 (或其他失敗) 可能會導致相同的訊息，從裝置的多個 retrasmissions。 相較於 GUID，也可以使用語意的訊息識別碼 (例如相關訊息資料欄位的雜湊)。

2. 新增下列方法中的 **Main** 方法就馬上 `Console.ReadLine()` 行:

        SendDeviceToCloudInteractiveMessagesAsync();

> [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，建議如 MSDN 文章 [暫時性錯誤處理] 所建議，實作重試原則 (例如指數型輪詢)。

<!-- Links -->
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png





