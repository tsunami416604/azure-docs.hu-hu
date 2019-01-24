---
title: Az Azure Service Bus-üzenet adatátvitel, zárolások és elszámolás |} A Microsoft Docs
description: A Service Bus-üzenet adatátvitel és elszámolás műveletek áttekintése
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
ms.date: 09/25/2018
ms.author: aschhab
ms.openlocfilehash: a78409a15acb4e60fc4200778d0f33b3fb566e85
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846441"
---
# <a name="message-transfers-locks-and-settlement"></a>Üzenetek átvitele, zárolása és elszámolása

Például a Service Bus egy közvetítő központi képességét, hogy fogadja az üzeneteket az üzenetsor vagy témakör, és tartsa elérhető a későbbi beolvasásához. *Küldés* a kifejezés, és a egy üzenet átvitelét be a közvetítő gyakran használják. *Kap* a kifejezés a lekérése során ügyfél üzenet átvitelét gyakran használják.

Amikor egy ügyfél egy üzenetet küld, általában szeretné tudni, hogy az üzenet megtörtént megfelelően át és fogadja el a közvetítő vagy valamilyen hiba lépett fel. Pozitív vagy negatív elfogadnia rendezi az ügyfél és a közvetítő ismertetése a üzenet átviteli állapotát, és így nevezzük *elszámolás*.

Hasonlóképpen, a közvetítő adatforgalom egy üzenetet egy ügyfélre, amikor a broker és az ügyfél szeretne hozhat létre e az üzenet feldolgozása sikeresen megtörtént, és ezért távolítható el, vagy hogy az üzenetek kézbesítését vagy a feldolgozás sikertelen volt, és így a üzenetet kézbesíteni újra lehet.

## <a name="settling-send-operations"></a>Műveletek stabilizálódási

A Service Bus API támogatott ügyfelek bármelyikével küldése a Service Bus műveletek mindig explicit módon kiegyenlített, ami azt jelenti, hogy az API-művelet megvárja, amíg egy kimarad, a Service Bus elfogadásának eredményét, és befejezi a küldési művelet.

Az üzenet Service Bus által elutasítása esetén az elutasítás tartalmaz egy hibát jelző és a egy "követési-azonosító" belül, a szöveg. Az elutasítás e a művelet végrehajtásával lehet újrapróbálkozni a siker bármely elvárás kapcsolatos információkat is tartalmaz. Az ügyfél ezt az információt kivétel be van kapcsolva, és a hívónak a küldési művelet kiváltása. Ha az üzenet már helyesen megadta, a művelet csendes befejeződik.

Az AMQP protokollt, amelyhez kizárólagos protokoll, a .NET Standard ügyféloldali és a Java ügyfél használatakor és [a .NET-keretrendszer ügyfél lehetőség, amely](service-bus-amqp-dotnet.md), üzenet adatátvitel és rendezések vagyok, és teljesen aszinkron, és az aszinkron programozási modell API variantní hodnoty használata ajánlott.

Küldő helyezhet több üzenet átkerül gyors egymás után nem kell megvárnia az egyes üzenetekhez arra vonatkozik, ahogy lenne az eset a SBMP protokoll vagy a HTTP 1.1. Ezek a műveletek aszinkron küldés hajtható végre, mert a megfelelő üzenetek fogadja és tárolja, particionált entitások, vagy amikor művelet küldeni a különböző entitások között átfedés van. A befejezések is az eredeti küldésének sorrendje nem fordulhatnak elő.

A kezelési műveletek eredményeit stratégiát az alkalmazás teljesítmény azonnali és jelentős hatással lehet. Ebben a szakaszban szereplő példák C# nyelven íródtak, és év Java Futures a alkalmazni.

Ha az alkalmazást hoz létre az üzenetek adatlöketekkel, bemutatott egyszerű hurkot, és await befejezése után a rendszer az egyes küldeni a művelet a következő, a szinkron üzenet elküldése előtt, vagy a jelenlegi funkcionalitása a aszinkron API alakzatokat, csak 10 üzenetet küld befejezése után 10 egymást követő teljes adatváltások rendezésre.

Az egy feltételezett 70 ezredmásodperces TCP körbejárási késés távolság egy helyszíni hely a Service Bus és a csak 10 ms-Service Bus számára, amely fogadja el, és minden üzenetet tárolására a következő ciklus foglal legalább 8 másodperc, nem számítva a tartalom átvitelének az idejét vagy potenciális útvonal torlódás hatásai:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Ha az alkalmazást elindítja a 10 aszinkron műveletek azonnali egymás után, és megfelelő megszakíthat külön-külön várja, ideje a 10 küldési műveletek átfedésben van. A 10 üzenetet átkerülnek a közvetlen egymás után, potenciálisan akár megosztása TCP keretek, és az összesített átviteli időtartam nagymértékben függ a hálózattal kapcsolatos szükséges időt első továbbítja az üzeneteket a közvetítőn.

Így az azonos feltételezéseket mint a korábbi hurok, a következő ciklus átfedett teljes végrehajtási idő még aktívak maradhatnak, és a egy másodperc:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Fontos megjegyezni, hogy minden aszinkron programozási modellek használata valamilyen memória-alapú, rejtett várólistát, amely tartalmazza a függőben lévő műveletek. Amikor [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) vagy **küldése** (Java) lépjen vissza, a küldési feladat az adott várólistát várólistára, de csak a protokoll hitelesítési módok megkezdése után a feladat következik futtatásához. Leküldéses üzenetek, és ahol megbízhatóság fontos adatlöketekkel általában kódot, a gondot kell fordítani, hogy túl sok üzenet kerüljenek "útban" egyszerre, mert az összes elküldött üzenetek is igénybe vehet memória addig, amíg azok ténylegesen megtörtént helyezett poslat.

Szemaforok használatát, ahogyan az a következő kódrészletet a C#, olyan szinkronizálási objektumok, amelyek lehetővé teszik, ilyen alkalmazásszintű szabályozás szükség esetén. Ez egy szemafor használata lehetővé teszi a útban lehet egyszerre legfeljebb 10 üzeneteket. A 10 elérhető szemafor zárolása egyike használatban van a küldés előtt, és akkor szabadul fel, mint a Küldés befejeződik. A hurok megvárja, amíg legalább egy előzetes küld 11 átengedési befejeződött, és majd elérhetővé teszi a zárolás:

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

Alkalmazások kell **soha nem** kezdeményezzen egy aszinkron küldési művelet "indul el, és felejtse el" módon anélkül, hogy a művelet eredményének beolvasása. Ezzel a elfogyott a memória akár belső, láthatatlan feladat-várólista betöltése, és megakadályozhatja, hogy az alkalmazás a küldési hibáinak észleléséhez:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Az alacsony szintű AMQP-ügyfél a Service Bus "előre kiegyenlített" átvitel is fogad. Egy előre kiegyenlített átadása egy fire és elfelejt művelet, amelynek az eredménye, mindkét módszer esetén nem készül jelentés vissza az ügyfél és az üzenet akkor tekinthető küldött rendezni. Visszajelzés az ügyfélnek hiánya is jelenti, hogy nem szerepel megjeleníthető hasznos adat elérhető diagnosztikai, ami azt jelenti, hogy ebben a módban nem tesz eleget keresztül az Azure-támogatás segítségét.

## <a name="settling-receive-operations"></a>Stabilizálódási fogadási műveletek

A fogadási műveletek, a Service Bus API-ügyfelek kétféle explicit módon engedélyezése: *Fogadása és-törlés* és *betekintési zárolással való*.

A [fogadása és törlése](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód arra utasítja a közvetítő kell figyelembe venni az összes üzenetet küld a fogadó ügyfél kiegyenlített, mikor küldött. Ez azt jelenti, hogy az üzenet számít felhasznált, amint a közvetítő állapotúra állította, poslat. Ha az üzenet átvitel sikertelen, az üzenet elveszik.

A feje az ebben a módban, hogy a fogadó nem kell további műveleteket az üzenetet, és nem is lassította várakozással az elszámolás eredményét. Ha az egyes üzenetek szereplő adatokat alacsony értéket és/vagy a rendszer csak nagyon rövid ideig jelentéssel bíró, ez a mód az ésszerű választás.

A [betekintési zárolással való](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód arra utasítja a közvetítő, hogy a fogadó ügyfelet szeretné-e explicit módon rendezi a fogadott üzenetek. Az üzenet vált elérhetővé a feldolgozásához, miközben kizárólagos zárolást a szolgáltatás alatt tartani, hogy más, egymással versengő fogadók nem jelenik meg a fogadóhoz. A Zárolás időtartama kezdetben van definiálva a várólista vagy az előfizetés szintjén és az ügyfél, a zárolás tulajdonos keresztül bővíthető a [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) műveletet.

Egy üzenet a zárolt ugyanabból az üzenetsorból vagy előfizetés fogadása más ügyfelek végrehajtása a zárolásokat, és nem aktív zárolás a következő elérhető üzeneteket beolvasni. Ha explicit módon felszabadul lévő üzenet zárolását, vagy ha a zárolás lejárta, a hibaüzenetet kap, miszerint készítsen biztonsági másolatot, vagy a lekérés sorrendjét redelivery elejéhez.

Ha az üzenet ismételten jelent a hozzáadásuk vagy a teljen el a meghatározott számú alkalommal a zárolást a használatukkal ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), az üzenet automatikusan eltávolítva az üzenetsorból vagy előfizetési és helyez el a társított kézbesítetlen levelek várólistájára vonatkozik.

A fogadó ügyfél kezdeményezi ügyintézésére pozitív visszajelzés érkezett üzenetet, ha meghívja [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) az API szintjén. Ez azt jelzi, hogy a közvetítőn, hogy az üzenet feldolgozása sikeresen megtörtént, és az üzenet törlődik az üzenetsorból vagy előfizetést. A közvetítő egy választ, amely azt jelzi, hogy elvégezhető az elszámolás válaszol a fogadó elszámolás szándékot.

Ha a fogadó ügyfél nem tudja feldolgozni egy üzenetet, de az üzenet újbóli kézbesítése szeretne, explicit módon kérhet az üzenetre, amely a, és feloldotta azonnal meghívásával [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) vagy azt nem csinál semmit teljen el a zárolást, és.

Ha egy fogadó ügyfél nem tudja feldolgozni egy üzenetet, és tudja, hogy az üzenet redelivering és megismételni a műveletet nem segít, azt az üzenetet, amely áthelyezése a kézbesíthetetlen levelek várólistába meghívásával elutasíthatja [kézbesítetlen](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), amely is lehetővé teszi, hogy egy egyéni tulajdonság, többek között az üzenetet a kézbesíthetetlen levelek üzenetsorból beolvasható Okkód beállítása.

Egy különleges esetben elszámolás késleltetési, amelyet külön cikk foglalkozik.

A **Complete** vagy **kézbesítetlen** műveletek, valamint a **RenewLock** műveletek hálózati hibák miatt sikertelen lehet, ha a tárolt zárolás lejárt, vagy más Szolgáltatásoldali feltételek, amelyek meggátolják a rendezése. Az utóbbi esetek egyikében küld a negatív visszajelzés a szolgáltatás a Surface-eszközök az API-ügyfelekben kivételként. Ha azért nem működő hálózati kapcsolat, a zárolás, mivel a Service Bus nem támogatja a meglévő AMQP-kapcsolatok helyreállítása egy másik kapcsolat eseményértesítése eldobva.

Ha **Complete** meghibásodik, ez akkor fordul elő általában nagyon végén üzenetkezelés és bizonyos esetekben a feldolgozási munka, perc elteltével eldöntheti, hogy a fogadó alkalmazásnak e megőrzi az állapotát, és figyelmen kívül hagyja az azonos üzenet másodszor vízjelezhetők, vagy ki a munkahelyi eredmény tosses e, és újrapróbálkozik, az üzenet újbóli kézbesítése.

A tipikus mechanizmus kézbesítések duplikált üzenetek azonosítására szolgáló van ehhez az üzenetazonosító, amely és a feladó egy egyedi értékére, valószínűleg az eredeti folyamat az adott azonosítót összhangban kell beállítani. Feladatütemezőt valószínűleg értékre kell állítania az üzenetazonosító próbál hozzárendelése a megadott feldolgozó alkalmazott a feladat azonosítóját, és a feldolgozó lenne ha figyelmen kívül a második előfordulása a feladat-hozzárendelés, hogy a feladat már megtörtént.

## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
