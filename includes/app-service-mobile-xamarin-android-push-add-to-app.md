1. Hozzon létre egy új osztályt a projekt neve `ToDoBroadcastReceiver`.
2. Adja hozzá a következő using utasításokat a **ToDoBroadcastReceiver** osztály:
   
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
3. Adja hozzá a következő engedélykéréseket között a **használatával** utasítások és a **névtér** deklarációjában:
   
        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
   
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
4. Cserélje le a meglévő **ToDoBroadcastReceiver** osztály definícióját a következőre:
   
        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
        Categories = new string[] { "@PACKAGE_NAME@" })]
        public class ToDoBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            // Set the Google app ID.
            public static string[] senderIDs = new string[] { "<PROJECT_NUMBER>" };
        }
   
    A fenti kódban, le kell cserélnie  *`<PROJECT_NUMBER>`*  Google által hozzárendelt, amikor az alkalmazást a Google fejlesztői portálján a kiépített projekt számát. 
5. A ToDoBroadcastReceiver.cs projekt fájlban adja hozzá az alábbi kódot, amely meghatározza a **PushHandlerService** osztály:
   
        // The ServiceAttribute must be applied to the class.
        [Service] 
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
   
            public PushHandlerService() : base(ToDoBroadcastReceiver.senderIDs) { }
        }
   
    Vegye figyelembe, hogy ez az osztály származik **GcmServiceBase** , és hogy a **szolgáltatás** Ez az osztály attribútumot kell alkalmazni.
   
   > [!NOTE]
   > A **GcmServiceBase** osztály megvalósítja a **OnRegistered()**, **OnUnRegistered()**, **OnMessage()** és  **OnError()** módszerek. Ezek a módszerek felül kell bírálnia a **PushHandlerService** osztály.
   > 
   > 
6. Adja hozzá a következő kódot a **PushHandlerService** osztály, amely felülbírálja a **OnRegistered** eseménykezelő. 
   
        protected override void OnRegistered(Context context, string registrationId)
        {
            System.Diagnostics.Debug.WriteLine("The device has been registered with GCM.", "Success!");
   
            // Get the MobileServiceClient from the current activity instance.
            MobileServiceClient client = ToDoActivity.CurrentActivity.CurrentClient;
            var push = client.GetPush();
   
            // Define a message body for GCM.
            const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
   
            // Define the template registration as JSON.
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
              {"body", templateBodyGCM }
            };
   
            try
            {
                // Make sure we run the registration on the same thread as the activity, 
                // to avoid threading errors.
                ToDoActivity.CurrentActivity.RunOnUiThread(
   
                    // Register the template with Notification Hubs.
                    async () => await push.RegisterAsync(registrationId, templates));
   
                System.Diagnostics.Debug.WriteLine(
                    string.Format("Push Installation Id", push.InstallationId.ToString()));
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(
                    string.Format("Error with Azure push registration: {0}", ex.Message));
            }
        }
   
    Ez a metódus visszaadott GCM regisztrációs Azonosítót használja az Azure-ral leküldéses értesítések regisztrálása. Címkék csak felveheti a regisztrációt a létrehozás után. További információkért lásd: [hogyan: címkék hozzáadása egy eszköz telepítése leküldéses-címkék engedélyezése](../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags).
7. Bírálja felül a **OnMessage** metódus a **PushHandlerService** az alábbi kódra:
   
       protected override void OnMessage(Context context, Intent intent)
       {          
           string message = string.Empty;
   
           // Extract the push notification message from the intent.
           if (intent.Extras.ContainsKey("message"))
           {
               message = intent.Extras.Get("message").ToString();
               var title = "New item added:";
   
               // Create a notification manager to send the notification.
               var notificationManager = 
                   GetSystemService(Context.NotificationService) as NotificationManager;
   
               // Create a new intent to show the notification in the UI. 
               PendingIntent contentIntent = 
                   PendingIntent.GetActivity(context, 0, 
                   new Intent(this, typeof(ToDoActivity)), 0);              
   
               // Create the notification using the builder.
               var builder = new Notification.Builder(context);
               builder.SetAutoCancel(true);
               builder.SetContentTitle(title);
               builder.SetContentText(message);
               builder.SetSmallIcon(Resource.Drawable.ic_launcher);
               builder.SetContentIntent(contentIntent);
               var notification = builder.Build();
   
               // Display the notification in the Notifications Area.
               notificationManager.Notify(1, notification);
   
           }
       }
8. Bírálja felül a **OnUnRegistered()** és **OnError()** módszereket az alábbi kódra.
   
       protected override void OnUnRegistered(Context context, string registrationId)
       {
           throw new NotImplementedException();
       }
   
       protected override void OnError(Context context, string errorId)
       {
           System.Diagnostics.Debug.WriteLine(
               string.Format("Error occurred in the notification: {0}.", errorId));
       }

