
1. A megoldás nézetben (vagy **Megoldáskezelőben** a Visual Studio), kattintson a jobb gombbal a **összetevők** mappát, kattintson a **további összetevők beszerzése...** , keresse meg a **Google Cloud Messaging Client** összetevő, és adja hozzá a projekthez.
2. Nyissa meg a ToDoActivity.cs projektfájlt, majd adja hozzá a következő using utasítást az osztályhoz:
   
        using Gcm.Client;
3. Az a **ToDoActivity** osztály, adja hozzá a következő új kódot: 
   
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
   
    Ez lehetővé teszi a mobil ügyfél-példány a leküldéses kezelő szolgáltatás folyamatának eléréséhez.
4. Adja hozzá a következő kódot a **OnCreate** módszer, utána a **MobileServiceClient** jön létre:
   
       // Set the current instance of TodoActivity.
       instance = this;
   
       // Make sure the GCM client is set up correctly.
       GcmClient.CheckDevice(this);
       GcmClient.CheckManifest(this);
   
       // Register the app for push notifications.
       GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

A **ToDoActivity** most felkészül a leküldéses értesítések hozzáadása.

