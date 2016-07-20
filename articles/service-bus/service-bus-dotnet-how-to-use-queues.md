<properties
    pageTitle="A Service Bus-üzenetsorok használata (.NET) | Microsoft Azure"
    description="Ismerje meg, hogyan használhatók a Service Bus-üzenetsorok az Azure-ban. A kódminták C# nyelven íródtak a .NET API használatával."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sethm"/>

# A Service Bus-üzenetsorok használata

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez a cikk a Service Bus-üzenetsorok használatát ismerteti. A kódminták C# nyelven íródtak, és a .NET API-t használják. Az ismertetett forgatókönyvek között megtalálható az üzenetsorok létrehozása, valamint az üzenetek küldése és fogadása. Az üzenetsorokkal kapcsolatos további információkért lásd a [További lépések](#next-steps) szakaszt.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## A Service Bus NuGet-csomag hozzáadása

A Service Bus API beszerzésének, valamint az alkalmazások az összes Service Bus-függőséggel való konfigurálásának legegyszerűbb módja a [Service Bus NuGet-csomag](https://www.nuget.org/packages/WindowsAzure.ServiceBus) telepítése. A NuGet-csomag az alkalmazásban való telepítéséhez tegye a következőket:

1.  A Megoldáskezelőben kattintson a jobb gombbal a **Hivatkozások** elemre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.
2.  Keressen a „Service Bus” kifejezésre, és válassza ki az **Microsoft Azure Service Bus** elemet. Kattintson a **Telepítés** gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt.

    ![][7]

Készen áll arra, hogy kódot írjon a Service Bushoz.

## Service Bus kapcsolati karakterlánc beállítása

A Service Bus egy kapcsolati karakterláncot használ a végpontok és a hitelesítő adatok tárolásához. A következő esetekben érdemes lehet a kapcsolati karakterláncot egy konfigurációs fájlban elhelyezni a rögzített megadás helyett:

- Az Azure Cloud Services használata esetén ajánlott a kapcsolati karakterláncot az Azure szolgáltatás konfigurációs rendszerével tárolni (.csdef és .cscfg fájlokban).
- Azure-webhelyek vagy Azure virtuális gépek használata esetén ajánlott a kapcsolati karakterláncot a. NET konfigurációs rendszerével tárolni (például a Web.config fájlban).

Mindkét esetben a [CloudConfigurationManager.GetSetting][GetSetting] metódussal kérheti le a kapcsolati karakterláncot, amint az a cikk későbbi részében látható lesz.

### A kapcsolati karakterlánc konfigurálása

A szolgáltatás konfigurációs mechanizmusa lehetővé teszi a konfigurációs beállítások dinamikus módosítását a [klasszikus Azure portálról][] az alkalmazás újbóli telepítése nélkül. Például hozzáadhat egy `Setting` címkét a szolgáltatás definíciós (.csdef) fájljához a következő példában látható módon.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```
Ezután értékeket határozhat meg a szolgáltatás konfigurációs (.cscfg) fájljában a következő példában látható módon.

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Használja a klasszikus Azure portálról lekért közös hozzáférésű jogosultságkód (SAS-) kulcs nevét és a kulcs értékeit az előző szakaszban leírtak szerint.

### A kapcsolati karakterlánc konfigurálása webhelyek vagy Azure virtuális gépek használatakor

Webhelyek vagy virtuális gépek használata esetén ajánlott a. NET konfigurációs rendszerét használni (például a **Web.config** fájlt). A kapcsolati karakterláncot a(z) `<appSettings>` elem használatával tárolhatja.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Használja a klasszikus Azure portálról lekért SAS-nevet és -kulcsértékeit az előző szakaszban leírtak szerint.

## Üzenetsor létrehozása

A [NamespaceManager][] osztály használatával kezelési műveleteket hajthat végre a Service Bus-üzenetsorokon. Ez az osztály metódusokat biztosít az üzenetsorok létrehozásához, enumerálásához és törléséhez.

A következő példa egy [NamespaceManager][] objektumot állít össze az Azure [CloudConfigurationManager][] osztály használatával egy olyan kapcsolati karakterlánccal, amely egy Service Bus-szolgáltatásnévtér alapszintű címéből és a megfelelő SAS hitelesítő adatokból áll, és rendelkezik a kezeléséhez szükséges engedélyekkel. Ez a kapcsolati karakterlánc a következő példában lévő formátumot követi.

````
Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey
````

Használja a következő példát az előző szakaszban megadott konfigurációs beállításokkal.

```
// Create the queue if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue("TestQueue");
}
```

A [CreateQueue](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx) metódus túlterhelésekkel rendelkezik, amelyek lehetővé teszik az üzenetsor tulajdonságainak hangolását (például az üzenetsorba küldött üzenetek alapértelmezett élettartam (TTL) értékének meghatározását). Ezek a beállítások a [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) használatával alkalmazhatók. A következő példa bemutatja, hogyan hozható létre egy `TestQueue` nevű üzenetsor 5 GB maximális mérettel és 1 perces alapértelmezett üzenet-élettartammal.

```
// Configure queue settings.
QueueDescription qd = new QueueDescription("TestQueue");
qd.MaxSizeInMegabytes = 5120;
qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new queue with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue(qd);
}
```

> [AZURE.NOTE] A [NamespaceManager][] objektumokon alkalmazhatja a [QueueExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) metódust annak ellenőrzéséhez, hogy már létezik-e egy adott nevű üzenetsor egy szolgáltatásnévtérben.

## Üzenetek küldése egy üzenetsorba

Az alkalmazás a kapcsolati karakterlánc használatával létrehoz egy [QueueClient][] objektumot egy Service Bus-üzenetsorba való üzenetküldéshez.

A következő kód bemutatja, hogyan hozható létre egy [QueueClient][] objektum a [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx) API-hívással most létrehozott `TestQueue` üzenetsorhoz.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

Client.Send(new BrokeredMessage());
```

A Service Bus-üzenetsorba küldött és onnan fogadott üzenetek a [BrokeredMessage][] osztály példányai. A [BrokeredMessage][] objektumok egy szabványos tulajdonságkészlettel (például a [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) és a [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), az egyéni alkalmazásspecifikus tulajdonságokat tároló könyvtárral, valamint egy tetszőleges alkalmazásadatokból álló törzzsel rendelkeznek. Az alkalmazás beállíthatja az üzenet törzsét egy bármilyen szerializálható objektumnak a [BrokeredMessage][] objektum konstruktorának való átadásával, ezután a megfelelő **DataContractSerializer** osztály szerializálja az objektumot. Másik lehetőségként megadhat egy **System.IO.Stream** objektumot.

A következő példa bemutatja, hogyan küldhető öt üzenet az előző példakódban beszerzett `TestQueue` [QueueClient][] objektumnak.

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set some addtional custom app-specific properties.
  message.Properties["TestProperty"] = "TestValue";
  message.Properties["Message number"] = i;

  // Send message to the queue.
  Client.Send(message);
}
```

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. Ha a particionálás engedélyezve van, a felső korlát magasabb. További információkért lásd: [Particionált üzenetküldési entitások](service-bus-partitioning.md).

## Üzenetek fogadása egy üzenetsorból

Az üzenetek üzenetsorból való fogadásához egy [QueueClient][] objektumot használata ajánlott. A [QueueClient][] objektumok két különböző módban tudnak működni: [ReceiveAndDelete és PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

A **ReceiveAndDelete** mód használatakor a fogadás egy egylépéses művelet – vagyis amikor a Service Bus egy olvasási kérést kap egy üzenetsorban lévő üzenetre vonatkozóan, feldolgozottként jelöli meg az üzenetet, és visszaadja az alkalmazásnak. A **ReceiveAndDelete** a legegyszerűbb modell, és az olyan forgatókönyvekben működik a legjobban, ha az alkalmazás működését nem zavarja, hogy hiba esetén nem dolgoz fel üzenetet. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel ekkor a Service Bus már feldolgozottként jelölte meg az üzenetet, az alkalmazás újraindításakor és az üzenetek feldolgozásának megkezdésekor ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

**PeekLock** módban (amely az alapértelmezett mód) a fogadás kétszakaszos művelet lesz, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), végrehajtja a fogadási folyamat második a [Complete][] meghívásával a fogadott üzenethez. Amikor a Service Bus látja a [Complete][] hívást, feldolgozottként jelöli meg az üzenetet, és eltávolítja az üzenetsorból.

A következő példa bemutatja, hogyan fogadhatók és dolgozhatók fel az üzenetek az alapértelmezett **PeekLock** mód használatával. Egy másik [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) érték meghatározásához a [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx) egy másik túlterhelését használhatja. A példa az [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) visszahívást használja az üzenetek feldolgozásához, amikor megérkeznek a `TestQueue` üzenetsorba.

```
string connectionString =
  CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
QueueClient Client =
  QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

// Callback to handle received messages.
Client.OnMessage((message) =>
{
    try
    {
        // Process message from queue.
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Test Property: " +
        message.Properties["TestProperty"]);

        // Remove message from queue.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in queue.
        message.Abandon();
    }
}, options);
```

A példa egy [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) objektum használatával konfigurálja az [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) visszahívást. Az [AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) **hamis** értékre van állítva, így manuálisan vezérelhető, hogy mikor legyen meghívva a [Complete][] metódus a fogadott üzenethez. Az [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) 1 percre van beállítva, aminek következtében az ügyfél legfeljebb egy percet vár az üzenetre, mielőtt a hívás túllépi az időkorlátot, és az ügyfél új hívást indít az üzenetek ellenőrzése érdekében. Ez a tulajdonságérték csökkenti azon alkalmak számát, amikor az ügyfél olyan felszámítható hívásokat kezdeményez, amelyek nem kérnek le üzeneteket.

## Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése

A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tud feldolgozni egy üzenetet, akkor meghívhatja az [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) metódust a fogadott üzenethez (a [Complete][] metódus helyett). Ennek hatására a Service Bus feloldja az üzenet zárolását az üzenetsoron belül, és lehetővé teszi az ugyanazon vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett egy időtúllépés van hozzárendelve az üzenetsorban lévő üzenetekhez, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási idő lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet zárolását, és lehetővé teszi az újbóli fogadását.

Ha az alkalmazás az üzenet feldolgozása után, de a [Complete][] kérés kiadása előtt omlik össze, az üzenet újból kézbesítve lesz az alkalmazásnak, amikor újraindul. Ezt a módszert gyakran **Legalább egyszeri feldolgozásnak** nevezik. Ez azt jelenti, hogy minden üzenet legalább egyszer fel lesz dolgozva, de bizonyos helyzetekben előfordulhat ugyanazon üzenet újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran az üzenet [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) tulajdonságával érhető el, amely állandó marad a kézbesítési kísérletek során.

## További lépések

Most, hogy megismerte a Service Bus-üzenetsorok alapjait, az alábbi hivatkozásokból tudhat meg többet.

-   A Service Bus üzenetküldési entitásokról az [Üzenetsorok, üzenettémák és előfizetések][] című dokumentumban olvashat.
-   A [Service Bus brokered messaging .NET tutorial][] (A Service Bus által felügyelt üzenettovábbítás .NET oktatóanyaga) segítségével összeállíthat egy működő alkalmazást, amely üzeneteket küld egy Service Bus-üzenetsorba, illetve üzeneteket fogad belőle.
-   Letölthet Service Bus-mintákat az [Azure-minták][] közül, illetve megtekintheti a [Service Bus-minták áttekintését][].

  [klasszikus Azure portálról]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Üzenetsorok, üzenettémák és előfizetések]: service-bus-queues-topics-subscriptions.md
  [Service Bus brokered messaging .NET tutorial]: service-bus-brokered-tutorial-dotnet.md
  [Azure-minták]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [a Service Bus-minták áttekintése]: service-bus-samples.md
  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [Complete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx


<!--HONumber=Jun16_HO2-->


