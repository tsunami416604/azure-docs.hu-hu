---
title: A Visual Studio Azure kód optimalizálása |} Microsoft Docs
description: Ismerje meg, az Azure kód optimalizálási eszközök Visual Studio érdekében a kód megbízhatóbb és jobban végrehajtása.
services: visual-studio-online
documentationcenter: na
author: cawa
manager: paulyuk
editor: ''
ms.assetid: ed48ee06-e2d2-4322-af22-07200fb16987
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: cawa
ms.openlocfilehash: 3ee2cc3ac5098ebf205331167faffa2b5f9b6d56
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937557"
---
# <a name="optimizing-your-azure-code"></a>Az Azure kód optimalizálása
Ha a Microsoft Azure használó alkalmazások által programozási, van néhány alkalmazás méretezhetőséget, viselkedését, egy felhőalapú környezetben a teljesítményt és a problémák elkerülése érdekében ajánlott eljárásit. A Microsoft biztosít egy Azure kód elemző eszköz, amely ismeri fel és azonosítja a leggyakrabban észlelt problémák számos, és segítséget nyújt a megoldásukkal együtt. Az eszköz a Visual Studio NuGet útján lehet letölteni.

## <a name="azure-code-analysis-rules"></a>Az Azure Analysis kód szabályok
Az Azure kód elemző eszköz a következő szabályok segítségével automatikusan jelzőt az Azure kódot, ha talál teljesítményt érintő ismert problémákat. Észlelt problémák jelenhetnek meg a figyelmeztetéseket vagy fordítási hibákat. A villanykörte ikonnal gyakran kipróbálni a kód javítások és a javaslatok a figyelmeztetés vagy a hiba megoldásához.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Ne használja az alapértelmezett (a folyamat) munkamenet-állapot módját
### <a name="id"></a>ID (Azonosító)
AP0000

### <a name="description"></a>Leírás
Ha használja az alapértelmezett (a folyamat) munkamenet-állapot módját a felhőalapú alkalmazásokhoz, a munkamenet-állapot elveszhet.

Ossza meg az ötletek és visszajelzés: [Azure kód elemzés visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Alapértelmezés szerint a web.config fájlban megadott munkamenet-állapot módját a folyamatban. Is ha a konfigurációs fájlban megadott bejegyzés, a munkamenet-állapot módját a alapértelmezés szerint az a folyamat. A folyamaton belüli módot a munkamenet-állapot az a memóriában tárolja a webkiszolgálón. Amikor egy újraindítása, vagy egy új példányt a terheléselosztás és feladatátvétel támogatásáról szolgál, a webkiszolgáló a memóriában tárolt munkamenet-állapot nem menti a rendszer. Ez a helyzet megakadályozza, hogy az alkalmazás nem méretezhető a felhő.

Az ASP.NET munkamenet-állapot munkamenet-állapot adatainak támogatja a több, eltérő tárolási lehetőség: InProc, StateServer, SQL Server, egyéni, és ki. Javasoljuk, hogy egyéni módot használja adatok tárolására a munkamenet-állapot külső áruházban, például a [Azure munkamenetállapot-szolgáltató az Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Megoldás
Egyetlen ajánlott megoldás, hogy munkamenet-állapot tárolása egy felügyelt gyorsítótár szolgáltatásra. Ismerje meg, hogyan használható [Azure munkamenetállapot-szolgáltató az Redis](http://go.microsoft.com/fwlink/?LinkId=401521) a munkamenet-állapot tárolásához. A munkamenet-állapot, így az alkalmazás méretezhető a felhő más helyen is tárolhatja. További információt olvassa el az alternatív megoldások [munkamenet-állapot módok](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Futtatási mód nem lehet aszinkron
### <a name="id"></a>ID (Azonosító)
AP1000

### <a name="description"></a>Leírás
Aszinkron metódusok létrehozása (például [await](https://msdn.microsoft.com/library/hh156528.aspx)) kívül a [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) módszer, majd hívja a az aszinkron metódusoknak [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Deklaráló a [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódus aszinkron módon okoz a feldolgozói szerepkör újraindítás hurok megadását.

Ossza meg az ötletek és visszajelzés: [Azure kód elemzés visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Aszinkron metódusok belüli meghívása a [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódus okoz a felhőalapú szolgáltatás futásideje újrahasznosítása a feldolgozói szerepkör. A feldolgozói szerepkör indulásakor az összes program végrehajtását belül kerül sor a [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódust. Kilépés a [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódus okoz a feldolgozói szerepkör újraindítására. A feldolgozói szerepkör futásidejű találatok az aszinkron metódussal, ha minden műveletnél kiszállítja az aszinkron metódus után, és adja vissza. Ennek hatására a Kilépés a feldolgozói szerepkör a [ [ [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódust, és indítsa újra. A következő munkamenetben végrehajtás a feldolgozói szerepkör az aszinkron metódusban találatok újra, és újraindul, a feldolgozói szerepkör újrahasznosítása újra is, amely.

### <a name="solution"></a>Megoldás
Helyezze el az összes aszinkron művelet kívül a [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódust. Majd, meghívják a a átkerült aszinkron metódus belül a [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) módszer, például RunAsync () .wait. A Azure kód elemző eszköz segít a probléma megoldásához.

A következő kódrészletet mutatja be a kódját a hiba javítása:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>Service Bus közös hozzáférésű Jogosultságkód-hitelesítés használata
### <a name="id"></a>ID (Azonosító)
AP2000

### <a name="description"></a>Leírás
Hitelesítéshez használandó közös hozzáférésű Jogosultságkód (SAS). A service bus hitelesítéshez Access Control Service (ACS) is elavult.

Ossza meg az ötletek és visszajelzés: [Azure kód elemzés visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
A fokozott biztonság érdekében Azure Active Directory SAS hitelesítési ACS hitelesítési lecseréli. Lásd: [Azure Active Directory az ACS a jövőben](https://cloudblogs.microsoft.com/enterprisemobility/2013/06/22/azure-active-directory-is-the-future-of-acs/) az átmenet terv olvashat.

### <a name="solution"></a>Megoldás
SAS-hitelesítés használata az alkalmazások. A következő példa bemutatja, hogyan egy meglévő SAS-jogkivonat egy service bus-névtér vagy entitás elérésére használhat.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

A következő témakörökben további információt.

* Megtudhatja, [megosztott hozzáférési aláírást hitelesítést a Service Bus](https://msdn.microsoft.com/library/dn170477.aspx)
* [A Service Bus megosztott hozzáférési aláírást hitelesítés használata](https://msdn.microsoft.com/library/dn205161.aspx)
* Egy minta-projekt lásd [használatával közös hozzáférésű Jogosultságkód (SAS) hitelesítés a Service Bus-előfizetések](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Érdemes lehet OnMessage metódus "az üzenetfogadási hurok" elkerülése érdekében
### <a name="id"></a>ID (Azonosító)
AP2002

### <a name="description"></a>Leírás
Egy "az üzenetfogadási hurok," üzembe elkerülése érdekében hívja a **OnMessage** metódus hívása mint üzenetek fogadása egy jobb megoldás a **Receive** metódust. Azonban ha kell használnia a **Receive** metódust, és adjon meg egy nem alapértelmezett server várakozási idő, győződjön meg arról, hogy a kiszolgáló várakozási idő több mint egy percig.

Ossza meg az ötletek és visszajelzés: [Azure kód elemzés visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Meghívásakor **OnMessage**, az ügyfél elindul egy belső üzenet szivattyú, amely folyamatosan kérdezze le a várólista vagy az előfizetéshez. Az üzenet szivattyú által kiállított üzeneteket fogadni hívás végtelen hurkot tartalmaz. A hívás túllépi az időkorlátot, ha egy új hívás ad ki. Az időkorlát értéke határozza meg a [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) tulajdonsága a [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)használt.

Használatának előnye **OnMessage** képest **Receive** , hogy a felhasználóknak nem kell manuálisan kérdezze le az üzenetek, kivételek kezelése, párhuzamosan több üzenetek feldolgozásához, és fejezze be az üzenetek.

Ha meghívja a **Receive** anélkül, hogy az alapértelmezett értéket használja, ügyeljen a *ServerWaitTime* értéke nagyobb, mint egy perc. Beállítás *ServerWaitTime* több mint egy perc alatt megakadályozza, hogy a kiszolgáló időtúllépés miatt az üzenet teljesen megérkezése előtt.

### <a name="solution"></a>Megoldás
Ellenőrizze a következő példák az ajánlott módjait. További részletekért lásd: [QueueClient.OnMessage metódus (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)és [QueueClient.Receive metódus (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Az Azure üzenetküldési infrastruktúra a teljesítmény javítása érdekében tekintse meg a kialakítási mintában [aszinkron üzenetkezelési ismertetése](https://msdn.microsoft.com/library/dn589781.aspx).

Az alábbiakban egy példa annak **OnMessage** üzeneteket fogadni.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

Az alábbiakban egy példa annak **Receive** az alapértelmezett kiszolgáló a várakozási idő.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

Az alábbiakban egy példa annak **Receive** egy nem alapértelmezett kiszolgálóval várakozási idő.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>Érdemes lehet aszinkron Service Bus-metódusok
### <a name="id"></a>ID (Azonosító)
AP2003

### <a name="description"></a>Leírás
Módszerekkel aszinkron Service Bus közvetítőalapú üzenettovábbítás a teljesítmény javításához.

Ossza meg az ötletek és visszajelzés: [Azure kód elemzés visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Aszinkron metódusok használatával lehetővé teszi a program egyidejű application, mert minden hívás végrehajtása ne blokkolja a fő szálnak. A Service Bus üzenetkezelés módszerek, egy olyan műveletet hajt használatakor (Küldés, kapni, törlés, stb.) időt vesz igénybe. Most a Service Bus szolgáltatás mellett a kérelem és a válasz késleltetése a művelet feldolgozása tartalmaz. Az idő alatt műveletek számának növeléséhez, műveletek végre kell hajtani egyidejűleg. További információ a tekintse meg [gyakorlati tanácsok a teljesítmény javítását használatával Service Bus Közvetítőalapú üzenetkezelés](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Megoldás
Lásd: [QueueClient osztály (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) a javasolt aszinkron metódus használatával kapcsolatos információt.

Az Azure üzenetküldési infrastruktúra a teljesítmény javítása érdekében tekintse meg a kialakítási mintában [aszinkron üzenetkezelési ismertetése](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Vegye figyelembe a particionálási Service Bus-üzenetsorok és témakörök
### <a name="id"></a>ID (Azonosító)
AP2004

### <a name="description"></a>Leírás
Partíció Service Bus-üzenetsorok és témakörök a jobb teljesítmény érdekében a Service Bus üzenetkezelés.

Ossza meg az ötletek és visszajelzés: [Azure kód elemzés visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Service Bus-üzenetsorok és témakörök particionálás növeli a teljesítményt átviteli sebesség és a szolgáltatás rendelkezésre állási, mivel a már nem korlátozzák a teljes átviteli képessége – a particionált üzenetsor vagy témakör egyetlen üzenet broker vagy üzenetküldési tárolóban teljesítmény szerint. Ezenkívül átmenetileg nem működik az üzenetküldési tárolóban nem elérhetetlenné particionált üzenetsor vagy témakör. További információkért lásd: [üzenetküldési entitások particionálás](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Megoldás
A következő kódrészletet üzenetküldési entitások particionálásáról jeleníti meg.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

További információkért lásd: [particionálva Service Bus-üzenetsorok és témakörök |} A Microsoft Azure Blog](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) és tekintse meg a [Microsoft Azure Service Bus particionálva várólista](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) minta.

## <a name="do-not-set-sharedaccessstarttime"></a>Ne adja meg az SharedAccessStartTime
### <a name="id"></a>ID (Azonosító)
AP3001

### <a name="description"></a>Leírás
Az aktuális idő azonnal el tudja indítani a megosztott hozzáférési házirend SharedAccessStartTimeset segítségével kerülendő. Csak szeretné állítani ezt a tulajdonságot, ha el szeretné indítani a megosztott hozzáférési házirend egy későbbi időpontban.

Ossza meg az ötletek és visszajelzés: [Azure kód elemzés visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Számítógépóra-szinkronizálás adatközpontok között enyhe időeltérése okoz. Például akkor logikailag gondol a kezdési időt a tároló SAS-házirend beállítását az aktuális idő DateTime.Now használatával, vagy hasonló módon, akkor azonnal érvénybe lépnek a SAS-házirendet. Azonban az Adatközpont között enyhe időeltérést problémákat okozhat a mivel lehet, hogy néhány datacenter eset némileg későbbi a kezdési időpontnál, míg mások azt előre. Ennek eredményeképpen a SAS-házirend lejárhat gyorsan (vagy akár azonnal) Ha a házirend-élettartam értéke túl rövid.

Az Azure storage közös hozzáférésű Jogosultságkód használatával további útmutatást lásd: [bevezetéséről tábla SAS (közös hozzáférésű Jogosultságkód), üzenetsor SAS és a frissítést a Blob SAS - Microsoft Azure tárolás fejlesztői Blog - hely kezdőlap - MSDN-blogok](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Megoldás
Távolítsa el az utasítást, amely beállítja a megosztott elérési házirend kezdési idejét. Az Azure kód elemző eszköz biztosít egy javítást a probléma. A biztonsági felügyelet további információkért lásd: a kialakítási mintában [Valet kulcs mintát](https://msdn.microsoft.com/library/dn568102.aspx).

A következő kódrészletet mutatja be a kódját a hiba javítása.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Megosztott hozzáférési házirend lejárati idő több mint öt perc kell lennie.
### <a name="id"></a>ID (Azonosító)
AP3002

### <a name="description"></a>Leírás
Lehet, mint amennyit az órák miatt egy "óra eltérésére." néven ismert különböző helyeken adatközpontok között eltérés öt perc A biztonsági Társítások megelőzése érdekében házirend jogkivonat lejárjanak korábbi, mint a tervezett és a lejárati idő több mint öt perc állítsa be.

Ossza meg az ötletek és visszajelzés: [Azure kód elemzés visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
A világ különböző helyeken adatközpontok egy órajel szinkronizálni. A különböző helyekre továbbítani órajel időt vesz igénybe, mert lehet a különböző földrajzi helyen adatközpontokat közötti idő eltérés Bár minden támaszkodnak szinkronizálva. Ez eltérést hatással lehet a megosztott hozzáférési házirend kezdési időt és a lejárati időközt. Ezért győződjön meg arról, megosztott hozzáférési házirend azonnal érvénybe lép, nem adja meg a kezdési időpontot. Emellett ellenőrizze, hogy a lejárati idő több mint 5 perc korai időtúllépés megelőzése érdekében.

Az Azure storage közös hozzáférésű Jogosultságkód használatával kapcsolatos további információkért lásd: [bevezetéséről tábla SAS (közös hozzáférésű Jogosultságkód), üzenetsor SAS és a frissítést a Blob SAS - Microsoft Azure tárolás fejlesztői Blog - hely kezdőlap - MSDN-blogok](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Megoldás
Biztonságkezelés további információkért tekintse meg a kialakítási mintában [Valet kulcs mintát](https://msdn.microsoft.com/library/dn568102.aspx).

Nem adja meg a megosztott hozzáférési házirend kezdő időpont példát a következő:

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

A megosztott hozzáférési házirend kezdési idő megadása öt percnél nagyobb házirend lejárati időtartammal rendelkező példát a következő:

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

További információkért lásd: [létrehozhat és használhat egy közös hozzáférésű Jogosultságkód](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="use-cloudconfigurationmanager"></a>CloudConfigurationManager használata
### <a name="id"></a>ID (Azonosító)
AP4000

### <a name="description"></a>Leírás
Használja a [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) projektek osztály, például az Azure Websites és az Azure mobile services nem vezetnek be futásidejű problémákat. Ajánlott eljárásként, azonban célszerű a felhő használandó[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) minden Azure felhőalapú alkalmazásokhoz konfigurációk kezelése egyesített módja.

Ossza meg az ötletek és visszajelzés: [Azure kód elemzés visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
CloudConfigurationManager olvassa be a konfigurációs fájl megfelelő az alkalmazás-környezetbe.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Megoldás
A kód használatához refactor a [CloudConfigurationManager osztály](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). Az Azure kód elemző eszköz a kód a hiba javítása biztosítja.

A következő kódrészletet mutatja be a kódját a hiba javítása. Csere

`var settings = ConfigurationManager.AppSettings["mySettings"];`

a következővel:

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Íme egy példa egy App.config vagy a Web.config fájlban tárolhatja a konfigurációs beállítás. A beállítások hozzáadása a konfigurációs fájl appSettings szakaszában. Az előző példakódban a Web.config fájlban a következő:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>Kerülje a kódolt kapcsolati karakterláncok
### <a name="id"></a>ID (Azonosító)
AP4001

### <a name="description"></a>Leírás
Ha a kódolt kapcsolati karakterláncokat használ, és azokat később frissíteni kell, összekapcsolta módosítja a forráskódot, és fordítsa újra az alkalmazást. Azonban ha egy konfigurációs fájlban tárolja a kapcsolati karakterláncokat, később bármikor módosíthatja azokat a konfigurációs fájl egyszerűen frissítésével.

Ossza meg az ötletek és visszajelzés: [Azure kód elemzés visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Rögzített megadás kapcsolati karakterláncok a hibás célszerű, mert problémák amikor kapcsolati karakterláncok gyorsan módosítani kell. Emellett a projekt van szüksége verziókövetési rendszerrel ellenőrizni kell, ha kódolt kapcsolati karakterláncok vezethet biztonsági rések óta a karakterláncok a forráskód lehet megtekinteni.

### <a name="solution"></a>Megoldás
Kapcsolati karakterláncok tárolni a konfigurációs fájlok vagy az Azure környezetben.

* Az önálló alkalmazások app.config segítségével tárolja a kapcsolódási karakterlánc beállításainak.
* IIS kiszolgálón futó webes alkalmazásokhoz a web.config használatával kapcsolati karakterláncok tárolni.
* Az ASP.NET-alkalmazások vNext configuration.json segítségével kapcsolati karakterláncok tárolni.

Konfigurációk fájlok – például a web.config vagy az App.config fájlt használatáról információkért lásd: [ASP.NET webes beállítási útmutatója](https://msdn.microsoft.com/library/vstudio/ff400235\(v=vs.100\).aspx). Az Azure környezeti változók munkahelyi információkért lásd: [webhelyek Azure: hogyan alkalmazás karakterláncok és a kapcsolati karakterláncok működik](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). A kapcsolati karakterlánc tárolása verziókezelő információkért lásd: [ne tegye a bizalmas adatokat például kapcsolati karakterláncok forráskódraktárban tárolt fájlok](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Diagnosztika konfigurációs fájl használata
### <a name="id"></a>ID (Azonosító)
AP5000

### <a name="description"></a>Leírás
Diagnosztikai beállítások konfigurálására a kódban, többek között a Microsoft.WindowsAzure.Diagnostics programozási API használatával, konfigurálnia kell az diagnosztikai beállítások a diagnostics.wadcfg fájlban. (Vagy, ha Azure SDK 2.5 diagnostics.wadcfgx). Ezzel az eljárással módosíthatja diagnosztikai beállítások nem kell fordítani a kódot.

Ossza meg az ötletek és visszajelzés: [Azure kód elemzés visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Mielőtt az Azure SDK 2.5-ös (amely az Azure diagnostics 1.3), Azure Diagnostics (ÜVEGVATTA) sikerült konfigurálni a számos különböző módszer használatával: hozzáadná a konfiguráció blob Storage, feltétlenül szükséges kódot, deklaratív konfigurációs vagy az alapértelmezett használatával konfiguráció. Azonban az előnyben részesített diagnosztika konfigurálása módja az XML konfigurációs fájl (diagnostics.wadcfg vagy diagnositcs.wadcfgx SDK 2.5 és újabb) az alkalmazás projektben. Ezt a módszert használja, a diagnostics.wadcfg fájl teljesen konfigurációját és frissíthető és újra telepíteni fogja a. A diagnostics.wadcfg konfigurációs fájl használatának keverése kell beállítania a konfigurációk használatával programozott módon a [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)vagy [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)osztályokat is zavart okozhat. Lásd: [inicializálása vagy Azure Diagnostics konfigurációjának módosítása](https://msdn.microsoft.com/library/azure/hh411537.aspx) további információt.

(Az Azure SDK 2.5 része) ÜVEGVATTA 1.3 kezdve már nem használható kód diagnosztika konfigurálása. Ennek eredményeképpen csak biztosíthat a konfigurációban, ha az alkalmazása vagy a diagnosztika-bővítmény frissítése.

### <a name="solution"></a>Megoldás
A diagnosztika configuration designer segítségével diagnosztikai beállítások áthelyezése a diagnosztika konfigurációs fájl (diagnositcs.wadcfg vagy diagnositcs.wadcfgx SDK 2.5 és újabb). Emellett ajánlott telepíteni [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) és diagnosztika szolgáltatás legújabb verziója.

1. A helyi menüben a konfigurálni kívánt szerepkör esetében válassza a tulajdonságok, és kattintson a konfiguráció lapon.
2. Az a **diagnosztika** területen győződjön meg arról, hogy a **engedélyezése diagnosztikai** jelölőnégyzet be van jelölve.
3. Válassza ki a **konfigurálása** gombra.

   ![A diagnosztika engedélyezése beállítás elérése](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

   Lásd: [diagnosztika konfigurálása az Azure Cloud Services és a virtuális gépek](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) további információt.

## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Kerülje a DbContext objektumokat statikus deklaráló
### <a name="id"></a>ID (Azonosító)
AP6000

### <a name="description"></a>Leírás
A memóriahasználat, ne a deklaráló statikus DBContext objektumokat.

Ossza meg az ötletek és visszajelzés: [Azure kód elemzés visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
DBContext objektumok minden hívás a lekérdezés eredményeinek tárolásához. Statikus DBContext objektumok nem értékesítik, amíg az alkalmazástartomány eltávolítva. Ezért a statikus DBContext objektum nagy mennyiségű memóriát is felhasználhatnak.

### <a name="solution"></a>Megoldás
DBContext deklarálható lokális változó vagy nem statikus mezőn, a feladat használni, és engedélyezze azt követően ártalmatlanítani.

Az alábbi példa MVC-vezérlő osztályhoz bemutatja, hogyan használja a DBContext objektumot.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>További lépések
Optimalizálás, és tájékozódhat hibaelhárítása az Azure-alkalmazások súgójának [hibaelhárítása a webes alkalmazás az Azure App Service szolgáltatásban a Visual Studio használatával](app-service/web-sites-dotnet-troubleshoot-visual-studio.md).
