
1. 在 [方案] 檢視中 (或 **方案總管] 中** Visual Studio 中)，以滑鼠右鍵按一下 **元件** 資料夾中，按一下 [  **取得更多元件...**, ，搜尋 **Google Cloud Messaging 用戶端** 元件並將它加入至專案。

2. 開啟 ToDoActivity.cs 專案檔案，然後將下列 using 陳述式加入至類別：

        using Gcm.Client;

3. 在 **ToDoActivity** 類別中，新增下列新程式碼: 

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

    這樣做可讓您從推播處理常式服務處理程序存取行動服務用戶端執行個體。

4.  加入下列程式碼以 **OnCreate** 方法之後， **MobileServiceClient** 建立:

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

您 **ToDoActivity** 現在已準備好供新增推播通知。
