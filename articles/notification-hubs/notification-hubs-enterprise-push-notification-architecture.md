---
title: Notification Hubs – vállalati leküldéses architektúra
description: Útmutató az Azure Notification Hubs használatával nagyvállalati környezetben
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: c8e204aef8bea26394c7180a72eb8ed8f62bbdc4
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447258"
---
# <a name="enterprise-push-architectural-guidance"></a>Útmutató a vállalati leküldési architektúrákhoz

Vállalatok ma olyan fokozatosan helyez át, vagy az alkalmazottak számára (belső) felé vagy mobilalkalmazások létrehozása a saját végfelhasználóik (külső). Meglévő háttérrendszer rendszerek legyen Nagyszámítógépek vagy bizonyos LoB-alkalmazások, amelyek integrálni kell a mobilalkalmazás-architektúra rendelkeznek. Ez az útmutató ismerteti a lehetséges megoldást javasolja a gyakori forgatókönyvek integrálni a legjobb módja.

Egy gyakori követelmény, a leküldéses értesítés küldését a felhasználókat a mobilalkalmazás keresztül, a háttér-rendszereken a lényeges események bekövetkezésekor. például egy banki ügyfelet, aki a bank banki alkalmazást az iPhone-on szeretné értesíti, ha tartozik a fiók vagy egy intranetes forgatókönyv, ahol a költségvetés jóváhagyási alkalmazás rendelkezik egy Windows Phone egy alkalmazott a pénzügyi részleg szeretné egy adott időtartamot a fentiekben  Ha a jóváhagyási kérelem érkezik, értesítést.

A fiók létrehozása és a jóváhagyási feldolgozási valószínűleg elvégezni egyes háttérrendszer rendszer, amely egy leküldéses a felhasználónak kell kezdeményeznie. Előfordulhat, hogy több ilyen háttérrendszerekhez, felépítési kell az összes ugyanolyan típusú logikai leküldéses értesítést az esemény aktiválásakor. Itt az összetettséget több háttérrendszerre együtt egy egyetlen leküldéses rendszer, ahol különböző értesítések előfordulhat, hogy feliratkozott a végfelhasználók számára, és még lehet több mobil alkalmazások integrálása az rejlik. Ha például intranetes mobilalkalmazások, egy mobilalkalmazás előfordulhat, hogy szeretne értesítéseket kapni az ilyen háttérrendszer több rendszer. A háttérrendszer rendszerek nem ismeri, vagy hogy ismernie kell a leküldéses szemantika/technológia gyakori megoldás volt egy összetevő, amely lekérdezi a háttérrendszer rendszerek bármely események számára, és a leküldéses üzenetek küldéséért felelős bevezetni a ügyfél.

Jobb megoldás, hogy az Azure Service Bus - Üzenettémák/előfizetések modellt használ, ami csökkenti az összetettséget közben a megoldás méretezhető.

Itt látható a megoldás az általános architektúrája, (több mobilalkalmazásokkal általánosított, de egyaránt érvényes, ha csak egy mobilalkalmazás)

## <a name="architecture"></a>Architektúra

![][1]

A legfontosabb, ez az architekturális ábra az Azure Service Bus, a témakörök/előfizetések programozási modell biztosító (a további [Service Bus Pub/Sub programozás]). A fogadó, amely ebben az esetben a mobil háttérszolgáltatásban (általában [Azure Mobile Service], amely kezdeményezi a mobilalkalmazások leküldéses) nem fogad üzeneteket közvetlenül a háttérkiszolgálón rendszerek azonban ehelyett egy köztes absztrakciós a réteg által biztosított [Azure Service Bus], amely lehetővé teszi, hogy a mobil háttérszolgáltatásban való fogad üzeneteket egy vagy több háttérrendszerre. A Service Bus-témakör kell létrehozni minden, a háttér-rendszerek, például a fiók, HR, pénzügy, amely alapvetően egy hasznos helyhez, amely kezdeményezi az üzenetek, leküldéses értesítés küldését a "témakörök". A háttérrendszer rendszerek üzeneteket küldenek az ezekről a témákról. Egy mobil háttérrendszer lehet témákra iratkozhat fel egy vagy több ilyen hoz létre a Service Bus-előfizetés. Ez feljogosítja a is megkapja az értesítéseket a megfelelő háttér rendszerből a mobil háttérszolgáltatás használatára. Mobil háttérszolgáltatás használatára továbbra is az azok az előfizetések üzeneteit és, amint egy üzenet érkezik, akkor kapcsolja vissza, és elküldi azokat a értesítés az értesítési központhoz. A Notification hubs majd végül kézbesíteni az üzenetet a mobilalkalmazásban. A következő kulcsfontosságú összetevők listáját:

1. A háttérrendszerek (üzletági/örökölt rendszerek)
   * Service Bus-témakör létrehozása
   * Üzenet küldése
1. Mobil háttérszolgáltatás
   * Szolgáltatás-előfizetést hoz létre
   * Üzenet fogadása (a háttérrendszer)
   * Értesítést küld az ügyfelek (keresztül az Azure Notification Hub)
1. Mobilalkalmazás
   * Kap, és értesítés megjelenítése

### <a name="benefits"></a>Előnyök

1. A fogadó (mobile alkalmazás/szolgáltatás Notification Hub-n keresztül) és a feladó (háttérrendszer rendszerekhez) közötti elválasztás lehetővé teszi, hogy további háttérrendszerre minimális módosítása folyamatban van integrálva.
1. A forgatókönyv képes arra, hogy az események fogadása az egy vagy több háttérrendszerre több mobilalkalmazások is teszi.  

## <a name="sample"></a>Sample

### <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az alábbi oktatóanyagok a fogalmak, valamint általános létrehozási & konfigurációs megoldások megismerkednie:

1. [Service Bus Pub/Sub programozás] – Ez az oktatóanyag ismerteti a Service Bus-témakörök/előfizetések, együttműködés részleteit tartalmazó témakörök/előfizetések, a névtér létrehozása hogyan üzenet fogadása & küldése.
2. [Notification Hubs – Windows Universal oktatóanyag] – Ez az oktatóanyag azt ismerteti, hogyan állítsa be a Windows Store alkalmazást és regisztrálja, és ezután kapni az értesítések a Notification Hubs használatával.

### <a name="sample-code"></a>Mintakód

A teljes mintakód elérhető legyen [Notification Hub-minták]. Mindhárom összetevő van felosztva:

1. **EnterprisePushBackendSystem**

    a. A projekt által használt a **WindowsAzure.ServiceBus** NuGet-csomagot és alapján [Service Bus Pub/Sub programozás].

    b. Ez az alkalmazás egy egyszerű C#-konzolalkalmazást egy LoB rendszerekhez, amely kezdeményezi a mobilalkalmazás küldendő üzenet szimulálásához.

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

    c. `CreateTopic` a Service Bus-témakörbe létrehozására szolgál.

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

    d. `SendMessage` az üzenetek küldése a Service Bus-témakör szolgál. Ez a kód egyszerűen elküld egy véletlenszerű üzenetkészletet rendszeres időközönként céljából a minta a témakörbe. Általában nincs háttérrendszer, amely üzeneteket küld egy esemény bekövetkeztekor.

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
2. **ReceiveAndSendNotification**

    a. A projekt által használt a *WindowsAzure.ServiceBus* és **Microsoft.Web.WebJobs.Publish** NuGet csomagot, és alapul [Service Bus Pub/Sub programozás].

    b. A következő Konzolalkalmazás fut, egy [Azure WebJob] mivel folyamatosan figyelni a LoB-/ háttérrendszerekhez érkező üzeneteket a futtatásához rendelkezik. Ez az alkalmazás a mobil háttérszolgáltatásban részét képezi.

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

    c. `CreateSubscription` segítségével hozzon létre egy Service Bus-előfizetést a témakör arról, hogy az a háttérrendszer hová küldi az üzeneteket. Az üzleti forgatókönyv függően ez az összetevő hoz létre egy vagy több előfizetés megfelelő témákra (például néhány lehetséges, hogy lehet üzenetek fogadása a HR-rendszerbe, néhány, a pénzügyi rendszer, és így tovább)

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

    d. `ReceiveMessageAndSendNotification` olvassa el az üzenetet a témakörből az előfizetést, és ha az olvasás sikeres majd formázhatják (az a forgatókönyv esetén a Windows natív bejelentési értesítés) értesítést kell küldeni az Azure Notification Hubs használatával mobilalkalmazás szolgál.

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

    e. Ez az alkalmazás közzétételéhez egy **webjobs-feladat**, kattintson a megoldást a Visual Studióban a jobb gombbal, és válassza ki **zzététel webjobs-feladat**

    ![][2]

    f. Válassza ki a közzétételi profilt, és a egy új Azure-webhely létrehozása, ha még nem létezik már, amely a webjobs-feladat, és a webhely majd miután **közzététel**.

    ![][3]

    g. "Folyamatos Futtatás" kell, hogy amikor bejelentkezik a feladat konfigurálása a [Azure Portal] valamit a következőhöz hasonlóan kell megjelennie:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Ez az alkalmazás egy Windows Store-alkalmazás, amely bejelentési értesítéseket fogad a mobil háttérszolgáltatásban részeként futó webjobs-feladatot, és megjeleníti azt. Ez a kód alapján [Notification Hubs – Windows Universal oktatóanyag].  

    b. Győződjön meg arról, hogy az alkalmazás engedélyezve van-e bejelentési értesítéseket fogadni.

    c. Győződjön meg arról, hogy a következő Notification Hubs regisztrációs kódot meghívva, az alkalmazás indítása (miután kicserélte a `HubName` és `DefaultListenSharedAccessSignature` értékeket:

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

1. Győződjön meg arról, hogy a webjobs-feladat megfelelően fut, és folyamatosan futtatott, ütemezett.
2. Futtassa a ** EnterprisePushMobileApp, amely elindítja a Windows Store app.
3. Futtassa a **EnterprisePushBackendSystem** konzolalkalmazásra, amely szimulálja a LoB-háttérrendszerhez, és elindítja a küld üzeneteket, és a bejelentési értesítések jelennek meg a következő képhez hasonlóan kell megjelennie:

    ![][5]

4. Az üzenetek Service Bus-témakörök, amelyek a Service Bus-előfizetések a Webjobs-feladat által figyelt eredetileg küldtek. Üzenet érkezett, miután értesítést létrehozásának és a mobilalkalmazásnak küldött. Áttekintheti a WebJob-naplókat, amikor a naplók hivatkozására, győződjön meg arról, hogy a feldolgozás [Azure Portal] a Webjobs-feladat esetében:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Notification Hub-minták]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Service]: http://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Service Bus Pub/Sub programozás]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: ../app-service/webjobs-create.md
[Notification Hubs – Windows Universal oktatóanyag]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure Portal]: https://portal.azure.com/
