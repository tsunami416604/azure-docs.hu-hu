---
title: Particionált Azure Service Bus-várólisták és témakörök létrehozása | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogyan particionálhat a Service Bus-várólisták és témakörök több üzenet-közvetítő használatával.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 02/06/2020
ms.author: aschhab
ms.openlocfilehash: 671368993acb43c0d55eca73119effa934e3cff8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260942"
---
# <a name="partitioned-queues-and-topics"></a>Particionált üzenetsorok és témakörök

Az Azure Service Bus több üzenetközvetítőt alkalmaz az üzenetek feldolgozására, és több üzenettárolót az üzenetek tárolására. A hagyományos várólistát vagy témakört egyetlen üzenetközvetítő kezeli, és egy üzenettárolóban tárolja. A Service *Bus-partíciók* lehetővé teszik a várólisták és témakörök, illetve *üzenetküldési entitások*particionálását több üzenetközvetítő és üzenettároló között. Particionálás azt jelenti, hogy a particionált entitás teljes átviteli teljesítményét már nem korlátozza a teljesítményét egyetlen üzenet-közvetítő vagy üzenetküldési tároló. Ezenkívül az üzenettároló ideiglenes kimaradása nem teszi elérhetetlenné a particionált várólistát vagy témakört. Partiparti várólisták és témakörök tartalmazhatnak az összes speciális Service Bus funkciók, például a tranzakciók és a munkamenetek támogatása.

> [!NOTE]
> Particionálás érhető el az entitás létrehozása az összes várólisták és témakörök alapszintű vagy standard termékkészletben érhető el. Nem érhető el a prémium szintű üzenetküldési termékváltozat, de a prémium szintű névterekben korábban meglévő particionált entitások továbbra is a várt módon működik.
 
A particionálási lehetőség nem módosítható egyetlen meglévő várólistán vagy témakörben sem; csak az entitás létrehozásakor állíthatja be a beállítást.

## <a name="how-it-works"></a>Működés

Minden particionált várólista vagy témakör több partícióból áll. Minden partíció egy másik üzenettárolóban van tárolva, és egy másik üzenetközvetítő kezeli. Amikor egy üzenetet egy particionált várólistába vagy témakörbe küld, a Service Bus hozzárendeli az üzenetet az egyik partícióhoz. A kiválasztás véletlenszerűen történik a Service Bus vagy egy partíciós kulccsal, amelyet a feladó megadhat.

Ha egy ügyfél egy particionált várólistából vagy egy particionált témakörre való előfizetésből szeretne üzenetet kapni, a Service Bus lekérdezi az összes partíciót az üzenetekhez, majd visszaadja az első üzenetet, amely az üzenettárolók bármelyikéből beérkezik a fogadónak. A Service Bus gyorsítótárazza a többi üzenetet, és visszaküldi azokat, amikor további fogadási kérelmeket kap. A fogadó ügyfél nem ismeri a particionálást; egy particionált várólista vagy témakör ügyfélfelé néző viselkedése (például olvasás, teljes, halasztás, kézbesítetlen levél, előzetes betöltés) megegyezik egy normál entitás viselkedésével.

Nincs további költség, ha üzenetet küld, vagy egy üzenetet kap, egy particionált várólista vagy témakör.

## <a name="enable-partitioning"></a>Particionálás engedélyezése

Particionált várólisták és témakörök használatához az Azure Service Bus használatával használja az `api-version=2013-10` Azure SDK 2.2-es vagy újabb verzióját, vagy adja meg vagy később a HTTP-kérelmekben.

### <a name="standard"></a>Standard

A standard üzenetkezelési rétegben 1, 2, 3, 4 vagy 5 GB-os méretben hozhat létre Service Bus-várólistákat és témaköröket (az alapértelmezett érték 1 GB). Ha a particionálás engedélyezve van, a Service Bus 16 példányt (16 partíciót) hoz létre az entitásból, amelyek mindegyike azonos méretű. Ebben az esetben, ha 5 GB méretű várólistát hoz létre, 16 partícióval \* a maximális várólistaméret (5 16) = 80 GB lesz. A particionált várólista vagy témakör maximális méretét az [Azure Portalon][Azure portal]való bejegyzésmegtekintésével tekintheti meg, az adott entitás **áttekintő** paneljén.

### <a name="premium"></a>Prémium

Prémium szintű névtérben a particionáló entitások nem támogatottak. A Service Bus-várólistákat és témaköröket azonban továbbra is létrehozhat 1, 2, 3, 4, 5, 10, 20, 40 vagy 80 GB-os méretben (az alapértelmezett érték 1,2, 3, 4, 5, 10, 20, 40 vagy 80 GB méretű (az alapértelmezett érték 1 GB). Megtekintheti a várólista vagy a témakör méretét az [Azure Portalon][Azure portal]való bejegyzésmegtekintésével, az adott entitás **áttekintő** paneljén.

### <a name="create-a-partitioned-entity"></a>Particionált entitás létrehozása

Particionált várólista vagy témakör többféleképpen is létrehozható. Amikor az alkalmazásból hozza létre a várólistát vagy a témakört, engedélyezheti a particionálást a várólistához vagy a témakörhöz, ha a [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] vagy [topicDescription.EnablePartitioning tulajdonságot][TopicDescription.EnablePartitioning] **true**értékre állítja. Ezeket a tulajdonságokat a várólista vagy a témakör létrehozásakor kell beállítani, és csak a régebbi [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) könyvtárban érhetők el. Ahogy azt korábban említették, ezeket a tulajdonságokat nem lehet módosítani egy meglévő várólistán vagy témakörben. Példa:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Másik lehetőségként létrehozhat egy particionált várólistát vagy témakört az [Azure Portalon.][Azure portal] Amikor várólistát vagy témakört hoz létre a portálon, a **particionálás engedélyezése** beállítás a várólista vagy a **létrehozás** párbeszédpanelen alapértelmezés szerint be van jelölve. Ezt a beállítást csak standard szintű entitásban tilthatja le; a prémium szintű particionálás nem támogatott, és a jelölőnégyzet nincs hatása. 

## <a name="use-of-partition-keys"></a>Partíciókulcsok használata

Ha egy üzenet van várólistára egy particionált várólista vagy témakör, Service Bus ellenőrzi a partíciókulcs jelenlétét. Ha talál egyet, akkor kiválasztja a partíciót a kulcs alapján. Ha nem talál partíciókulcsot, belső algoritmus alapján választja ki a partíciót.

### <a name="using-a-partition-key"></a>Partíciókulcs használata

Egyes forgatókönyvek, például a munkamenetek vagy a tranzakciók, az üzenetek et egy adott partíción kell tárolni. Mindezek a forgatókönyvek partíciókulcs használatát igénylik. Minden olyan üzenet, amely ugyanazt a partíciókulcsot használja, ugyanahhoz a partícióhoz van rendelve. Ha a partíció átmenetileg nem érhető el, a Service Bus hibát ad vissza.

A forgatókönyvtől függően a különböző üzenettulajdonságok partíciókulcsként használatosak:

**SessionId**: Ha egy üzenethez a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) tulajdonság van beállítva, akkor a Service Bus **a SessionID azonosítót** használja partíciókulcsként. Így az azonos munkamenethez tartozó összes üzenetet ugyanaz az üzenetközvetítő kezeli. A munkamenetek lehetővé teszik, hogy a Service Bus garantálja az üzenetek rendezését, valamint a munkamenet-állapotok konzisztenciáját.

**PartitionKey**: Ha egy üzenet rendelkezik a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonsággal, de nem a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) tulajdonságkészlettel, akkor a Service Bus a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonságértéket használja partíciókulcsként. Ha az üzenet hez [a SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) és a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság is be van állítva, mindkét tulajdonságnak azonosnak kell lennie. Ha a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság értéke eltér a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) tulajdonságétól, a Service Bus érvénytelen műveletkivételt ad vissza. A [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonságot akkor kell használni, ha a feladó nem munkamenet-barát tranzakciós üzeneteket küld. A partíciókulcs biztosítja, hogy a tranzakción belül küldött összes üzenetet ugyanaz az üzenetküldési közvetítő kezelje.

**MessageId:** Ha a várólista vagy a témakör a [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) tulajdonság **true** értékre van állítva, és a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) vagy a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonságok nincsenek beállítva, akkor a [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) tulajdonság értéke szolgál partíciókulcsként. (A Microsoft .NET és AMQP tárak automatikusan hozzárendelik az üzenetazonosítót, ha a küldő alkalmazás nem.) Ebben az esetben ugyanannak az üzenetnek az összes példányát ugyanaz az üzenetközvetítő kezeli. Ez az azonosító lehetővé teszi, hogy a Service Bus észlelje és megszüntesse az ismétlődő üzeneteket. Ha a [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) tulajdonság értéke nincs **true**értékre, a Service Bus nem tekinti a [MessageId tulajdonságot](/dotnet/api/microsoft.azure.servicebus.message.messageid) partíciókulcsnak.

### <a name="not-using-a-partition-key"></a>Nem használ partíciókulcsot

Partíciókulcs hiányában a Service Bus ciklikus multiplexelési módon osztja el az üzeneteket a particionált várólista vagy témakör összes partíciójára. Ha a kiválasztott partíció nem érhető el, a Service Bus hozzárendeli az üzenetet egy másik partícióhoz. Így a küldési művelet sikeres, annak ellenére, hogy az üzenettároló ideiglenesen nem áll rendelkezésre. Azonban nem fogja elérni a partíciókulcs által biztosított garantált rendelést.

A rendelkezésre állás (nincs partíciókulcs) és a konzisztencia (partíciókulcs használatával) közötti kompromisszum részletesebb megvitatását [ebben a cikkben](../event-hubs/event-hubs-availability-and-consistency.md)olvashatja el. Ez az információ egyaránt vonatkozik a particionált Service Bus entitásokra.

Ahhoz, hogy a Service Bus elegendő időt az üzenet várólistára egy másik partícióra, az ügyfél által megadott, az üzenetet küldő [operationtimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) értéknek 15 másodpercnél nagyobbnak kell lennie. Javasoljuk, hogy állítsa be az [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) tulajdonságot az alapértelmezett 60 másodperces értékre.

A partíciókulcs "rögzíti" az üzenetet egy adott partícióra. Ha a partíciót tároló üzenettároló nem érhető el, a Service Bus hibát ad vissza. Partíciókulcs hiányában a Service Bus másik partíciót választhat, és a művelet sikeres. Ezért ajánlott, hogy ne adja meg a partíciókulcsot, kivéve, ha szükséges.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Speciális témakörök: tranzakciók használata particionált entitásokkal

A tranzakciók keretében küldött üzenetekben meg kell adni egy partíciókulcsot. A kulcs a következő tulajdonságok egyike lehet: [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)vagy [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Az ugyanazon tranzakció részeként küldött összes üzenetnek ugyanazt a partíciókulcsot kell megadnia. Ha egy tranzakción belül partíciókulcs nélkül próbál üzenetet küldeni, a Service Bus érvénytelen műveletkivételt ad vissza. Ha ugyanazon a tranzakción belül különböző partíciókulcsokkal rendelkező üzeneteket próbál küldeni, a Service Bus érvénytelen műveletkivételt ad vissza. Példa:

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

Ha a partíciókulcsként szolgáló tulajdonságok bármelyike be van állítva, a Service Bus egy adott partícióra tűzi az üzenetet. Ez a viselkedés akkor fordul elő, függetlenül attól, hogy egy tranzakciót használnak-e vagy sem. Ha nem szükséges, ne adjon meg partíciókulcsot.

## <a name="using-sessions-with-partitioned-entities"></a>Munkamenetek használata particionált entitásokkal

Ha tranzakciós üzenetet szeretne küldeni egy munkamenet-barát témakörnek vagy várólistának, az üzenetnek a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) tulajdonsággal kell rendelkeznie. Ha a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság is meg van adva, akkor meg kell egyeznie a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) tulajdonsággal. Ha ezek eltérnek, a Service Bus érvénytelen műveletkivételt ad vissza.

A normál (nem particionált) várólistákkal vagy témakörökkel ellentétben egyetlen tranzakció val a különböző munkamenetek számára nem lehet több üzenetet küldeni. Ha megkísérli, a Service Bus érvénytelen műveletkivételt ad vissza. Példa:

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

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatikus üzenettovábbítás particionált entitásokkal

A Service Bus támogatja a particionált entitásokból, azokba vagy azok között küldött automatikus üzenettovábbítást. Az automatikus üzenettovábbítás engedélyezéséhez állítsa be a [QueueDescription.ForwardTo][QueueDescription.ForwardTo] tulajdonságot a forrásvárólistán vagy az előfizetésen. Ha az üzenet partíciókulcsot[(SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)vagy [MessageId)](/dotnet/api/microsoft.azure.servicebus.message.messageid)ad meg, a rendszer a célentitáshoz használja a partíciókulcsot.

## <a name="considerations-and-guidelines"></a>Szempontok és iránymutatások
* **Magas konzisztenciafunkciók:** Ha egy entitás olyan szolgáltatásokat használ, mint a munkamenetek, a duplikáltelem-észlelés vagy a particionálási kulcs explicit vezérlése, akkor az üzenetkezelési műveletek mindig egy adott partícióra lesznek irányítva. Ha a partíciók bármelyike nagy forgalmat tapasztal, vagy az alapul szolgáló tároló nem megfelelő állapotú, ezek a műveletek sikertelenek, és a rendelkezésre állás csökken. Összességében a konzisztencia még mindig sokkal magasabb, mint a nem particionált entitások; csak egy részhalmaza a forgalom tapasztalható problémák, szemben az összes forgalmat. További információt a [rendelkezésre állásról és a konzisztenciáról szóló témakörben talál.](../event-hubs/event-hubs-availability-and-consistency.md)
* **Kezelés:** Az olyan műveleteket, mint a Létrehozás, a Frissítés és a Törlés, az entitás összes partícióján végre kell hajtani. Ha bármelyik partíció nem megfelelő állapotú, ez a műveletek hibákat eredményezhet. A Get művelethez az olyan információkat, mint például az üzenetek száma, összesíteni kell az összes partícióról. Ha bármelyik partíció nem kifogástalan állapotú, az entitás rendelkezésre állási állapota korlátozottként lesz jelentve.
* **Kis mennyiségű üzenet forgatókönyvek**: Az ilyen esetekben, különösen a HTTP protokoll használata esetén, előfordulhat, hogy több fogadási műveletet kell végrehajtania az összes üzenet beszerzéséhez. A fogadási kérelmek esetén az előtér az összes partíción fogadást hajt végre, és gyorsítótárazza az összes kapott választ. Egy későbbi fogadási kérelem ugyanazon a kapcsolaton előnyös lenne ez a gyorsítótárazás, és a késések alacsonyabb lesz. Ha azonban több kapcsolattal rendelkezik, vagy HTTP-t használ, amely minden kéréshez új kapcsolatot hoz létre. Mint ilyen, nincs garancia arra, hogy ugyanazon a csomóponton landolna. Ha az összes meglévő üzenet zárolva van, és egy másik előtérben van gyorsítótárazva, a fogadási művelet **null értéket**ad vissza. Az üzenetek végül lejárnak, és újra megkaphatja őket. HTTP életben tartás ajánlott. Particionálás használata esetén kis mennyiségű forgatókönyvek, fogadási műveletek a vártnál hosszabb időt vehet igénybe. Ezért azt javasoljuk, hogy ne használja a particionálás ezekben a forgatókönyvekben. Törölje a meglévő particionált entitásokat, és hozza létre őket újra a particionálás letiltva a teljesítmény javítása érdekében.
* **Tallózás/betekintés üzenetek:** Csak a régebbi [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) könyvtárban érhető el. [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nem mindig adja vissza a [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) tulajdonságban megadott üzenetek számát. Ennek a viselkedésnek két gyakori oka van. Ennek egyik oka, hogy az üzenetek gyűjteményének összesített mérete meghaladja a 256 KB maximális méretet. A másik ok az, hogy ha a várólista vagy a témakör [enablepartitioning tulajdonságértéke](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) **igaz**, előfordulhat, hogy a partíció nem rendelkezik elegendő üzenettel a kért számú üzenet végrehajtásához. Általában, ha egy alkalmazás szeretne kapni egy adott számú üzenetet, meg kell hívni [peekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) többször, amíg nem kapja meg az üzenetek száma, vagy nincs több üzenet betekintés. További információért, beleértve a kódmintákat, olvassa el a [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) vagy [a SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) API dokumentációját.

## <a name="latest-added-features"></a>Legújabb hozzáadott funkciók

* A particionált entitások mostantól támogatják a szabály hozzáadása vagy eltávolítása. A nem particionált entitásoktól eltérően ezek a műveletek nem támogatottak a tranzakciók ban. 
* Az AMQP mostantól támogatott a particionált entitásba érkező és onnan érkező üzenetek küldéséhez és fogadásához.
* Az AMQP mostantól a következő műveletekhez támogatott: [Kötegelt küldés,](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch) [Kötegelt fogadás](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [Fogadás sorszám szerint,](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) [Betekintés,](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek) [Zárolás megújítása](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [Ütemezési üzenet](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), Ütemezett üzenet [megszakítása,](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync) [Szabály hozzáadása](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Szabály eltávolítása](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Munkamenet-megújítási zárolás](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [Munkamenet-állapot beállítása](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [Munkamenet-állapot beolvasása](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)és [Munkamenet-szám törlése](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Particionált entitások korlátozásai

A Service Bus jelenleg a következő korlátozásokat írja elő a particionált várólistákra és témakörökre vonatkozóan:

* Particionált várólisták és témakörök nem támogatottak a prémium üzenetkezelési rétegben. Munkamenet-munkamenetek támogatja a premier szintű sessionid használatával. 
* A particionált várólisták és témakörök nem támogatják az olyan üzenetek küldését, amelyek egyetlen tranzakció különböző munkameneteihez tartoznak.
* A Service Bus névterenként jelenleg 100 particionált üzenetsort vagy témakört képes kezelni. Minden particionált várólista vagy témakör névtérenként 10 000 entitás kvótájába számít (nem vonatkozik a prémium szintű szintre).

## <a name="next-steps"></a>További lépések

Az AMQP 1.0 üzenetküldési specifikációjának alapfogalmairól az [AMQP 1.0 protokollútmutatóban](service-bus-amqp-protocol-guide.md)olvashat.

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
