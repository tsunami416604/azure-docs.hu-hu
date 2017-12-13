---
title: "A Notification Hubs - vállalati leküldéses architektúrája"
description: "Útmutató az Azure Notification Hubs használatával vállalati környezetben"
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: c626d6415a27f8495304eeaab480ab62606102ea
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="enterprise-push-architectural-guidance"></a>Útmutató a vállalati leküldési architektúrákhoz
A vállalatok ma egyre fokozatosan áthelyezése felé létrehozása vagy az alkalmazásokat a végfelhasználók számára (külső) vagy (belső) az alkalmazottak számára. Létező háttérrendszerek helyen kell azt Nagyszámítógépek vagy néhány LoB-alkalmazások, amelyek integrálni kell a mobilalkalmazás-architektúra rendelkeznek. Ez az útmutató előadás hogyan legjobb, ha ez az integráció ajánló lehetséges megoldás a gyakori forgatókönyvek.

Gyakori követelmény van a leküldéses értesítések küldését a felhasználók számára a mobilalkalmazás keresztül, a háttér-rendszer egyik fontos esemény bekövetkezésekor. Például egy bank ügyfél, a bank banki alkalmazást az iPhone rendelkező szeretne értesítést tartozik szeretné tenni saját fiók vagy egy intranetes forgatókönyvet, ahol a pénzügyi részleg egy alkalmazott, a költségvetési jóváhagyást alkalmazások a Windows Phone rendelkező szeretné nem lehet egy bizonyos összeg felett Ha a jóváhagyási kérelem kapjuk egadott.

A banki vagy jóváhagyási feldolgozási valószínű, hogy néhány háttérrendszer, amely egy leküldéses a felhasználónak kell kezdeményeznie végezhető. Előfordulhat, hogy több ilyen kell az összes build az eseményt akkor váltja ki egy értesítést leküldéses megvalósítási logika ugyanolyan típusú háttér rendszereket. Itt összetettségét létrejönnie egy egyetlen leküldéses rendszert, ahol lehetséges, hogy a végfelhasználók számára különböző értesítések előfizetett és még akkor is előfordulhat, például intranet mobilalkalmazások esetében több mobilalkalmazások Ha az egyik több háttérrendszerek integrálása mobilalkalmazás előfordulhat, hogy értesítést szeretne kapni a több ilyen háttérrendszerek. A háttérrendszerek nem ismeri vagy tudniuk kell, hogy leküldéses szemantikáját/technológia, így a közös megoldást-ra volt egy összetevő, amely lekérdezi a háttérrendszerek bármely események számára, és a leküldéses üzenetek küldéséért felelős bevezetni a az ügyfél.
Itt előadás egy használó Azure Service Bus - témakört/előfizetést modell, amely csökkenti a összetettségét a megoldás méretezhető tétele közben is jobb megoldás.

Ez a megoldás általános architektúrájának (több mobilalkalmazásokkal általános, de egyaránt alkalmazható, ha csak egy mobilalkalmazás)

## <a name="architecture"></a>Architektúra
![][1]

A kulcs darab a architekturális diagram Azure Service Bus, amely a témakörök/előfizetések programozási modellt biztosít (további sablonokat a [Service Bus Pub/Sub programozási]). A fogadó, amely ebben az esetben a mobil-háttéralkalmazást (általában [Azure Mobile Services mobilszolgáltatás], amely indít el a mobilalkalmazások leküldéses) kapott üzenetek közvetlenül a háttérrendszerről, de ehelyett egy köztes tudunk hardverabsztrakciós réteg által biztosított [Azure Service Bus] lehetővé teszi a mobil-háttéralkalmazást üzenetek fogadása egy vagy több háttérrendszerek. A Service Bus-témakörbe kell létrehozni az egyes a háttérrendszerek pl. fiók HR, pénzügyi, amelyek alapvetően "témakörök" iránt, amelyek indít el, leküldéses értesítést küldeni. A háttérrendszerek témákhoz üzenetet szeretne küldeni. A mobil-háttéralkalmazás kérhet le egy vagy több ilyen témakörök által a Service Bus-előfizetés létrehozása. Ez feljogosítja a mobil-háttéralkalmazást is megkapja az értesítéseket a megfelelő háttér rendszerből. Mobil-háttéralkalmazást továbbra is az üzeneteket az előfizetések és, amint egy üzenet érkezik, akkor kapcsolja vissza, és elküldi az értesítési központba értesítésként. A Notification hubs majd végül kézbesíti az üzenetet a mobilalkalmazásban. Úgy, hogy összesítse a legfontosabb összetevők, vezetünk be:

1. A háttérrendszerek (LoB/örökölt rendszerek)
   * Service Bus-témakörbe létrehozása
   * Üzenet küldése
2. Mobil háttérszolgáltatás
   * Szolgáltatás-előfizetést hoz létre
   * Üzenet fogadása (a háttérrendszer)
   * Értesítés küldése (az Azure Notification Hub) keresztül ügyfelek
3. Mobilalkalmazás
   * Kap, és értesítést jelenít meg

### <a name="benefits"></a>Előnyei:
1. A fogadó (app/mobilszolgáltatás Notification Hub használatával) és a feladó (háttérrendszerek) közötti leválasztásával lehetővé teszi, hogy további háttérrendszerek integrálva van a minimális módosítása.
2. Így a több mobilalkalmazások tudnak majd egy vagy több háttérrendszerről származó események fogadására a forgatókönyvet.  

## <a name="sample"></a>Minta:
### <a name="prerequisites"></a>Előfeltételek
El kell végeznie az alábbi oktatóanyagok megismerkednie a fogalmakat, valamint közös létrehozása és konfigurációs lépéseket:

1. [Service Bus Pub/Sub programozási] -ebben a részben működik-e a Service Bus témakörök/előfizetések, részleteit tartalmazó témakörök/előfizetésekre, a névtér létrehozása hogyan üzeneteket fogadjon & küldése.
2. [A Notification Hubs - oktatóanyag univerzális Windows-] -ezt ismerteti, hogyan állítson be egy Windows Áruházbeli alkalmazást és regisztrálja, és ezután kapni az értesítések a Notification Hubs használatával.

### <a name="sample-code"></a>Mintakód
A teljes minta kódja megtalálható [Notification Hub minták]. A oszlik három összetevővel:

1. **EnterprisePushBackendSystem**
   
    a. Ez a projekt használja a *WindowsAzure.ServiceBus* Nuget-csomag alapozva [Service Bus Pub/Sub programozási].
   
    b. Ez az egy egyszerű C# Konzolalkalmazás egy LoB szimulálására kezdeményező a mobilalkalmazás küldendő üzenet.
   
        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the topic where we will send notifications
            CreateTopic(connectionString);
   
            // Send message
            SendMessage(connectionString);
        }
   
    c. `CreateTopic`létrehozásához használt a Service Bus-témakörbe ahol üzenetet küldünk.
   
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
   
    d. `SendMessage`az üzenetek küldése a Service Bus-témakörbe szolgál. Itt azt egyszerűen küldenek véletlenszerű üzenetek készleteit a témakörhöz, rendszeres időközönként a minta céljából. Általában nem lesznek egy háttér-rendszert, amely üzenetet szeretne küldeni, ha az esemény akkor következik be.
   
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
2. **ReceiveAndSendNotification**
   
    a. Ez a projekt használja a *WindowsAzure.ServiceBus* és *Microsoft.Web.WebJobs.Publish* Nuget a csomagok és alapuló [Service Bus Pub/Sub programozási].
   
    b. Ez az egy másik C# konzolalkalmazást, amely jelenleg fut egy [Azure webjobs-feladat] mivel folyamatosan figyelni a LoB-/ háttérrendszerek üzeneteit futtatásához rendelkezik. Ez a mobil-háttéralkalmazást része lesz.
   
        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the subscription which will receive messages
            CreateSubscription(connectionString);
   
            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }
   
    c. `CreateSubscription`segítségével hozzon létre egy Service Bus-előfizetést, a témakör ahol a háttérrendszer fog üzeneteket küldeni. Attól függően, hogy az üzleti terület Ez az összetevő hoz létre egy vagy több előfizetés (pl. néhány lehetséges, hogy lehet üzenetek fogadása HR rendszer, egyes pénzügyi rendszer, és így tovább) kapcsolódó témakörök
   
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
   
    d. Az üzenet olvasni a témakör az előfizetést, és ha az olvasás sikeres majd kézműipari (forgatókönyvében a minta egy Windows natív bejelentési értesítés) értesítést a mobilalkalmazás használata Azure küldendő ReceiveMessageAndSendNotification szolgál Notification hubs használatával.
   
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
   
    e. Közzététel ezt egy **webjobs-feladat**, a Visual Studio-megoldásban kattintson a jobb gombbal, és válassza ki **webjobs-feladat közzététel**
   
    ![][2]
   
    f. Válassza ki a közzétételi profilt, és hozzon létre egy új Azure-webhelyre, ha még nem létezik már fogja tárolni, amely a webjobs-feladat, és ha a webhely majd **közzététel**.
   
    ![][3]
   
    g. A "Folyamatos Futtatás" kell, hogy mikor jelentkezik be a feladat a [Azure-portálon] látnia kell a következőhöz:
   
    ![][4]
3. **EnterprisePushMobileApp**
   
    a. Ez az egy Windows Áruházbeli alkalmazással, amely bejelentési értesítések fogadása a webjobs-feladat a mobil-háttéralkalmazást részeként fut, és megjeleníti azt. Ez a alapul [A Notification Hubs - oktatóanyag univerzális Windows-].  
   
    b. Győződjön meg arról, hogy az alkalmazás engedélyezve van-e bejelentési értesítéseket fogadni.
   
    c. Győződjön meg arról, hogy a következő Notification Hubs regisztrációs kód lett meghívva, az alkalmazás elindításához (miután kicserélte a *HubName* és *DefaultListenSharedAccessSignature*:
   
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

### <a name="running-sample"></a>A minta fut:
1. Győződjön meg arról, hogy a webjobs-feladat sikeresen fut, és a "Folyamatos Futtatás" ütemezett.
2. Futtassa a **EnterprisePushMobileApp** amely indul el a Windows áruház egy alkalmazásához.
3. Futtassa a **EnterprisePushBackendSystem** konzolalkalmazást, amely szimulálni fogja a LoB háttér és küldésének megkezdése üzeneteket, és a következő megjelenő bejelentési értesítést kell megjelennie:
   
    ![][5]
4. Service Bus-témaköröktől, amely a Service Bus-előfizetések műveleteit a webes projekt által figyelt eredetileg elküldött üzenetek. Üzenet érkezett, miután értesítést létrejött, de a mobilalkalmazás küldött. A webjobs-feladat bejegyzéseit, amelyek a naplók hivatkozásra lépve, győződjön meg arról, hogy a feldolgozási keresztül meggyőződhet [Azure-portálon] a webes projekt:
   
    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Notification Hub minták]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Services mobilszolgáltatás]: http://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Service Bus Pub/Sub programozási]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure webjobs-feladat]: ../app-service/web-sites-create-web-jobs.md
[A Notification Hubs - oktatóanyag univerzális Windows-]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure-portálon]: https://portal.azure.com/
