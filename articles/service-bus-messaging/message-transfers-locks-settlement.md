---
title: "Az Azure Service Bus üzenet átvitelek zárolások és elszámolási |} Microsoft Docs"
description: "A Service Bus message átvitelek és elszámolási műveletek áttekintése"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: 4789da3c84d52b2615bf4250a36093a74154e1d4
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="message-transfers-locks-and-settlement"></a>Üzenet-átvitel, zárolások és elszámolási

Egy üzenet broker, például a Service Bus központi képességét, hogy fogadja az üzeneteket az üzenetsor vagy témakör és tartsa éri el újabb. *Küldési* a kifejezés, és az átvitel az üzenet broker az üzenet gyakran használják. *Fogadási* a lekérése során ügyfél üzenet átvitelét általánosan használt fogalom.

Amikor egy ügyfél üzenetet küld, akkor általában szeretné megnézni, hogy az üzenet már megfelelően át és fogadja el az átvitelszervező, vagy valamilyen hiba lépett fel. A pozitív vagy negatív tudomásul vétele rendezi az ügyfél és a broker ismertetése az üzenet az átvitel állapotával kapcsolatos információkat, és így nevezzük *elszámolási*.

Hasonlóképpen, amikor a broker üzenet egy ügyfelet, az ügyfél és a kíván létesíteni megismerhesse, hogy az üzenet feldolgozása sikeresen megtörtént, és ezért távolítható el, vagy e üzenetkézbesítést vagy feldolgozása sikertelen volt, és így a üzenet esetleg újra kell kézbesíteni.

## <a name="settling-send-operations"></a>Műveletek rendezése

A Service Bus API támogatott ügyfelek használva küldése műveletek a Service Bus mindig explicit módon kiegyenlített, ami azt jelenti, hogy az API-művelet megvárja-e egy elfogadási eredmény a Service Bus kimarad, majd befejezi a küldési művelet.

A Service Bus által visszautasítja az üzenetet, ha az elutasítás tartalmaz, hibát jelző, a "nyomon követési-id" saját szöveget. Az elutasítás arról, hogy a művelet követően újra megkísérelhető a sikeres a bármely elvárása az adatokat is tartalmaz. Az ügyfél ezt az információt kivétel be van kapcsolva, és a hívónak a küldési művelet következik be. Ha az üzenet elfogadását, a művelet csendes befejeződik.

Az AMQP protokoll, amely a .NET-szabvány ügyfél és a Java-ügyfél kizárólagos protokoll használata esetén és [a .NET-keretrendszer ügyfél lehetősége, amely](service-bus-amqp-dotnet.md), üzenet átvitelek és rendezések futószalagos és teljesen aszinkron, ezért ajánlott, hogy használja-e az aszinkron programozási modell API Variant adattípusban.

Egy küldő helyezhetik több üzenet a keresztülhaladnak a hálózaton gyors egymásutánban Várjon, amíg minden üzenetet vonatkozik, ahogyan az a volna ellenkező esetben a SBMP protokollt vagy a HTTP 1.1 nélkül. Aszinkron küldés műveletek el, mivel a megfelelő üzenetek elfogadja és tárolja, particionált entitások, vagy amikor művelet küldeni a különféle entitásokat átfedi egymást. A befejezésekhez is az eredeti küldési sorrendje nem fordulhatnak elő.

A kezelési műveletek eredményeit stratégiája az alkalmazás azonnali és jelentős hatása lehet. Ebben a szakaszban szereplő példák C# nyelven íródtak, és Java határidő év vonatkoznak.

Ha az alkalmazás létrehozza az üzenetek felszakadásáig, egyszerű hurkot az itt bemutatott és await létrehozása után nem voltak az egyes küldési művelet a következő, a szinkron üzenet küldése előtt, vagy egyaránt a aszinkron API alakzatok, csak 10 üzenetküldésre befejeződése után 10 egymást követő teljes adatváltások rendezésre.

Az egy feltételezett 70 ezredmásodperces TCP körbejárási késés távolság egy helyszíni hely a Service Bus-és jogosultságot ad a Service Bus csak 10 ms fogadja el, és minden üzenetet tárolására a következő hurok foglal legalább 8 másodperc, nem számítva a tartalom átvitelének az idejét vagy a potenciális útvonal torlódás hatások:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Ha az alkalmazást elindítja a 10 aszinkron műveletek közvetlen egymás után, és a megfelelő befejezési külön várja, az oda-vissza idő 10 küldési műveletek átfedésben van. A 10 üzenetek átkerülnek a közvetlen egymás után, esetleg még megosztása TCP keretek, és a teljes átviteli időtartama nagymértékben függ a hálózattal kapcsolatos szükséges időt a továbbított üzenet eléréséhez a broker.

Így az azonos feltételezéseket, mint a korábbi hurok, a teljes átfedett végrehajtási ideje a következő hurok maradhatnak, egy másodperc alatt is:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks.ToArray());
```

Fontos megjegyezni, hogy minden aszinkron programozási modellek használja-e valamilyen memóriaalapú, rejtett várólista, amely tárolja a függőben lévő műveletek. Ha [sendasync metódusok párhuzamosan](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) vagy **küldése** return (Java) a küldési feladat, hogy a munkahelyi várólistában sorba, de a protokoll hitelesítési mód csak megkezdése után a feladatütemezés következik futtatásához. A leküldéses üzenetek és a megbízhatóság esetén veszélye felszakadásáig általában kódját ügyelni kell, hogy túl sok üzenetek kerülnek "útban" egyszerre, mert minden küldött üzenetek akár memória igénybe vehet, amíg azokat ténylegesen az átvitel közbeni helyezik üzembe le.

Szemaforok, ahogyan az a következő kódrészletet a C#,-e szinkronizálási objektumok, amelyek lehetővé teszik, ilyen alkalmazásszintű sávszélesség-szabályozás szükség esetén. A szemafor használatával kell útban egyszerre legfeljebb 10 üzenetek. A 10 elérhető szemafor zárolásokat egyik lesz végrehajtva, a küldés előtt, és megjelent, a Küldés befejeződik. A hurok megvárja, amíg legalább egy előzetes küld 11 továbbítása befejeződött, és ezután elérhetővé teszi a zárolási:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks.ToArray());
```

Alkalmazások kell **soha nem** egy aszinkron küldés művelet "érvényesítést és elfelejti" módon indítása nélkül a művelet eredményének beolvasása. Ezzel betölteni a belső és láthatatlan feladat várólistára legfeljebb elfogyott a memória, és hogy az alkalmazás küldési hibáinak észleléséhez:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Egy alacsony szintű AMQP-ügyféllel a Service Bus "előre kiegyenlített" átvitel is fogad. A egy előre kiegyenlített átvitele egy tűz-és-elfelejti művelet, amelynek az eredménye, mindkét módszer esetén jelentése nem vissza az ügyfél és az üzenet elküldésekor rendezni számít. Az ügyfél visszajelzés hiánya is azt jelenti, hogy végrehajthatóként adatot nem érhető el diagnosztika, azaz, hogy ez a mód nem felel meg az Azure támogatási keresztül segítségét.

## <a name="settling-receive-operations"></a>Rendezése fogadási műveletek

A fogadási műveletek, a Service Bus API ügyfelek kétféle explicit módon engedélyezi: *fogadása és törlése* és *betekintés-Lock*.

A [fogadása és törlése](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód közli az összes üzenetet küld a fogadó ügyfél kiegyenlített, mikor érdemes broker küldött. Ez azt jelenti, hogy az üzenet, amint az átvitelszervező állította a hálózaton, felhasznált számít. Ha az üzenetátvitel sikertelen, az üzenet elveszik.

Az ebben a módban a feje, hogy a fogadó nem kell további műveletek végrehajtása az üzenetet, és nem is lassítsa várakozással rendezésére eredményeit. Ha az egyes üzeneteket lévő adatok alacsony érték és/vagy is csak nagyon rövid időn végrehajthatók, ebben a módban ésszerű választás.

A [betekintés-Lock](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód közli az átvitelszervező, hogy a fogadó ügyfél fogadott üzenetek explicit módon rendezni szeretné. Az üzenet feldolgozásához, miközben szolgáltatási kizárólagos zárolást alatt tartani, hogy a versengő, más fogadó nem látható a fogadó számára elérhetők. A Zárolás időtartama kezdetben definiálása a várólista vagy az előfizetési szinten, és az ügyfél, a zárolás tulajdonos keresztül bővíthető a [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) műveletet.

Egy üzenet le van tiltva, ha más ügyfelek ugyanabból az üzenetsorból vagy előfizetés fogadása hoz zárolások és nem aktív zárolást a következő rendelkezésre álló üzeneteket beolvasni. Amikor az üzenetet a rendszer explicit módon zárolás vagy a zárolás lejár, az üzenet biztonsági mentése ugrik, kevés vagy a lekérdezési ahhoz, hogy redelivery elején.

Ha az üzenet ismételten fogadók által kiadott vagy azok lehetővé teszik, hogy a zárolás, a meghatározott számú alkalommal eltelnie ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), az üzenet automatikusan eltávolítja az üzenetsorból vagy az előfizetés és a társított csoportosítson kézbesítetlen levelek várólistájára.

A fogadó ügyfél pozitív visszajelzés fogadott üzenet rendezésére kezdeményez, ha meghívja [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) API szintjén. Ez jelzi a broker, hogy az üzenet feldolgozása sikeresen megtörtént, és az üzenet törlődik az üzenetsorból vagy előfizetés. A broker válaszokat a fogadó elszámolási célt, amely jelzi, hogy elvégezhető-e az elszámolási válasz.

A fogadó ügyfél nem tudja feldolgozni egy üzenetet, de az üzenet újból kézbesítve azt szeretné, akkor explicit módon kérje meg az üzenet, amely és meghívásával azonnal zárolása feloldva [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) vagy semmi sem lehet hasznos, és lehetővé teszik a eltelnie zárolást.

Ha fogadó ügyfél nem tudja feldolgozni egy üzenetet, és ismeri az üzenet redelivering, majd próbálja megismételni a műveletet nem nyújt azt is el az üzenetet, amely áthelyezi a kézbesítetlen levelek várólistájára kerülő meghívásával [kézbesítetlen levelek](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), mely is lehetővé teszi, hogy egy egyéni tulajdonság, többek között a OK, amely lehet beolvasni. a kézbesítetlen levelek várólistájára az üzenet beállítása.

Rendezés egy különleges esetben halasztás, amely egy külön cikk tárgyalja.

A **Complete** vagy **kézbesítetlen levelek** műveleteket, valamint a **RenewLock** műveletek hálózati problémák miatt sikertelenek lehetnek, ha a tárolt zárolása lejárt, vagy más kiszolgálóoldali feltételek, amelyek meggátolják a rendezése. Az utóbbi esetek egyikében elküldi a negatív visszajelzés a szolgáltatás adott felületek az API-ügyfelekben kivételként. A hiba oka a hibás hálózati kapcsolat létrehozásakor, a zárolás megszakad a Service Bus nem támogatja a meglévő AMQP hivatkozások helyreállítása egy másik kapcsolat óta.

Ha **Complete** sikertelen lesz, amely általában nagyon végén üzenetek kezelésének és egyes esetekben feldolgozási munka perc után a fogadó alkalmazás eldöntheti, hogy megőrzi a feladat állapotát, és figyelmen kívül hagyja az azonos még egyszer biztosítását, vagy jelenik meg a munkahelyi eredmény tosses e, és újrapróbálkozik, mint az üzenet újból kézbesítve.

A tipikus mechanizmus alapján azonosítja a duplikált üzenetek kézbesítések van, úgy, az üzenet-azonosítóját és a feladó egyedi értékre, valószínűleg összhangban a származó folyamatának azonosítót kell megadni. A Feladatütemező valószínűleg értékre kell állítania az üzenetazonosító kíván hozzárendelni a megadott munkavégző alkalmazott a feladat azonosítóját, és a dolgozó volna Ha figyelmen kívül a második előfordulása esetén a feladat-hozzárendelés, hogy a feladat már megtörtént.

## <a name="next-steps"></a>További lépések

Tudhat meg többet a Service Bus üzenetkezelés, a következő témakörökben:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
