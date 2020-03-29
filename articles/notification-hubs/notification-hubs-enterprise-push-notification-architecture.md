---
title: Értesítési központok vállalati leküldéses architektúrája
description: Az Azure Értesítési központok vállalati környezetben való használatának megismerése
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 0104547a432f7f78d74731e11926bcd82088cef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264033"
---
# <a name="enterprise-push-architectural-guidance"></a>Útmutató a vállalati leküldési architektúrákhoz

A vállalatok ma fokozatosan haladnak a mobil alkalmazások létrehozása felé vagy a végfelhasználók (külső) vagy az alkalmazottak (belső) számára. Meglévő háttérrendszerekkel rendelkeznek, legyen az nagyszámítógép vagy néhány LoB alkalmazás, amelyeket integrálni kell a mobil alkalmazásarchitektúrába. Ez az útmutató bemutatja, hogyan lehet a legjobban ezt az integrációt a gyakori forgatókönyvek lehetséges megoldásának ajánlásával.

Gyakori követelmény, hogy leküldéses értesítést küld jön a felhasználók a mobilalkalmazáson keresztül, ha egy érdekes esemény történik a háttérrendszerekben. Például egy olyan banki ügyfél, aki a bank banki alkalmazását egy iPhone-on rendelkezik, értesítést szeretne kapni, ha egy terhelés egy bizonyos összeg felett történik a számláról, vagy egy intranetes forgatókönyv, amikor a pénzügyi osztály on windows phone-on költségvetési jóváhagyási alkalmazással rendelkező alkalmazottja a jóváhagyási kérelem beérkezésekor.

A bankszámla vagy a jóváhagyás feldolgozása valószínűleg néhány háttérrendszerben történik, amelynek leküldéses kezdeményezést kell kezdeményeznie a felhasználó számára. Előfordulhat, hogy több ilyen háttérrendszerek, amelyek mindegyike létre kell hoznia az azonos típusú logika leküldéses, ha egy esemény esemény indítókon egy értesítést. A komplexitás itt abban rejlik, hogy integrálja több háttérrendszerek együtt egy push rendszer, ahol a végfelhasználók is feliratkozott a különböző értesítéseket, és ott is lehet több mobil alkalmazások. Például intranetes mobilalkalmazások, ahol egy mobilalkalmazás több ilyen háttérrendszertől is szeretne értesítéseket kapni. A háttérrendszerek nem ismerik, vagy tudniuk kell a push szemantika / technológia, így a közös megoldás itt hagyományosan az volt, hogy vezessenek be egy összetevő, amely lekérdezi a háttérrendszerek minden érdekes esemény, és felelős a push üzenetek küldése az ügyfélnek.

Egy jobb megoldás az Azure Service Bus – Topic/Subscription modell használata, amely csökkenti a bonyolultságot, miközben a megoldás méretezhetővé válik.

Itt van a megoldás általános architektúrája (több mobilalkalmazással általánosítva, de egyformán alkalmazható, ha csak egy mobilalkalmazás van)

## <a name="architecture"></a>Architektúra

![][1]

Az architekturális diagram legfontosabb eleme az Azure Service Bus, amely témakörök/előfizetések programozási modelljét biztosítja (erről bővebben a [Service Bus Pub/Sub programozásnál).] A fogadó, amely ebben az esetben a mobil háttérrendszer (általában [az Azure Mobile Service,]amely kezdeményezi a leküldéses a mobilalkalmazások) nem fogad üzeneteket közvetlenül a háttérrendszerek, hanem egy köztes absztrakciós réteg által biztosított [Azure Service Bus,]amely lehetővé teszi a mobil háttérrendszer üzenetek fogadására egy vagy több háttérrendszerek. A Szolgáltatásbusz-témakört létre kell hozni az egyes háttérrendszerekhez, például a fiókhoz, a HR-hez, a pénzügyekhez, amely alapvetően "érdeklődésre számot tartó témakörök", amelyek leküldéses értesítésként küldendő üzeneteket kezdeményeznek. A háttérrendszerek üzeneteket küldenek ezekre a témakörökre. A mobile backend egy vagy több ilyen témakörre előfizethet egy Service Bus-előfizetés létrehozásával. Feljogosítja a mobil háttérrendszert arra, hogy értesítést kapjon a megfelelő háttérrendszertől. A mobil háttérrendszer továbbra is figyeli az előfizetéseikben lévő üzeneteket, és amint egy üzenet megérkezik, visszafordul, és értesítést küld az értesítési központnak. Az értesítési központok ezután végül kézbesítik az üzenetet a mobilalkalmazásnak. Itt van a lista a legfontosabb összetevők:

1. Háttérrendszerek (LoB/Legacy rendszerek)
   * Létrehoz szolgáltatásbusz-témakört
   * Üzenet küldése
1. Mobil háttérkapcsolat
   * Szolgáltatás-előfizetés létrehozása
   * Üzenet fogadása (háttérrendszerről)
   * Értesítés küldése az ügyfeleknek (az Azure Értesítési központon keresztül)
1. Mobilalkalmazás
   * Értesítések fogadása és megjelenítése

### <a name="benefits"></a>Előnyök

1. A vevő (mobilalkalmazás/szolgáltatás értesítési központon keresztül) és a küldő (háttérrendszerek) közötti leválasztás lehetővé teszi, hogy további háttérrendszereket integráljon minimális változással.
1. Azt is lehetővé teszi, hogy több mobilalkalmazás képes eseményekfogadására egy vagy több háttérrendszerből.  

## <a name="sample"></a>Sample

### <a name="prerequisites"></a>Előfeltételek

Végezze el az alábbi oktatóanyagokat a fogalmak megismeréséhez, valamint a gyakori létrehozási & konfigurációs lépésekhez:

1. [Service Bus Pub/Sub programozás] – Ez az oktatóanyag ismerteti a Service Bus-témakörök/előfizetések munkarészleteit, hogyan hozhat létre névteret témakörök/előfizetések tárolására, hogyan küldhet & fogadhat üzeneteket tőlük.
2. [Értesítési központok – Windows Univerzális oktatóanyag] – Ez az oktatóanyag bemutatja, hogyan állíthat be windows Áruházbeli alkalmazást, és hogyan használhatja az értesítési központokat a regisztrációhoz, majd az értesítések fogadásához.

### <a name="sample-code"></a>Mintakód

A teljes mintakód elérhető az [Értesítési központ mintái]nál. Három részre oszlik:

1. **EnterprisePushBackendSystem**

    a. Ez a projekt a **WindowsAzure.ServiceBus** NuGet csomagot használja, és a [Service Bus Pub/Sub programozáson]alapul.

    b. Ez az alkalmazás egy egyszerű C# konzol alkalmazás szimulálni lob rendszer, amely elindítja az üzenetet kell kézbesíteni a mobilalkalmazásba.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    c. `CreateTopic`a Service Bus témakör létrehozásához használható.

    ```csharp
    public static void CreateTopic(string connectionString)
    {
        // Create the topic if it does not exist already

        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.TopicExists(sampleTopic))
        {
            namespaceManager.CreateTopic(sampleTopic);
        }
    }
    ```

    d. `SendMessage`az üzenetek küldésére szolgál erre a Service Bus-témakörre. Ez a kód egyszerűen küld egy sor véletlenszerű üzeneteket a témakör rendszeresidőközönként céljából a minta. Általában van egy háttérrendszer, amely üzeneteket küld, ha egy esemény bekövetkezik.

    ```csharp
    public static void SendMessage(string connectionString)
    {
        TopicClient client =
            TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

        // Sends random messages every 10 seconds to the topic
        string[] messages =
        {
            "Employee Id '{0}' has joined.",
            "Employee Id '{0}' has left.",
            "Employee Id '{0}' has switched to a different team."
        };

        while (true)
        {
            Random rnd = new Random();
            string employeeId = rnd.Next(10000, 99999).ToString();
            string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

            // Send Notification
            BrokeredMessage message = new BrokeredMessage(notification);
            client.Send(message);

            Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

            System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
        }
    }
    ```
2. **Értesítés fogadása és küldése**

    a. Ez a projekt a *WindowsAzure.ServiceBus* és a **Microsoft.Web.WebJobs.Publish** NuGet csomagokat használja, és a [Service Bus Pub/Sub programozáson]alapul.

    b. A következő konzolalkalmazás [Azure WebJob] néven fut, mivel folyamatosan kell futnia a LoB/háttérrendszerekből érkező üzenetek figyeléséhez. Ez az alkalmazás része a mobil háttér.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    c. `CreateSubscription`A Service Bus-előfizetés létrehozására szolgál ahhoz a témakörhöz, ahol a háttérrendszer üzeneteket küld. Az üzleti forgatókönyvtől függően ez az összetevő egy vagy több előfizetést hoz létre a megfelelő témakörökhöz (például néhányan hr-rendszerből, néhány a pénzügyi rendszerből stb.)

    ```csharp
    static void CreateSubscription(string connectionString)
    {
        // Create the subscription if it does not exist already
        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
        {
            namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
        }
    }
    ```

    d. `ReceiveMessageAndSendNotification`az üzenet olvasására szolgál a témakörelőfizetéshasználatával, és ha az olvasás sikeres, majd a craft egy értesítést (a minta forgatókönyvében a Windows natív bejelentési értesítés) kell küldeni a mobilalkalmazás az Azure Notification Hubs használatával.

    ```csharp
    static void ReceiveMessageAndSendNotification(string connectionString)
    {
        // Initialize the Notification Hub
        string hubConnectionString = CloudConfigurationManager.GetSetting
                ("Microsoft.NotificationHub.ConnectionString");
        hub = NotificationHubClient.CreateClientFromConnectionString
                (hubConnectionString, "enterprisepushservicehub");

        SubscriptionClient Client =
            SubscriptionClient.CreateFromConnectionString
                    (connectionString, sampleTopic, sampleSubscription);

        Client.Receive();

        // Continuously process messages received from the subscription
        while (true)
        {
            BrokeredMessage message = Client.Receive();
            var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

            if (message != null)
            {
                try
                {
                    Console.WriteLine(message.MessageId);
                    Console.WriteLine(message.SequenceNumber);
                    string messageBody = message.GetBody<string>();
                    Console.WriteLine("Body: " + messageBody + "\n");

                    toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                    SendNotificationAsync(toastMessage);

                    // Remove message from subscription
                    message.Complete();
                }
                catch (Exception)
                {
                    // Indicate a problem, unlock message in subscription
                    message.Abandon();
                }
            }
        }
    }
    static async void SendNotificationAsync(string message)
    {
        await hub.SendWindowsNativeNotificationAsync(message);
    }
    ```

    e. Az alkalmazás **WebJob**ként való közzétételéhez kattintson a jobb gombbal a megoldásra a Visual Studio alkalmazásban, és válassza a **Közzététel webjobként parancsot.**

    ![][2]

    f. Válassza ki a közzétételi profilt, és hozzon létre egy új Azure-webhelyet, ha még nem létezik, amely a webmunkát tartalmazza, és miután rendelkezik a webhellyel, majd **tegye közzé.**

    ![][3]

    g. Állítsa be a feladatot "Folyamatos futtatás" állapotúra, így amikor bejelentkezik az [Azure Portalra,] a következőhöz hasonló tetszőnek kell lennie:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Ez az alkalmazás egy Windows Áruházbeli alkalmazás, amely bejelentési értesítéseket kap a WebJob-tól, amely a mobil háttérrendszer részeként fut, és megjeleníti azt. Ez a kód az [Értesítési központok – Windows Universal oktatóanyagon]alapul.  

    b. Győződjön meg arról, hogy az alkalmazás engedélyezve van a bejelentési értesítések fogadásához.

    c. Győződjön meg arról, hogy a következő értesítési központok regisztrációs `HubName` `DefaultListenSharedAccessSignature` kódja van meghívva az alkalmazás indításakor (az értékek és értékek cseréje után:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

### <a name="running-the-sample"></a>A minta futtatása

1. Győződjön meg arról, hogy a WebJob sikeresen fut, és folyamatos futtatásra van ütemezve.
2. Futtassa az **EnterprisePushMobileApp**alkalmazást, amely elindítja a Windows Áruházbeli alkalmazást.
3. Futtassa az **EnterprisePushBackendSystem** konzolalkalmazást, amely szimulálja a LoB háttérrendszert, és elkezdi az üzenetek küldését, és a bejelentési értesítések nek az alábbi képen láthatónak kell lenniük:

    ![][5]

4. Az üzeneteket eredetileg a Service Bus-témakörök, amely et figyelt a Service Bus-előfizetések a webfeladatban. Az üzenet fogadását követően létrejött egy értesítés, amelyet a mobilalkalmazás küldött. A WebJob-naplókban ellenőrizheti a feldolgozást, amikor a Webjobához az [Azure Portalnaplók] hivatkozásra lép:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Értesítési központ-minták]: https://github.com/Azure/azure-notificationhubs-samples
[Azure mobilszolgáltatás]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Service Bus Pub/Sub programozás]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: ../app-service/webjobs-create.md
[Értesítési központok – Windows Universal oktatóanyag]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure-portál]: https://portal.azure.com/
