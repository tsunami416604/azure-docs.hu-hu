## 從應用程式後端傳送雲端到裝置訊息

在本節中，您會撰寫 Windows 主控台應用程式，將雲端到裝置訊息傳送至模擬裝置應用程式。

1. 在目前的 Visual Studio 方案中，建立新的 Visual C# 桌面應用程式專案使用 **主控台應用程式** 專案範本。 將專案命名為 **SendCloudToDevice**。

    ![][20]

2. 在 [方案總管] 中，以滑鼠右鍵按一下方案，然後按一下 [**管理方案的 NuGet 封裝...**]。

    此時會顯示 [管理 NuGet 封裝] 視窗。

3. 搜尋 `Microsoft Azure 裝置`, ，按一下 [ **安裝**, ，並接受使用規定。

    這會下載、 安裝，並將參考加入至 [Azure IoT-服務 SDK NuGet 封裝]。

4. 新增下列 `使用` 上方的陳述式 **Program.cs** 檔案:

        using Microsoft.Azure.Devices;

5. 加入下列欄位來 **程式** 類別，來取代預留位置值 IoT 中心連接字串，從 [開始使用 IoT 中心]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. 將下列方法加入至 **Program** 類別：

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    這個方法會將新的定域機組對裝置訊息傳送之識別碼的裝置 `myFirstDevice`。 因此，變更此參數，以防您修改中所用的 [開始使用 IoT 中心]。

7. 最後，將下列幾行加入至 **Main** 方法：

     Console.WriteLine("Send Cloud-to-Device message\n");
     serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    
     Console.WriteLine("Press any key to send a C2D message.");
     Console.ReadLine();
     SendCloudToDeviceMessageAsync().Wait();
     Console.ReadLine();

8. 在 Visual Studio 中，以滑鼠右鍵按一下您的解決方案並選取 [設定啟始專案...]****。 選取 [多個啟始專案]****，然後同時針對 **ProcessDeviceToCloudMessages**、**SimulatedDevice** 以及 **SendCloudToDevice** 選取 [啟動]**** 動作。

9.  按下 **F5**，您應該會看到這三個應用程式啟動。 選取 [SendCloudToDevice]**** 視窗，然後按下 **Enter**：您應該會看到模擬應用程式收到訊息。

    ![][21]

## 接收傳遞意見反應

您可以從 IoT 中心要求每個雲端到裝置訊息的傳遞 (或到期) 通知。 如此一來，雲端後端就能輕鬆地通知重試或補償邏輯。 請參閱 [IoT 中心開發人員指南 ][iot hub developer guide - c2d] 如需有關定域機組對裝置的意見反應。

在本節中，您將修改 **SendCloudToDevice** 應用程式以要求意見反應，並接收來自 IoT 中心的意見反應。

1. 在 Visual Studio 的 **SendCloudToDevice** 專案中，將下列方法加入至 **Program** 類別。

     private async static void ReceiveFeedbackAsync()
     {
         var feedbackReceiver = serviceClient.GetFeedbackReceiver();
    
         Console.WriteLine("\nReceiving c2d feedback from service");
         while (true)
         {
             var feedbackBatch = await feedbackReceiver.ReceiveAsync();
             if (feedbackBatch == null) continue;
    
             Console.ForegroundColor = ConsoleColor.Yellow;
             Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
             Console.ResetColor();
    
             await feedbackReceiver.CompleteAsync(feedbackBatch);
         }
     }

 請注意，這裡的接收模式，與用來從裝置應用程式接收雲端到裝置訊息的模式相同。

2. 新增下列方法中的 **Main** 方法緊接著 `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` 行:

        ReceiveFeedbackAsync();

3. 為了要求針對傳遞雲端到裝置訊息的意見反應，您必須在 **SendCloudToDeviceMessageAsync** 方法中指定屬性。 之後加入這一行， `var commandMessage = 新 Message(...);` 行:

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  按下 **F5** 以執行應用程式，您應該會看到這三個應用程式啟動。 選取 [SendCloudToDevice]**** 視窗，然後按下 **Enter**：您應該會看到模擬應用程式收到訊息，並在幾秒鐘後，您的 **SendCloudToDevice** 應用程式收到意見反應訊息。

    ![][22]

> [AZURE.NOTE] 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，建議如 MSDN 文章 [暫時性錯誤處理] 所建議，實作重試原則 (例如指數型輪詢)。





[iot hub developer guide - c2d]: iot-hub-devguide.md#c2d 
[azure iot - service sdk nuget package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/ 
[transient fault handling]: https://msdn.microsoft.com/en-us/library/hh680901(v=pandp.50).aspx 
[get started with iot hub]: iot-hub-csharp-csharp-getstarted.md 
[20]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png 
[21]: ./media/iot-hub-c2d-cloud-csharp/sendc2d1.png 
[22]: ./media/iot-hub-c2d-cloud-csharp/sendc2d2.png 

