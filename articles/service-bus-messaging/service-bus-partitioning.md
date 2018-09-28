---
title: Hozzon létre a particionált Azure Service Bus-üzenetsorok és témakörök |} A Microsoft Docs
description: Ismerteti, hogyan lehet particionálni a Service Bus-üzenetsorok és üzenettémák több üzenetközvetítők használatával.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/06/2018
ms.author: spelluru
ms.openlocfilehash: 2ca2063158634dfa42da094b77c70a2730e82176
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423170"
---
# <a name="partitioned-queues-and-topics"></a>Particionált üzenetsorok és témakörök

Az Azure Service Bus-üzenetek feldolgozása több üzenetközvetítők és több üzenetküldési tároló üzenetek tárolására használ. A hagyományos üzenetsor vagy témakör egyetlen üzenettovábbítóról kezeli, és egyetlen üzenetküldési tárolóra tárolja. A Service Bus *partíciók* üzenetsorokat és üzenettémákat, engedélyezése vagy *üzenetküldési entitások*, particionálandó több üzenetközvetítők és üzenetküldési tároló. A particionálás, az azt jelenti, hogy a particionált entitás összesített átviteli már nem korlátozzák a teljesítmény egyetlen üzenettovábbítóról vagy üzenetküldési tárolóban. Emellett egy átmeneti szolgáltatáskimaradás az üzenetküldési tárolóban nem jelennek meg a particionált üzenetsor vagy témakör nem érhető el. Particionált üzenetsorok és témakörök az összes speciális Service Bus-szolgáltatások, például a tranzakciók és a munkamenetek támogatása is tartalmazhat.

> [!NOTE]
> A particionálás entitás létrehozásakor valamennyi üzenetsorok és témakörök a Basic vagy Standard termékváltozatok esetében érhető el. A prémium szintű üzenetkezelési Termékváltozat nem érhető el, de a prémium szintű névterek minden korábban meglévő particionált entitások továbbra is a várt módon működik.
 
Már nem módosíthatja a particionálási lehetőséget, bármely meglévő üzenetsor vagy témakör; a beállítás csak akkor állíthat be az entitás létrehozásakor.

## <a name="how-it-works"></a>Működés

Minden egyes particionált üzenetsor vagy témakör több töredékkel áll. Minden részlet egy különböző üzenetküldési tárolóban tárolja, és egy másik közvetítő kezeli. Ha egy üzenetet küld egy particionált üzenetsorra vagy témakörbe, a Service Bus hozzárendeli az üzenetet a töredék egyikéhez. A Service Bus vagy egy partíciókulcsot, hogy a küldő megadhatja a kijelölt véletlenszerűen történik.

Amikor egy ügyfél kéri egy üzenetet egy particionált üzenetsorra vagy egy particionált témakör lekérdezések a Service Bus-előfizetés minden naplóhasználatra üzeneteket, majd adja vissza az első üzenet, amely a fogadó kérhető le az üzenetküldési tárolók bármelyikét. A Service Bus-gyorsítótárak a további üzeneteket, és adja vissza őket, amikor kap további kérések fogadására. A fogadó ügyfél még nem ismeri a particionálás; az ügyfél irányultságú viselkedését egy particionált üzenetsorra vagy témakörbe (például olvasási, végezze el, késlelteti, a kézbesítetlen, prefetching) azonos a normál entitások viselkedését.

Nincs üzenet küldésekor, vagy üzenet fogadása a particionált üzenetsor vagy témakör a további költségek nélkül.

## <a name="enable-partitioning"></a>Particionálás engedélyezése

Particionált üzenetsorok és témakörök használata az Azure Service Bus, az Azure SDK 2.2 vagy újabb verzióját, vagy adja meg `api-version=2013-10` vagy későbbi részében a HTTP-kérelmekre.

### <a name="standard"></a>Standard

A Standard szintű üzenetküldés, a Service Bus-üzenetsorok és témakörök az 1, 2, 3, 4 vagy 5 GB-os méret (az alapértelmezett érték 1 GB-os) hozhat létre. A particionálás engedélyezve van, a Service Bus másolatát hozza létre 16 (16 partíciók) a entitás minden GB, akkor adja meg. Mint ilyen, ha létrehoz egy sort, amely 5 GB-os méretig, a 16 partíciókat a várólista maximális mérete válik (5 \* 16) = 80 GB. A particionált üzenetsor vagy témakör maximális mérete láthatja hozzá tartozó bejegyzés megnézzük a [az Azure portal][Azure portal], a a **áttekintése** panel az adott entitáshoz.

### <a name="premium"></a>Prémium

Egy prémium szintű névtér particionálás az entitások nem támogatott. Azonban továbbra is létrehozhat Service Bus-üzenetsorok és témakörök az 1, 2, 3, 4, 5, 10, 20, 40 vagy 80 GB-os méret (az alapértelmezett érték 1 GB-os). Megjelenik az üzenetsor vagy témakör mérete hozzá tartozó bejegyzés megnézzük a [az Azure portal][Azure portal], a a **áttekintése** panel az adott entitáshoz.

### <a name="create-a-partitioned-entity"></a>A particionált entitás létrehozása

Többféleképpen is lehet létrehozni egy particionált üzenetsorra vagy témakörbe. Amikor az alkalmazás az üzenetsor vagy témakör hoz létre, particionálási üzenetsor vagy témakör rendre beállításával engedélyezheti a [QueueDescription.EnablePartitioning] [ QueueDescription.EnablePartitioning] vagy [ TopicDescription.EnablePartitioning] [ TopicDescription.EnablePartitioning] tulajdonságot **igaz**. Ezeket a tulajdonságokat kell beállítani az üzenetsor vagy témakör létrehozásakor, és csak a régebbi a kiváló [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) könyvtár. Ahogy korábban is hangsúlyoztuk, nincs lehetőség módosítani ezeket a tulajdonságokat a egy létező üzenetsorba vagy témakörbe. Példa:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Másik lehetőségként létrehozhat a particionált üzenetsor vagy témakör, a [az Azure portal][Azure portal]. A portálon, üzenetsor vagy témakör létrehozásakor a **particionálás engedélyezése** lehetőség az üzenetsor vagy témakör **létrehozás** párbeszédpanel alapértelmezés szerint be van jelölve. Is csak letiltja ezt a beállítást, a Standard szintű entitásokban; a prémium szintű a particionálás nem támogatott, és a jelölőnégyzet nem lesz hatása. 

## <a name="use-of-partition-keys"></a>Partíciós kulcsok használata

Amikor egy üzenet sorba egy particionált üzenetsorra vagy témakörbe történő, a Service Bus ellenőrzi, hogy az egy partíciókulcsot. Ha talál egyet, a részlet a kulcs alapján választja ki. Ha nem talál egy partíciókulcsot, a részlet egy belső algoritmus alapján választja ki.

### <a name="using-a-partition-key"></a>A partíciókulcsok használatával

Bizonyos példahelyzetekben a munkamenetek vagy tranzakciók, például egy adott töredék a tárolandó üzenetek szükséges. Ezek a forgatókönyvek olyan partíciókulcsot használatának megkövetelése. Ugyanazzal a partíciókulccsal használó összes üzenet ugyanarra a töredékre vannak hozzárendelve. Ha a részlet átmenetileg nem érhető el, a Service Bus hibát ad vissza.

A forgatókönyvtől függően a különböző üzenettulajdonságok partíciókulcsként használhatók:

**Munkamenet-azonosító**: üzenet-e a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) tulajdonságát állítsa be, majd a Service Bus használ **SessionID** partíciókulcsként. Ezzel a módszerrel a azonos közvetítő ugyanahhoz a munkamenethez tartozó összes üzenet kezeli. Munkamenetek engedélyezze a Service Bus üzenetrendezés, valamint a munkamenet-állapotok konzisztenciájának biztosítása érdekében.

**PartitionKey**: üzenet-e a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság, de nem a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) tulajdonságát állítsa be, majd a Service Bus használ a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság értéket partíciókulcsként. Ha az üzenet rendelkezik mind a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) és a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonságainak beállítása, mindkét tulajdonság azonosnak kell lennie. Ha a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonsága mint egy másik értéket a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) tulajdonság, a Service Bus érvénytelen művelet kivételt adja vissza. A [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság használandó, ha egy küldő küld a munkamenet-kompatibilis tranzakciós üzenetek. A partíciós kulcs biztosítja, hogy az azonos üzenetközvetítő tranzakción belül küldött összes üzenet kezeli.

**Üzenetazonosító**: Ha az üzenetsor vagy témakör a [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) tulajdonság **igaz** és a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) vagy [ PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonságai nincsenek beállítva, akkor a [üzenetazonosító](/dotnet/api/microsoft.azure.servicebus.message.messageid) tulajdonság értéke a partíciós kulcs szolgál. (A Microsoft .NET-keretrendszer és AMQP-kódtárakat automatikusan hozzárendel egy üzenet azonosítója, ha a küldő alkalmazás nem létezik.) Ebben az esetben ugyanazon üzenet összes másolatát az kezeli a azonos közvetítő. Ezt az Azonosítót lehetővé teszi, hogy a Service Bus és megszüntetheti az ismétlődő üzeneteket. Ha a [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) tulajdonság értéke nem **igaz**, Service Bus nem veszi figyelembe a [üzenetazonosító](/dotnet/api/microsoft.azure.servicebus.message.messageid) tulajdonságot partíciókulcsként.

### <a name="not-using-a-partition-key"></a>A partíciókulcsok nem használatával

A partíciós kulcs hiányában a Service Bus üzenetek a particionált üzenetsor vagy témakör összes töredékekre Ciklikus időszeleteléses módon osztja el. A kiválasztott töredék nem érhető el, ha a Service Bus hozzárendeli az üzenetet egy másik kódrészletet. Ezzel a módszerrel a küldési művelet sikeres, annak ellenére, hogy az átmeneti elérhetetlensége, valamint egy üzenetküldési tárolóban. Azonban Ön nem éri el a garantált rendezése, amelyek egy partíciókulcsot biztosít.

Rendelkezésre állás (nem partíciós kulcs) és a konzisztencia (használatával egy partíciókulcsot) közötti több részletes tárgyalását lásd: [Ez a cikk](../event-hubs/event-hubs-availability-and-consistency.md). Ezeket az információkat egyaránt particionált Service Bus-entitások vonatkozik.

Adni a Service Bus elegendő időt sorba helyezni az üzenetet be egy másik töredék a [így időtúllépés történt](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) az ügyfél által küldött üzenet 15 másodpercnél nagyobbnak kell lennie. megadott érték. Javasoljuk, hogy állítsa a [így időtúllépés történt](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) tulajdonságot az alapértelmezett érték 60 másodperc.

A partíciós kulcs "rögzítése" egy adott kódrészletet egy üzenet. Az üzenetkezelési tárba, amely tartalmazza az e töredékben nem érhető el, ha a Service Bus hibát ad vissza. Egy partíciókulcsot hiányában a Service Bus választhat egy másik kódrészletet, és a művelet sikeres. Ezért ajánlott, hogy nem ad meg partíciókulcsot, kivéve, ha szükség rá.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Speciális témakörök: tranzakciók használata particionált entitások

A tranzakciók keretében küldött üzenetekben meg kell adni egy partíciókulcsot. A kulcs a következő tulajdonságok egyike lehet: [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey), vagy [üzenetazonosító](/dotnet/api/microsoft.azure.servicebus.message.messageid). Ugyanazon tranzakció keretében küldött üzenetekben ugyanazt a partíciókulcsot kell adnia. Ha megpróbálja elküldeni egy üzenetet nem tartozik partíciós kulcs tranzakción belül, a Service Bus érvénytelen művelet kivételt adja vissza. Megkísérli az eltérő partíciókulcsok rendelkező ugyanazon a tranzakción belül több üzenetet küld, ha a Service Bus érvénytelen művelet kivételt adja vissza. Példa:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

Ha a tulajdonságok, amelyek egy partíciókulcsot szolgálhat, a Service Bus rögzíti az üzenet egy adott töredékre. Ez akkor fordul elő, egy tranzakció használja-e. Javasoljuk, hogy nincs megadva egy partíciókulcsot. Ha nem szükséges.

## <a name="using-sessions-with-partitioned-entities"></a>Munkamenetek használata particionált entitások

Tranzakciós üzenet küldése egy munkamenet-kompatibilis üzenettémából vagy üzenetsorból, az üzenet kell rendelkeznie a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) tulajdonság beállítása. Ha a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság is meg van adva, meg kell egyeznie a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) tulajdonság. Ha eltérnek, a Service Bus érvénytelen művelet kivételt adja vissza.

Normál (nem particionált) üzenetsorok és témakörök eltérően nincs lehetőség egy tranzakció több üzenetet küldhet különböző munkamenetek használandó. Próbálkozás esetén a Service Bus érvénytelen művelet kivételt adja vissza. Példa:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>A particionált entitások automatikus üzenetek továbbítása

A Service Bus továbbítási származó, a vagy particionált entitások közötti üzenetek automatikus támogatja. Ahhoz, hogy az automatikus üzenetek továbbítása, állítsa be a [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] tulajdonság a forrásüzenetsor vagy előfizetés esetében. Ha az üzenetet határozza meg, a partíciókulcs ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey), vagy [üzenetazonosító](/dotnet/api/microsoft.azure.servicebus.message.messageid)), a partíciókulcsot használt a cél az entitáshoz.

## <a name="considerations-and-guidelines"></a>Megfontolandó szempontok és irányelvek
* **Magas konzisztencia funkciók**: Ha egy entitás például munkamenetek, kettős észlelés vagy partíciós kulcs explicit irányítását funkciókat használ, akkor az üzenetküldési művelet mindig a meghatározott töredékekre legyenek irányítva. Ha bármelyik a szilánkok tapasztal nagy forgalom, illetve az alapul szolgáló tárolónak nem megfelelő állapotban, ezek a műveletek sikertelenek, és a rendelkezésre állási csökken. A teljes továbbra is sokkal magasabb, mint a nem particionált entitások;-e a konzisztencia forgalom egy része figyelésekor minden forgalom a problémák tapasztalhatók. További információkért lásd: Ez [rendelkezésre állás és konzisztencia](../event-hubs/event-hubs-availability-and-consistency.md).
* **Felügyeleti**: az entitás a szilánkok létrehozási, frissítési és törlési műveleteket kell elvégezni. Minden részlet állapota nem megfelelő, ha azt hibák ezeket a műveleteket eredményezhet. A Get művelethez információkat, mint például üzenet összesíteni kell az összes töredék. Ha minden részlet állapota nem megfelelő, entitás rendelkezésre állását az elvártnak megfelelően korlátozott.
* **Kis mennyiségű üzenet erőforrás esetén**: az ilyen esetek különösen akkor, ha a HTTP protokollt használja, előfordulhat, hogy rendelkezik több végrehajtásához fogadási műveletek összes üzenetet a beszerzéséhez. Fogadási kérelmek az előtér egy fogadás hajt végre a töredékek száma, és minden, a kapott válaszok gyorsítótárazza. Egy későbbi fogadási kérést, ugyanazon a kapcsolaton kihasználhatják a gyorsítótárazás és fogadni a késésük alacsonyabbak lesznek. Azonban, ha több kapcsolat van, vagy a HTTP Protokollt használja, amely egy új kapcsolatot létesít az egyes kérések. Mint ilyen nincs garancia arra, hogy azt kellene land ugyanazon a csomóponton. Ha az összes meglévő üzeneteket zárolva van, és tárolja a rendszer egy másik előtér, a receive műveletet adja vissza **null**. Üzenetek idővel jár le, és újra megkapja őket. HTTP életben tartást használata javasolt.
* **Tallózással keresse meg és betekintés üzenetek**: csak a régebbi a [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) könyvtár. [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nem mindig ad vissza a megadott üzenetek számát a [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) tulajdonság. Ez a viselkedés a két gyakori oka van. Egyik oka, hogy az üzenetek a gyűjtemény összesített mérete meghaladja a maximális 256 KB-os. A másik OK, hogy ha az üzenetsor vagy témakör a [EnablePartitioning tulajdonság](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) beállítása **igaz**, egy partíció nem rendelkezik elegendő üzenetek hajtsa végre a kért üzenetek száma. Általánosságban elmondható, ha meghatározott számú üzenet egy alkalmazás kéri, akkor meg kell hívnia [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) ismétlődő, amíg kap, hogy az üzenetek száma, vagy nincsenek a betekintés további üzenetek. További információk, beleértve a kódmintákban: a [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) vagy [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) API dokumentációja.

## <a name="latest-added-features"></a>Legújabb új funkciók

* Adja hozzá, vagy távolítsa el a szabály mostantól támogatják a particionált entitások. Nem particionált entitások eltérő, ezek a műveletek nem támogatottak a tranzakciókat. 
* Az AMQP támogatása az üzenetek küldése és fogadása, és a egy particionált entitás.
* AMQP mostantól támogatott a következő műveletek: [Batch küldése](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [Batch kap](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [fogadási sorszáma szerint](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [Belepillantás](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [ Zárolásának megújítása](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [üzenet ütemezése](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [ütemezett üzenet törlése](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [szabály hozzáadása](/dotnet/api/microsoft.azure.servicebus.ruledescription), [szabály eltávolítása](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Munkamenet zárolásának megújítása](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [munkamenet-állapot beállítása](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [Get munkamenet-állapot](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate), és [munkamenetek enumerálása](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Particionált entitások korlátozások

Jelenleg a Service Bus ír elő a következő korlátozások vonatkoznak a particionált üzenetsorok és témakörök:

* Particionált üzenetsorok és témakörök nem támogatottak a prémium szintű üzenetkezelési csomag. Munkamenetek támogatottak a premier szintű munkamenet-azonosító használatával. 
* Particionált üzenetsorok és témakörök nem támogatják az üzenetek küldése egy tranzakción belül különböző munkamenetek tartoznak.
* A Service Bus névterenként jelenleg 100 particionált üzenetsort vagy témakört képes kezelni. Minden egyes particionált üzenetsor vagy témakör felé számolnak (nem vonatkozik a prémium szintű) névterenként 10 000 entitások kvótáját.

## <a name="next-steps"></a>További lépések

Az alapvető fogalmait, meghatározása az üzenetkezelés az AMQP 1.0-s, olvassa el a [AMQP 1.0 protokoll – útmutató](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
