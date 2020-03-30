---
title: Az Azure Service Bus üzenetátvitelei, zárolásai és kiegyenlítése
description: Ez a cikk áttekintést nyújt az Azure Service Bus üzenetátvitelek, zárolások és kiegyenlítési műveletek ről.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: aschhab
ms.openlocfilehash: a2c353d612280981a83b32463d34efdc70878495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260994"
---
# <a name="message-transfers-locks-and-settlement"></a>Üzenetek átvitele, zárolása és elszámolása

Az üzenetközvetítő, például a Service Bus központi képessége, hogy üzeneteket fogad el egy várólistába vagy témakörbe, és későbbi lekérésre elérhetővé teszi őket. *A küldés* az a kifejezés, amelyet gyakran használnak egy üzenet átvitelére az üzenetközvetítőbe. *A fogadás* az a kifejezés, amelyet gyakran használnak üzenet nek a lekérő ügyfélnek történő átvitelére.

Amikor egy ügyfél üzenetet küld, általában azt szeretné tudni, hogy az üzenet megfelelően átkerült-e a brókerhez, és elfogadta-e, vagy valamilyen hiba történt. Ez a pozitív vagy negatív elismervény rendezi az ügyfelet és a közvetítőt az üzenet átadási állapotáról, és így *elszámolásnak*nevezik .

Hasonlóképpen, amikor a közvetítő átad egy üzenetet egy ügyfélnek, a bróker és az ügyfél meg akarja állapítani, hogy az üzenet feldolgozása sikeresen megtörtént-e, és ezért eltávolítható-e, vagy hogy az üzenet kézbesítése vagy feldolgozása sikertelen volt-e, és így a üzenet újra kézbesíteni kell.

## <a name="settling-send-operations"></a>Küldési műveletek kiegyenlítése

A támogatott Service Bus API-ügyfelek bármelyikének használatával a küldési műveletek a Service Bus-ba mindig explicit módon rendezve lesznek, ami azt jelenti, hogy az API-művelet megvárja a Service Bus elfogadási eredményének megérkezését, majd befejezi a küldési műveletet.

Ha a Service Bus elutasítja az üzenetet, az elutasítás tartalmaz egy hibajelzőt és egy "tracking-id" szöveget. Az elutasítás információkat is tartalmaz arról, hogy a művelet újrapróbálkozható-e a siker minden elvárásával. Az ügyfélben ez az információ kivételsé alakul, és a küldési művelet hívójának lesz megemelve. Ha az üzenet elfogadásra került, a művelet csendben befejeződik.

Az AMQP protokoll használatakor, amely a .NET Standard ügyfél és a Java ügyfél kizárólagos protokollja, és [amely a .NET Framework ügyfél számára is elérhető, az](service-bus-amqp-dotnet.md)üzenetátvitelek és -kiegyenlítések futószalagosak és teljesen aszinkronak, és ajánlott az aszinkron programozási modell API-változatainak használata.

A feladó több üzenetet is elhelyezhet a vezetéken gyors egymásutánban anélkül, hogy meg kellene várnia az egyes üzenetek nyugtázását, ahogy az egyébként az SBMP protokoll vagy a HTTP 1.1 esetében is történne. Ezek az aszinkron küldési műveletek a megfelelő üzenetek elfogadásával és tárolásával, particionált entitásokon vagy a különböző entitások nak történő küldési művelet átfedésben vannak. A befejezések az eredeti küldési rendelésből is előfordulhatnak.

A küldési műveletek kimenetelének kezelésére vonatkozó stratégia azonnali és jelentős teljesítménybeli hatással lehet az alkalmazásra. Az ebben a szakaszban található példák C# nyelven vannak megírva, és a Java Futures-re is vonatkoznak.

Ha az alkalmazás olyan üzenetsorozatokat hoz létre, amelyeket itt egy egyszerű ciklussal illusztrálnak, és meg kell várnia az egyes küldési műveletek befejezését a következő üzenet, szinkron vagy aszinkron API-alakzatok elküldése előtt, a 10 üzenet küldése csak 10 után fejeződik be egymást követő teljes oda-vissza utak a település.

A feltételezett 70 ezredmásodperces TCP oda-vissza késéstávolság egy helyszíni helyről a Service Bus-ra, és mindössze 10 ms-t ad a Service Bus-nak az egyes üzenetek fogadásához és tárolásához, a következő ciklus legalább 8 másodpercet vesz igénybe, nem számítva a hasznos adatátviteli időt vagy a potenciális az útvonali torlódások hatásai:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Ha az alkalmazás azonnal egymás után indítja el a 10 aszinkron küldési műveletet, és külön várja a megfelelő befejezést, a 10 küldési művelet oda-vissza útja átfedésben van. A 10 üzenet azonnali egymásutánban kerül átvitelre, potenciálisan még a TCP-keretek megosztásával is, és a teljes átviteli időtartam nagymértékben függ a hálózattal kapcsolatos időtől, amely az üzenetek átviteléhez szükséges a brókernek.

Ha ugyanazokat a feltételezéseket készíti, mint az előző ciklusesetében, a következő ciklus teljes átfedésben lévő végrehajtási ideje jóval egy másodperc alatt maradhat:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Fontos megjegyezni, hogy minden aszinkron programozási modell valamilyen memóriaalapú, rejtett munkavárólistát használ, amely a függőben lévő műveleteket tartalmazza. Amikor [a SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) vagy a **Send** (Java) visszaküldi, a küldési feladat várólistára kerül a munkavárólistában, de a protokoll kézmozdulat csak akkor kezdődik, ha a feladat nak futnia kell. A kód, amely hajlamos a push tört az üzenetek, és ahol a megbízhatóság aggodalomra ad okot, ügyelni kell arra, hogy nem túl sok üzenetet tesznek "repülés" egyszerre, mert minden elküldött üzenetek et memóriát, amíg azok ténylegesen került rá a vezetéket.

A szemaforok, ahogy az a C#-ban a következő kódrészletben látható, olyan szinkronizálási objektumok, amelyek szükség esetén engedélyezik az ilyen alkalmazásszintű szabályozást. Ez a szemafor használata lehetővé teszi, hogy legbőleg10 üzenet egyszerre repülhet. A küldés előtt a 10 rendelkezésre álló szemafor-zárolás egyike kerül a küldés elé, és a küldés befejeződésekor felszabadul. A 11-es áthaladás a hurkon megvárja, amíg az előző küldések legalább egyike befejeződik, majd elérhetővé teszi a zárolást:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

Az alkalmazások **soha ne** kezdeményezzenek aszinkron küldési műveletet "tűz és felejtsd el" módon a művelet eredményének beolvasása nélkül. Ezzel betöltheti a belső és láthatatlan feladatvárólistát a memória kimerülésig, és megakadályozhatja, hogy az alkalmazás észlelje a küldési hibákat:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Egy alacsony szintű AMQP-ügyféllel a Service Bus is elfogadja az "előre kiegyenlített" átviteleket. Az előre kiegyenlített átvitel olyan tűz-és-felejtés művelet, amelynek kimenetelét, akár így, úgy nem jelentik vissza az ügyfélnek, és az üzenet elküldéskor rendezettnek minősül. Az ügyfélnek küldött visszajelzés hiánya azt is jelenti, hogy nincs használható adat a diagnosztikához, ami azt jelenti, hogy ez a mód nem jogosult az Azure-támogatáson keresztül nyújtott segítségre.

## <a name="settling-receive-operations"></a>Rendezési fogadási műveletek

A fogadási műveletekhez a Service Bus API-ügyfelek két különböző explicit módot engedélyeznek: *fogadásés törlés* és *betekintés-zárolás.*

### <a name="receiveanddelete"></a>Fogadás és törlés

A [fogadás és törlés](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód azt mondja a brókernek, hogy a fogadó ügyfélnek küldött összes üzenetet úgy tekintse meg, mint egyeztetette, amikor elküldte. Ez azt jelenti, hogy az üzenet fogyasztásnak minősül, amint a bróker feltette a vezetékre. Ha az üzenet átvitele sikertelen, az üzenet elvész.

Ennek az üzemmódnak a előnye, hogy a vevőnek nem kell további lépéseket tennie az üzenettel, és nem is lassítja a település eredményének megvárásával. Ha az egyes üzenetekben szereplő adatok értéke alacsony, és/vagy csak nagyon rövid ideig értelmezhetők, ez a mód ésszerű választás.

### <a name="peeklock"></a>PeekLock (Kukucsk

A [Peek-Lock](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód közli a közvetítővel, hogy a fogadó ügyfél explicit módon szeretné rendezni a fogadott üzeneteket. Az üzenetet a címzett feldolgozza, miközben a szolgáltatás ban kizárólagos zár alatt tartja, hogy más, versengő fogadók ne láthassák azt. A zárolás időtartama kezdetben a várólista vagy az előfizetés szintjén van meghatározva, és a zárolást birtokló ügyfél a [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) műveleten keresztül bővíthető.

Ha egy üzenet zárolva van, az ugyanabból a várólistából vagy előfizetésből fogadó többi ügyfél zárolhatja a zárolásokat, és lekérheti a következő elérhető üzeneteket, amelyek nem aktív zárolás alatt vannak. Amikor egy üzenet zárolása explicit módon felszabadul, vagy amikor a zárolás lejár, az üzenet a visszaolvasási rendelés elején vagy annak közelében jelenik meg.

Amikor az üzenetet a fogadók ismételten kiadják, vagy egy meghatározott számú alkalommal[(maxDeliveryCount)](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)hagyják a zárolást elengedni, az üzenet automatikusan törlődik a várólistából vagy az előfizetésből, és a társított kézbesítetlen levelek várólistájába kerül.

A fogadó ügyfél kezdeményezi a fogadott üzenet kiegyenlítését pozitív nyugtával, amikor az API szintjén [complete-t](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) hív meg. Ez azt jelzi a közvetítőnek, hogy az üzenet feldolgozása sikeresen megtörtént, és az üzenet eltávolításra kerül a várólistából vagy az előfizetésből. A közvetítő válaszol a fogadó kiegyenlítési szándékára egy válaszsal, amely jelzi, hogy a kiegyenlítés elvégezhető-e.

Ha a fogadó ügyfél nem tudja feldolgozni az üzenetet, de azt akarja, hogy az üzenetet újra kézbesítse, akkor kifejezetten kérheti az üzenet kiadását és zárolásának feloldását azonnal az [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) hívásával, vagy nem tehet semmit, és hagyja, hogy a zárolás elteljen.

Ha a fogadó ügyfél nem tudja feldolgozni az üzenetet, és tudja, hogy az üzenet újbóli kézbesítése és a művelet újbóli megkísérlése nem segít, elutasíthatja az üzenetet, amely a kézbesítetlen levelek várólistájába helyezi át a [kézbesítetlen](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter)levelek várólistájának hívásával, amely lehetővé teszi egy egyéni tulajdonság beállítását is, beleértve egy okkódot, amely a kézbesítetlen levelek várólistájából lehívható üzenettel együtt lehívható.

Az egyezség különleges esete a halasztás, amelyet egy külön cikk tárgyal.

A **Teljes** vagy **a Holtlevél-műveletek,** valamint a **RenewLock** műveletek hálózati problémák miatt sikertelenek lehetnek, ha a zárolt zárolás lejárt, vagy más szolgáltatásoldali feltételek akadályozzák a kiegyenlítést. Az utóbbi esetek egyikében a szolgáltatás negatív nyugtázást küld, amely kivételként megjelenik az API-ügyfelekben. Ha ennek oka egy megszakadt hálózati kapcsolat, a zárolás megszakad, mivel a Service Bus nem támogatja a meglévő AMQP-kapcsolatok helyreállítását egy másik kapcsolaton.

Ha **a Complete** meghibásodik, amely általában az üzenetkezelés legvégén és bizonyos esetekben a feldolgozási munka percei után következik be, a fogadó alkalmazás eldöntheti, hogy megőrzi-e a munka állapotát, és figyelmen kívül hagyja ugyanazt az üzenetet, amikor másodszor kézbesítik, vagy kinyomja-e a munka eredményét, és újrapróbálkozik az üzenet újbóli kézbesítése közben.

Az ismétlődő üzenetkézbesítések azonosításának tipikus mechanizmusa az üzenetazonosító ellenőrzésével, amelyet a feladó egyedi értékre állíthat be, és amelyet esetleg az eredeti folyamat azonosítójához igazítva állíthat be. A feladatütemező valószínűleg az üzenetazonosítót annak a feladatnak az azonosítójára állítja be, amelyet az adott dolgozóval egy dolgozóhoz próbál hozzárendelni, és a dolgozó figyelmen kívül hagyná a feladat-hozzárendelés második előfordulását, ha a feladat már befejeződött.

> [!IMPORTANT]
> Fontos megjegyezni, hogy a PeekLock által az üzeneten beszerzett zár illékony, és elveszhet a következő körülmények között
>   * Szolgáltatás frissítése
>   * Operációs rendszer frissítése
>   * Az entitás (Várólista, Témakör, Előfizetés) tulajdonságainak módosítása a zárolás megtartása közben.
>
> Ha a zárolás megszakad, az Azure Service Bus létrehoz egy LockLostException, amely megjelenik az ügyfélalkalmazás-kód. Ebben az esetben az ügyfél alapértelmezett újrapróbálkozási logikája automatikusan beindul, és újra meg kell próbálnia a műveletet.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Bus üzenetküldéséről, olvassa el az alábbi témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
