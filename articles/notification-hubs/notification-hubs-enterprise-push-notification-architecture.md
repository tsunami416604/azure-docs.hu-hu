---
title: A Notification Hubs - vállalati leküldéses architektúrája
description: Útmutató az Azure Notification Hubs használatával vállalati környezetben
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: d7066b58330d35e5dba66cfe6ed5cfaddff4b68a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778062"
---
# <a name="enterprise-push-architectural-guidance"></a>Útmutató a vállalati leküldési architektúrákhoz
A vállalatok ma egyre fokozatosan áthelyezése felé létrehozása vagy az alkalmazásokat a végfelhasználók számára (külső) vagy (belső) az alkalmazottak számára. Létező háttérrendszerek helyen kell azt Nagyszámítógépek vagy néhány LoB-alkalmazások, amelyek integrálni kell a mobilalkalmazás-architektúra rendelkeznek. Ez az útmutató hogyan legjobb, ha ez az integráció ajánló lehetséges megoldás a gyakori forgatókönyvek beszél.

Gyakori követelmény van a leküldéses értesítések küldését a felhasználók számára a mobilalkalmazás keresztül, a háttér-rendszer egyik fontos esemény bekövetkezésekor. például egy bank ügyfél, a bank banki alkalmazás iPhone rendelkező szeretne értesítést, ha tartozik egy bizonyos mennyiség a fiók vagy egy intranetes forgatókönyvet, ahol azt szeretné a pénzügyi részleg egy alkalmazott, aki rendelkezik egy költségvetési jóváhagyási alkalmazást a Windows Phone újabb  Ha értesítést szeretne kapni a jóváhagyási kérelem érkezik.

A banki vagy jóváhagyási feldolgozási valószínű, hogy néhány háttérrendszer, amely egy leküldéses a felhasználónak kell kezdeményeznie végezhető. Előfordulhat, hogy több ilyen háttér rendszerek, amelyek kell az összes build ugyanezeket a programot, majd, ha az eseményt akkor váltja ki egy értesítést. Itt összetettségét és egy egyetlen leküldéses rendszer, ahol lehetséges, hogy a végfelhasználók számára különböző értesítések előfizetett és is lehet több mobilalkalmazások több háttérrendszerek integrálása létrejönnie. Például intranet mobilalkalmazások ahol egy mobilalkalmazás előfordulhat, hogy értesítést szeretne kapni a több ilyen háttérrendszerek. A háttérrendszerek nem ismeri vagy tudniuk kell, hogy leküldéses szemantikáját/technológia, így a közös megoldást-ra volt egy összetevő, amely lekérdezi a háttérrendszerek bármely események számára, és a leküldéses üzenetek küldéséért felelős bevezetni a az ügyfél.

Jobb megoldás, hogy Azure Service Bus - témakört/előfizetést modell, amely méretezhető és a megoldás csökkenti a összetettségét használja.

Ez a megoldás általános architektúrájának (több mobilalkalmazásokkal általános, de egyaránt alkalmazható, ha csak egy mobilalkalmazás)

## <a name="architecture"></a>Architektúra
![][1]

A kulcs darab architekturális ezen a diagramon az Azure Service Bus, amely a témakörök/előfizetések programozási modellt biztosít (további sablonokat a [Service Bus Pub/Sub programozási]). A fogadó, amely ebben az esetben a mobil-háttéralkalmazást (általában [Azure Mobile Services mobilszolgáltatás], amely kezdeményezi egy leküldéses a mobilalkalmazások) kapott üzenetek közvetlenül a háttérrendszerről azonban ehelyett köztes absztrakciós réteg megadott [Azure Service Bus által, amely lehetővé teszi a mobil-háttéralkalmazást üzenetek fogadása egy vagy több háttérrendszerek. A Service Bus-témakörbe kell létrehozni minden, a háttér-rendszerek, például a fiók, a HR, a pénzügyi, amely alapvetően "témakörök" iránt, amely kezdeményezi, leküldéses értesítést küldeni. A háttérrendszerek üzenetek küldése a következő témaköröket. A mobil-háttéralkalmazás kérhet le egy vagy több ilyen témakörök által a Service Bus-előfizetés létrehozása. Feljogosítja a a mobil-háttéralkalmazást is megkapja az értesítéseket a megfelelő háttér rendszerből. Mobil-háttéralkalmazást továbbra is az üzeneteket az előfizetések és, amint egy üzenet érkezik, akkor kapcsolja vissza, és elküldi az értesítési központba értesítésként. A Notification hubs majd végül kézbesíteni az üzenetet a mobilalkalmazásban. A legfontosabb összetevők listája itt található:

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
2. A forgatókönyv éppen egy vagy több háttérrendszerről származó események fogadására képes több mobilalkalmazásából lesz.  

## <a name="sample"></a>Minta:
### <a name="prerequisites"></a>Előfeltételek
Hajtsa végre az alábbi oktatóanyagok megismerkednie a fogalmakat, valamint a gyakori létrehozása a & konfigurációs lépéseket:

1. [Service Bus Pub/Sub programozási] -Ez az oktatóanyag azt ismerteti, működik-e a Service Bus témakörök/előfizetések, részleteit tartalmazó témakörök/előfizetésekre, a névtér létrehozása hogyan üzeneteket fogadjon & küldése.
2. [A Notification Hubs - oktatóanyag univerzális Windows-] -Ez az oktatóanyag azt ismerteti, hogyan állítson be egy Windows Áruházbeli alkalmazást és regisztrálja, és ezután kapni az értesítések a Notification Hubs használatával.

### <a name="sample-code"></a>Mintakód
A teljes minta kódja megtalálható [Notification Hub minták]. A oszlik három összetevővel:

1. **EnterprisePushBackendSystem**
   
    a. Ez a projekt használja a *WindowsAzure.ServiceBus* NuGet-csomag alapozva [Service Bus Pub/Sub programozási].
   
    b. Ez az alkalmazás egy egyszerű C# Konzolalkalmazás szimulálására egy LoB, amely kezdeményezi a mobilalkalmazás küldendő üzenet.
   
        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the topic
            CreateTopic(connectionString);
   
            // Send message
            SendMessage(connectionString);
        }
   
    c. `CreateTopic` a Service Bus-témakörbe létrehozására szolgál.
   
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
   
    d. `SendMessage` az üzenetek küldése a Service Bus-témakörbe szolgál. Ez a kód rendszeres időközönként a minta céljából a következő témakörben egyszerűen küld véletlenszerű üzenetek készleteit. Nincs szokásos módon a háttérrendszer, amely üzeneteket küld, amikor az esemény akkor következik be.
   
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
   
    a. Ez a projekt használja a *WindowsAzure.ServiceBus* és *Microsoft.Web.WebJobs.Publish* NuGet a csomagok és alapuló [Service Bus Pub/Sub programozási].
   
    b. A következő Konzolalkalmazás fut egy [Azure webjobs-feladat] mivel folyamatosan figyelni a LoB-/ háttérrendszerek üzeneteit futtatásához rendelkezik. Ez az alkalmazás a mobil-háttéralkalmazást részét képezi.
   
        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the subscription that receives messages
            CreateSubscription(connectionString);
   
            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }
   
    c. `CreateSubscription` használt előfizetés létrehozása a Service Bus a témakör a háttérrendszer ahol üzeneteket küld. Attól függően, hogy az üzleti terület ezt az összetevőt a megfelelő témakörök legalább egy előfizetést hoz létre (például néhány lehetséges, hogy lehet üzenetek fogadása HR rendszer, egyes pénzügyi rendszer, és így tovább)
   
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
   
    e. Ez az alkalmazás-közzététel egy **webjobs-feladat**, a Visual Studio-megoldásban kattintson a jobb gombbal, és válassza ki **webjobs-feladat közzététel**
   
    ![][2]
   
    f. Válassza ki a közzétételi profilt, és hozzon létre egy új Azure-webhelyre, ha még nem létezik, amely a webjobs-feladat, és ha a webhely majd **közzététel**.
   
    ![][3]
   
    g. A "Folyamatos Futtatás" kell, hogy mikor jelentkezik be a feladat a [Azure-portálon] látnia kell a következőhöz:
   
    ![][4]
3. **EnterprisePushMobileApp**
   
    a. Ez az alkalmazás egy Windows Áruházbeli alkalmazást, amely a webjobs-feladat a mobil-háttéralkalmazást részeként bejelentési értesítést kap, és megjeleníti azt. Ez a kód alapján [A Notification Hubs - oktatóanyag univerzális Windows-].  
   
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
1. Győződjön meg arról, hogy a webjobs-feladat sikeresen fut, és ütemezett folyamatosan.
2. Futtassa a ** EnterprisePushMobileApp, amely elindítja a Windows áruház egy alkalmazásához.
3. Futtassa a **EnterprisePushBackendSystem** konzolalkalmazásra, amely a LoB-háttérrendszer szimulálja, és elindítja a küld üzeneteket, és például az alábbi képen szereplő bejelentési értesítést kell megjelennie:
   
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
