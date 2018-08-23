---
title: Optimalizálás az Azure kódhoz a Visual Studióban |} A Microsoft Docs
description: Megtudhatja, Azure kód optimalizálási eszközök a Visual Studio hatékony, és jobban teljesítő, győződjön meg a kódot.
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
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 11/11/2016
ms.author: cawa
ms.openlocfilehash: 0497ac628d7882a0b722796493c10c0d8b04e759
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42443668"
---
# <a name="optimizing-your-azure-code"></a>Az Azure-os kód optimalizálása
A Microsoft Azure használó alkalmazások által használt programozási, ha nincsenek néhány kódolási gyakorlatot, kövesse az alkalmazás méretezhetőségi, viselkedését és a felhőalapú környezetekben problémák elkerülése érdekében. A Microsoft biztosít egy Azure kód elemző eszköz, amely felismeri és azonosítja a leggyakrabban észlelt problémák számos, és segít elhárítani őket. Az eszköz a Visual Studióban a NuGet segítségével töltheti le.

## <a name="azure-code-analysis-rules"></a>Az Azure kód Kódelemzési szabályok
Az Azure kód elemző eszköz a következő szabályok segítségével automatikusan megjelöli az Azure-kódmintát, ha úgy találja, hogy a teljesítményt érintő ismert problémákat. Észlelt problémák jelennek meg a figyelmeztetéseket vagy fordítási hibákat. Opravy kódu vagy javaslatok a figyelmeztetés vagy hiba megoldásához gyakran szolgáltatáson keresztül a villanykörte ikonnal.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Ne használja az alapértelmezett (a-folyamat) munkamenet-állapot módját
### <a name="id"></a>ID (Azonosító)
AP0000

### <a name="description"></a>Leírás
Ha használja az alapértelmezett (a-folyamat) munkamenet-állapot módját a felhőalapú alkalmazásokhoz, a munkamenet-állapot elvesztése fogadható el.

Ossza meg az ötletek és a visszajelzések [Azure kód elemzési visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Alapértelmezés szerint a web.config fájlban megadott munkamenet-állapot módját a folyamaton. Is ha a konfigurációs fájlban megadott bejegyzés nem a munkamenet-állapot módját a alapértelmezés szerint a folyamaton. A folyamaton belüli módot a munkamenet-állapot a webkiszolgáló a memóriában tárolja. Ha egy példány újraindítása, vagy egy új példányt használja a terheléselosztási és feladatátvételi támogatást, a webkiszolgáló a memóriában tárolt munkamenet-állapot nem menti a rendszer. Ez a helyzet megakadályozza, hogy az alkalmazás a felhő skálázható folyamatban van.

ASP.NET-munkamenetállapot több különböző tárolási lehetőség támogatja a munkamenet-állapot adatainak: InProc, StateServer, SQL Server, egyéni, kijelentkezniük és. Javasoljuk, hogy egyéni módot használja adatok tárolására egy külső munkamenet-állapot áruházban, például [Redis Azure munkamenetállapot-szolgáltatója](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Megoldás
Egyetlen ajánlott megoldás, ha a managed cache szolgáltatás a munkamenet-állapot tárolása. Ismerje meg, hogyan használható [Redis Azure munkamenetállapot-szolgáltatója](http://go.microsoft.com/fwlink/?LinkId=401521) a munkamenet-állapotának tárolására. A munkamenet-állapot más helyen, hogy az alkalmazás biztosan skálázható a felhőben is tárolhatja. További információért olvassa el az alternatív megoldások tudnivalók [munkamenet-állapot módok](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Futtatási mód nem lehet aszinkron
### <a name="id"></a>ID (Azonosító)
AP1000

### <a name="description"></a>Leírás
Hozzon létre aszinkron metódusok (például [await](https://msdn.microsoft.com/library/hh156528.aspx)) kívül a [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódus majd hívja a aszinkron metódusok a [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Deklaráló a [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódus aszinkron módon okoz, adjon meg egy újraindítási hurokba a feldolgozói szerepkör.

Ossza meg az ötletek és a visszajelzések [Azure kód elemzési visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Belül aszinkron metódusok meghívása a [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódus okoz a cloud service futtatási a feldolgozói szerepkör újraindítása. Feldolgozói szerepkör indulásakor az összes program végrehajtását belül kerül sor a [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódust. Kilépés a [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódus a feldolgozói szerepkör újraindítását okozza. A feldolgozói szerepkör futtatókörnyezet az async módszert rákeres, amikor továbbítja a minden művelet után az async módszert, és adja vissza. Ennek hatására a feldolgozói szerepkör-ből való kilépéshez a [ [ [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódust, és indítsa újra. A következő verzió továbbfejlesztésében végrehajtási a feldolgozói szerepkör ismét eléri az async módszert, és újraindul, a feldolgozói szerepkör-újrahasznosítást újra is okoz.

### <a name="solution"></a>Megoldás
Helyezze el az alkalmazáson kívül minden aszinkron műveletek a [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódust. Ezután hívja meg a újratervezhetők aszinkron metódust a belül a [ [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) ](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) módszer, például RunAsync () .wait. Az Azure kód elemzőeszköz segíthet a probléma megoldásához.

A következő kódrészletet mutat be a kódot a probléma javítása:

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
Közös hozzáférésű Jogosultságkód (SAS) használata a hitelesítéshez. A service bus-hitelesítés hamarosan elavulttá válik Access Control Service (ACS).

Ossza meg az ötletek és a visszajelzések [Azure kód elemzési visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
A fokozott biztonság érdekében az Azure Active Directory lecseréli az ACS-hitelesítés SAS-hitelesítéssel. Lásd: [Azure Active Directory az ACS jövője](https://cloudblogs.microsoft.com/enterprisemobility/2013/06/22/azure-active-directory-is-the-future-of-acs/) az átmenet terv tájékoztatást.

### <a name="solution"></a>Megoldás
SAS-hitelesítés használata az alkalmazásokban. Az alábbi példa bemutatja, hogyan használja egy meglévő SAS-tokent egy service bus-névtér vagy entitás eléréséhez.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

További információ a következő témakörökben talál.

* Áttekintéséhez lásd: [közös hozzáférésű Jogosultságkódos hitelesítés Service Bus szolgáltatással](https://msdn.microsoft.com/library/dn170477.aspx)
* [Közös hozzáférésű Jogosultságkódos hitelesítés Service Bus használatával](https://msdn.microsoft.com/library/dn205161.aspx)
* A mintaprojekt, lásd: [használata közös hozzáférésű Jogosultságkód (SAS) hitelesítés a Service Bus-előfizetések](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Érdemes lehet OnMessage metódus "hurok jelenik meg" elkerülése érdekében
### <a name="id"></a>ID (Azonosító)
AP2002

### <a name="description"></a>Leírás
Elkerülése érdekében a "fogadás hurok," hívása a **OnMessage** metódus hívása, mint az üzenetek fogadása egy jobb megoldás a **Receive** metódust. Azonban ha kell használnia a **Receive** metódust, és adjon meg egy nem alapértelmezett kiszolgáló várakozási idő, győződjön meg arról, hogy a kiszolgáló várakozási idő több mint egy perc.

Ossza meg az ötletek és a visszajelzések [Azure kód elemzési visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Hívásakor **OnMessage**, az ügyfél elindul egy belső message szivattyú, amely folyamatosan kérdezze le a várólista vagy előfizetést. Ez az üzenet szivattyú végtelen ciklust, amely üzeneteket fogadni hívást tartalmaz. A hívás túllépi az időkorlátot, ha egy új hívást kapcsolatos problémák. Az időkorlát értékét határozza meg a [így időtúllépés történt](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) tulajdonságát a [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)használt.

Használatának előnye **OnMessage** képest **Receive** , hogy a felhasználóknak nem kell manuálisan üzenetek lekérdezésére, kivételek kezelése, párhuzamosan több üzenetet fel és végezze el az üzeneteket.

Ha felhívja **Receive** nélkül használja az alapértelmezett értékére, mindenképpen a *ServerWaitTime* értéke több, mint egy perc. Beállítás *ServerWaitTime* több mint egy perc alatt megakadályozza, hogy a kiszolgáló időtúllépése előtt az üzenetet teljesen.

### <a name="solution"></a>Megoldás
Tekintse át a következő hitelesítésikód-példák a tanúsítványalgoritmusok ajánlott. További részletekért lásd: [QueueClient.OnMessage metódus (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)és [QueueClient.Receive metódus (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Az Azure-üzenetküldési infrastruktúra, a teljesítmény javítása érdekében tekintse meg a kialakítási mintában [aszinkron üzenetkezelés ismertetését](https://msdn.microsoft.com/library/dn589781.aspx).

Az alábbiakban egy példát a **OnMessage** üzeneteket fogadni.

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

Az alábbiakban egy példát a **Receive** az alapértelmezett kiszolgáló a várakozási idő.

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

Az alábbiakban egy példát a **Receive** egy nem alapértelmezett kiszolgálóval várakozási idő.

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
## <a name="consider-using-asynchronous-service-bus-methods"></a>Fontolja meg a Service Bus aszinkron metódusok
### <a name="id"></a>ID (Azonosító)
AP2003

### <a name="description"></a>Leírás
Módszerekkel aszinkron a Service Bus közvetítőalapú üzenettovábbítás a teljesítmény javítása.

Ossza meg az ötletek és a visszajelzések [Azure kód elemzési visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Alkalmazás program egyidejűségi aszinkron metódusok használata lehetővé teszi, mivel minden hívás végrehajtása nem tiltja a fő szálát. A Service Bus üzenetkezelés módszerek, egy műveletet használatakor (Küldés, kap, törlés, stb.) időt vesz igénybe. Ezúttal a művelet feldolgozása tartalmazza a Service Bus szolgáltatás a kéréseket és a válasz késés mellett. Növelje a műveletek száma idő, műveleteket végre kell hajtani egyidejűleg. További részletekért tekintse meg [ajánlott eljárások a teljesítmény fejlesztései használatával Service Bus felügyelt üzenettovábbítás](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Megoldás
Lásd: [QueueClient osztály (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) aszinkron ajánlott módszer használatával kapcsolatos információkat.

Az Azure-üzenetküldési infrastruktúra, a teljesítmény javítása érdekében tekintse meg a kialakítási mintában [aszinkron üzenetkezelés ismertetését](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Érdemes lehet a particionálási Service Bus-üzenetsorok és témakörök
### <a name="id"></a>ID (Azonosító)
AP2004

### <a name="description"></a>Leírás
Partíció Service Bus-üzenetsorok és témakörök a jobb teljesítmény érdekében a Service Bus-üzenetkezelés.

Ossza meg az ötletek és a visszajelzések [Azure kód elemzési visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Particionálása a Service Bus-üzenetsorok és témakörök növeli a teljesítményt átviteli sebesség és a szolgáltatás rendelkezésre állási, mivel a teljes átviteli képessége particionált üzenetsor vagy témakör már nem korlátozzák a teljesítmény egyetlen üzenettovábbítóról vagy üzenetküldési tárolóban. Emellett egy átmeneti szolgáltatáskimaradás az üzenetküldési tárolóban nem elérhetetlenné egy particionált üzenetsorra vagy témakörbe. További információkért lásd: [üzenetküldési entitások particionálás](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Megoldás
A következő kódrészletet bemutatja, hogyan üzenetküldési entitások particionálásához.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

További információkért lásd: [particionált Service Bus-üzenetsorok és témakörök |} A Microsoft Azure Blog](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) , és tekintse meg a [a Microsoft Azure Service Bus-particionált üzenetsor](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) minta.

## <a name="do-not-set-sharedaccessstarttime"></a>Nincs beállítva a SharedAccessStartTime
### <a name="id"></a>ID (Azonosító)
AP3001

### <a name="description"></a>Leírás
Kerülje a SharedAccessStartTimeset az aktuális időpontnál azonnal elindítani a megosztott hozzáférési szabályzat. Csak akkor kell állítani ezt a tulajdonságot, ha a megosztott hozzáférési szabályzat elindítása egy későbbi időpontban szeretné.

Ossza meg az ötletek és a visszajelzések [Azure kód elemzési visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Számítógépóra-szinkronizálás eredményezi egy kis idő különbség az adatközpontok között. Például szeretné logikailag úgy gondolja, hogy a tároló SAS-szabályzat kezdési idejét beállítása a jelenlegi idő szerint DateTime.Now használatával, vagy egy hasonló módszer eredményezi a SAS-szabályzat azonnal érvénybe. Azonban az adatközpontok közötti enyhe időeltérést problémákat okozhat a egyes adatközpont lehet némileg későbbi a kezdési időpontnál, míg más modelljei előre, mivel. Ennek eredményeképpen lejár az SAS-szabályzatot is gyorsan (vagy akár azonnal) Ha a házirend élettartamának értéke túl rövid.

Az Azure storage közös hozzáférésű Jogosultságkód használatáról további útmutatóért lásd: [bemutatása tábla SAS (közös hozzáférésű Jogosultságkód), várólista SAS és a Blobokra vonatkozó SAS - a Microsoft Azure Storage csapat blogja - update webhely kezdőlap - MSDN blogok](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Megoldás
Távolítsa el az utasítást, amely beállítja a megosztott elérési házirend kezdési idejét. Az Azure kód elemző eszközt biztosít a javítás erre a problémára. Biztonságkezelés további információkért tekintse meg a kialakítási mintában [Pótkulcs minta](https://msdn.microsoft.com/library/dn568102.aspx).

A következő kódrészlet azt mutatja be a kódot a probléma javítása.

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

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Megosztott hozzáférési szabályzat lejárati idő több mint öt perc alatt kell lennie.
### <a name="id"></a>ID (Azonosító)
AP3002

### <a name="description"></a>Leírás
Lehet, mint amennyit az órák "óra torzulása." néven ismert feltétel miatt különböző helyeken lévő adatközpontok között eltérés öt perc Hogy a SAS lejárjanak a házirend jogkivonat-esnél korábbi tervezett, állítsa a lejárati idő több mint öt perc alatt.

Ossza meg az ötletek és a visszajelzések [Azure kód elemzési visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
A világ különböző pontjain különböző helyeken lévő adatközpontokban óra jel szinkronizálni. Mivel némi időre a órajel továbbítani a különböző helyeken, lehetnek a különböző földrajzi helyen lévő adatközpontok közötti idő eltérés Bár mindent szoftverként szinkronizálva van. Ez eltérést hatással lehet a megosztott hozzáférési szabályzat kezdési időt és a lejárati időköz. Ezért annak érdekében, hogy a megosztott hozzáférési szabályzat azonnal érvénybe lép, ne adja meg a kezdő időpont. Emellett ellenőrizze, hogy a lejárati idő több mint 5 perc korai időtúllépési elkerülése érdekében.

Az Azure storage közös hozzáférésű Jogosultságkód használatával kapcsolatos további információkért lásd: [bemutatása tábla SAS (közös hozzáférésű Jogosultságkód), várólista SAS és a Blobokra vonatkozó SAS - a Microsoft Azure Storage csapat blogja - update webhely kezdőlap - MSDN blogok](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Megoldás
Biztonságkezelés további információkért lásd: a kialakítási mintában [Pótkulcs minta](https://msdn.microsoft.com/library/dn568102.aspx).

Az alábbiakban látható egy példa nem adja meg a megosztott hozzáférési szabályzat kezdési időt.

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

Az alábbiakban látható egy példa az öt percnél nagyobb házirend lejárati időtartam kezdési idő megosztott hozzáférési szabályzat megadása.

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

További információkért lásd: [hozhat létre és használhat egy közös hozzáférésű Jogosultságkód](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="use-cloudconfigurationmanager"></a>CloudConfigurationManager használata
### <a name="id"></a>ID (Azonosító)
AP4000

### <a name="description"></a>Leírás
Használatával a [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) osztály projektek, mint például az Azure-webhely és az Azure mobilszolgáltatások nem vezetnek be memóriafelhasználás problémákat. Ajánlott eljárásként, azonban jó ötlet Felhőbeli használandó[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager\(v=vs.110\).aspx) egységes adtuk meg, az összes Azure-felhőalkalmazások konfigurációk kezelése.

Ossza meg az ötletek és a visszajelzések [Azure kód elemzési visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
CloudConfigurationManager beolvassa a konfigurációs fájl megfelelő az alkalmazás-környezetbe.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Megoldás
Refaktorovat a kódot, és használja a [CloudConfigurationManager osztály](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). A kód a probléma javítása az Azure kód elemző eszköz által biztosított.

A következő kódrészlet azt mutatja be a kódot a probléma javítása. Csere

`var settings = ConfigurationManager.AppSettings["mySettings"];`

a következővel:

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Íme egy példa bemutatja, hogyan egy App.config vagy a Web.config fájlban tárolhatja a konfigurációs beállítás. Adja hozzá az appSettings szakaszt a konfigurációs fájl a beállításokat. Az előző példakódban a Web.config fájlt a következő:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>Kerülje a szokott kapcsolati karakterláncok
### <a name="id"></a>ID (Azonosító)
AP4001

### <a name="description"></a>Leírás
Változtatható kapcsolati karakterláncokat használ, és később frissíteni kell, ha módosítja a forráskódot, és az alkalmazás újrafordítottuk fog rendelkezik. Azonban ha a kapcsolati karakterláncokat tárolja a konfigurációs fájlban, módosíthatja őket később egyszerűen a konfigurációs fájl frissítésével.

Ossza meg az ötletek és a visszajelzések [Azure kód elemzési visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Fix kódolása kapcsolati karakterláncok egy rossz ajánlott, mert problémák, ha a kapcsolati karakterláncok gyorsan módosítani kell. Ezenkívül ha a projekt kell jelölni a forráskezelőhöz, változtatható kapcsolati karakterláncok vezeti be biztonsági rések óta a karakterláncok tekinthet meg a forráskódban.

### <a name="solution"></a>Megoldás
Kapcsolati karakterláncok Store a konfigurációs fájlok vagy az Azure környezetben.

* Különálló alkalmazások esetében használni app.config kapcsolatikarakterlánc-beállításokat.
* IIS kiszolgálón futó webalkalmazások esetén használja a web.config kapcsolati karakterláncok tárolásához.
* Az ASP.NET-alkalmazások vNext használja a configuration.json kapcsolati karakterláncok tárolásához.

Konfigurációk fájlok, például a web.config vagy az App.config fájlt használatáról további információért lásd: [ASP.NET webes beállítási útmutatója](https://msdn.microsoft.com/library/vstudio/ff400235\(v=vs.100\).aspx). Információ az Azure környezeti változók működése: [Azure-webhelyek: alkalmazás-karakterláncok és a kapcsolati karakterláncok működik](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). A kapcsolati karakterlánc tárolása verziókövetési rendszerben További információkért lásd: [ne tegye a bizalmas adatokat, például kapcsolati karakterláncokat a forráskód tárházából a tárolt fájlok](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Diagnosztikai konfigurációs fájl
### <a name="id"></a>ID (Azonosító)
AP5000

### <a name="description"></a>Leírás
Diagnosztikai beállítások konfigurálására a kódban, mint például a Microsoft.WindowsAzure.Diagnostics programozási API használatával, konfigurálnia kell az diagnosztikai beállítások a diagnostics.wadcfg fájlban. (Vagy, ha az Azure SDK 2.5-ös diagnostics.wadcfgx). Ezzel az eljárással módosíthatja a diagnosztikai beállítások nélkül kell fordítani a kódot.

Ossza meg az ötletek és a visszajelzések [Azure kód elemzési visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
Mielőtt az Azure SDK 2.5-ös (amely az Azure diagnostics 1.3-as), az Azure Diagnostics (WAD) sikerült konfigurálni a számos különböző módszer használatával: hozzáadná a konfiguráció blob storage, az imperatív kódok, deklaratív konfigurációs vagy az alapértelmezett használatával konfiguráció. Viszont az előnyben részesített módja diagnosztika konfigurálása, hogy az XML konfigurációs fájl (diagnostics.wadcfg vagy diagnositcs.wadcfgx SDK 2.5-ös és újabb verziók) az alkalmazás-projektben. Ebben a megközelítésben az diagnostics.wadcfg fájl teljesen konfigurációját és is frissíthetők és újratelepítése, majd. A diagnostics.wadcfg konfigurációs fájl használatát keverni a konfigurációk beállítása használatával a programozott módszerekkel a [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)vagy [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)osztályokat is zavart okozhat. Lásd: [inicializálása vagy az Azure Diagnostics konfigurációjának módosítása](https://msdn.microsoft.com/library/azure/hh411537.aspx) további információt.

(Az Azure SDK 2.5-ös része) WAD 1.3-tól kezdve már nem használható kódot a diagnosztika való konfigurálásához. Ennek eredményeképpen csak biztosíthat a konfiguráció alkalmazása, vagy a diagnosztika bővítmény frissítése közben.

### <a name="solution"></a>Megoldás
A diagnosztika konfigurációtervező használata a diagnosztikai beállítások áthelyezése a diagnosztikai konfigurációs fájl (diagnositcs.wadcfg vagy diagnositcs.wadcfgx SDK 2.5-ös és újabb verziók). Emellett ajánlott telepíteni [Azure SDK 2.5-ös](http://go.microsoft.com/fwlink/?LinkId=513188) és a legújabb diagnosztikai szolgáltatás használata.

1. A helyi menüben a szerepkörhöz, amelyet konfigurálni szeretne válassza a Tulajdonságok elemet, és válassza a konfiguráció lapon.
2. Az a **diagnosztikai** területén ellenőrizze, hogy a **diagnosztika engedélyezése** jelölőnégyzet be van jelölve.
3. Válassza ki a **konfigurálása** gombra.

   ![A diagnosztika engedélyezése beállítás elérése](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

   Lásd: [diagnosztika beállítása az Azure Cloud Services és Virtual Machines](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) további információt.

## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Kerülje a DbContext objektum statikus deklaráló
### <a name="id"></a>ID (Azonosító)
AP6000

### <a name="description"></a>Leírás
A memóriahasználat, kerülje a DBContext objektum statikus deklaráló.

Ossza meg az ötletek és a visszajelzések [Azure kód elemzési visszajelzés](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Ok
DBContext objektum tartsa a lekérdezés eredményeit az egyes hívások. Statikus DBContext objektum nem értékesítik, amíg az alkalmazás tartománya a memóriából. Ezért egy statikus DBContext objektum nagy mennyiségű memóriát használ.

### <a name="solution"></a>Megoldás
DBContext deklaráljon egy helyi változóhoz vagy Pole instance nem statikus, használhatja azt egy feladatot, és majd biztosítani, hogy a használat után kell értékesíteni.

Az alábbi példa MVC vezérlőosztály bemutatja, hogyan használhatja a DBContext objektum.

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
További információ optimalizálás és az Azure-alkalmazások hibáinak elhárítása kapcsolatban lásd: [-webalkalmazás létrehozása az Azure App Service-ben a Visual Studio használatával hibaelhárítása](app-service/web-sites-dotnet-troubleshoot-visual-studio.md).
