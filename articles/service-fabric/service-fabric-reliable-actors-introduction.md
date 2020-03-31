---
title: Szolgáltatásfabric megbízható szereplők áttekintése
description: Bevezetés a Service Fabric megbízható szereplők programozási modell, a virtuális aktor minta alapján.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 6aafa2a3372c431f8afa7fad41051c26c3fe5fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645565"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Bevezetés a Service Fabric megbízható szereplők
Megbízható szereplők egy Service Fabric alkalmazás keretrendszer a [virtuális aktor](https://research.microsoft.com/en-us/projects/orleans/) minta alapján. A Reliable Actors API egy egyszálas programozási modellt biztosít, amely a Service Fabric által biztosított méretezhetőségi és megbízhatósági garanciákra épül.

## <a name="what-are-actors"></a>Mik azok a színészek?
Az aktor egy elszigetelt, független számítási egység és állapot egyszálas végrehajtással. Az [aktorminta](https://en.wikipedia.org/wiki/Actor_model) egy számítási modell egyidejű vagy elosztott rendszerek, amelyben nagyszámú ilyen szereplők végre egyszerre és egymástól függetlenül. A színészek kommunikálhatnak egymással, és több szereplőt hozhatnak létre.

### <a name="when-to-use-reliable-actors"></a>Mikor kell használni a Megbízható szereplőket?
A Service Fabric megbízható szereplői az aktor tervezési minta megvalósítása. Mint minden szoftver tervezési minta, a döntés, hogy egy adott minta alapján történik-e vagy sem egy szoftver tervezési probléma illeszkedik a mintát.

Bár az aktor tervezési minta lehet egy jó illeszkedést számos elosztott rendszerek problémák és forgatókönyvek, gondos anamnézis a korlátok a minta és a keret végrehajtása kell tenni. Általános útmutatásként vegye figyelembe az aktormintát a probléma vagy forgatókönyv modellezéséhez, ha:

* A problémás terület nagy számú (több ezer vagy több) kis, független és elszigetelt állapot- és logikai egységet foglal magában.
* Olyan egyszálas objektumokkal szeretne dolgozni, amelyek nem igényelnek jelentős interakciót a külső összetevőktől, beleértve az állapot lekérdezését az egy adott szereplőkészletben.
* Az aktorpéldányok nem blokkolja a hívókat előre nem látható késésekkel I/O-műveletek kibocsátásával.

## <a name="actors-in-service-fabric"></a>A service fabric szereplői
A Service Fabric, a szereplők a Reliable Actors keretrendszerben: Egy aktor-minta-alapú alkalmazás keretrendszer a [Service Fabric megbízható szolgáltatások.](service-fabric-reliable-services-introduction.md) Minden megbízható aktor szolgáltatás ír egy particionált, állapotalapú megbízható szolgáltatás.

Minden aktor egy aktortípus példányaként van definiálva, amely megegyezik azzal, ahogyan a .NET objektum egy .NET típusú példány. Előfordulhat például, hogy van egy aktortípus, amely megvalósítja a számológép funkcióit, és előfordulhat, hogy sok ilyen típusú szereplő van elosztva a fürt különböző csomópontjain. Minden ilyen szereplőegyedileg azonosítja egy aktor azonosító.

## <a name="actor-lifetime"></a>Színész élettartama
A Service Fabric-szereplők virtuálisak, ami azt jelenti, hogy élettartamuk nem kötődik a memórián belüli ábrázolásukhoz. Ennek eredményeképpen ezeket nem kell kifejezetten létrehozni vagy megsemmisíteni. A Reliable Actors futásidejű automatikusan aktiválja az aktor az első alkalommal, amikor az aktor-azonosítóra vonatkozó kérést kap. Ha egy aktor nem használja egy ideig, a Reliable Actors futásidejű szemétgyűjti a memóriában lévő objektumot. Azt is fenntartja ismerete a színész létezését kell újra ítani később. További részletek: [Actor lifecycle and garbage collection](service-fabric-reliable-actors-lifecycle.md).

Ez a virtuális szereplő élettartama absztrakció hordoz néhány kikötések eredményeként a virtuális aktor modell, és valójában a Reliable Actors megvalósítása eltér időnként ettől a modelltől.

* Az aktor automatikusan aktiválódik (ami egy aktorobjektum felépítését okozza) az üzenet első elküldése az aktorazonosítójára. Egy idő után a szereplő objektum szemetet gyűjtött. A jövőben az aktorazonosító újra használva egy új aktorobjektumot hoz létre. Egy szereplő állapota túléli az objektum élettartamát, amikor az állapotkezelőtárolja.
* Bármely aktor metódus hívása egy aktorazonosítóhoz aktiválja az aktort. Emiatt az aktortípusok a konstruktor t implicit módon a futásidejű. Ezért az ügyfélkód nem tudja átadni a paramétereket az aktortípus konstruktorának, bár a paramétereket maga a szolgáltatás is átadhatja az aktor konstruktorának. Az eredmény az, hogy az operátorok lehet létrehozni egy részlegesen inicializált állapotban, mire más metódusok hívják meg, ha az aktor szükséges inicializálási paramétereket az ügyféltől. Nincs egyetlen belépési pont az ügyfélaktól származó aktor aktiválásához.
* Bár a Reliable Actors implicit módon aktorobjektumokat hoz létre; ön képes explicit módon törölni egy aktor és annak állapotát.

## <a name="distribution-and-failover"></a>Terjesztés és feladatátvétel
A méretezhetőség és a megbízhatóság biztosítása érdekében a Service Fabric elosztja a fürtök szereplőit, és szükség szerint automatikusan áttelepíti őket a sikertelen csomópontokról a kifogástalan állapotúakra. Ez egy absztrakció egy [particionált, állapotalapú megbízható szolgáltatás felett.](service-fabric-concepts-partitioning.md) A terjesztési, méretezhetőségi, megbízhatósági és automatikus feladatátvétel mind azon tény alapján biztosítják, hogy a szereplők az *aktorszolgáltatás*nevű állapotalapú megbízható szolgáltatáson belül futnak.

A szereplők az aktorszolgáltatás partíciói között vannak elosztva, és ezek a partíciók egy Service Fabric-fürt csomópontjai között vannak elosztva. Minden szolgáltatáspartíció tartalmaz egy szereplőkészletet. A Service Fabric kezeli a szolgáltatáspartíciók disztribúcióját és feladatátvételét.

Például egy aktor szolgáltatás kilenc partíciók üzembe helyezett három csomópont az alapértelmezett aktor partíció elhelyezése lenne elosztva így:

![Megbízható szereplők elosztása][2]

Az Aktor keretrendszer kezeli a partíciósémát és a kulcstartomány beállításait. Ez leegyszerűsíti néhány választás, hanem hordoz némi figyelmet:

* Megbízható szolgáltatások lehetővé teszi, hogy válasszon egy particionálási séma, kulcstartomány (tartomány particionálási séma használata esetén) és a partíciók száma. A Reliable Actors a tartományparticionálási sémára (az egységes Int64 sémára) korlátozódik, és a teljes Int64 kulcstartomány t igényel.
* Alapértelmezés szerint a szereplők véletlenszerűen kerülnek partíciókba, ami egységes elosztást eredményez.
* Mivel a szereplők véletlenszerűen vannak elhelyezve, várható, hogy az aktorműveletek mindig hálózati kommunikációt igényelnek, beleértve a metódushívási adatok szerializálását és deszerializálását, késést és terhelést.
* Speciális forgatókönyvekben az aktor partíció elhelyezése az adott partíciókhoz leképező Int64 aktorazonosítók használatával szabályozható. Ez azonban a szereplők kiegyensúlyozatlan eloszlását eredményezheti a partíciók között.

Az aktorszolgáltatások particionálásáról a szereplők particionálási fogalmai című további információkért tekintse meg a [műveletek particionálási fogalmait.](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)

## <a name="actor-communication"></a>Színész kommunikáció
Az aktor interakciók egy olyan felületen vannak definiálva, amelyet a felületet megvalósító aktor és az ugyanazon a felületen keresztül egy aktorhoz proxyt kap. Mivel ez a felület aszinkron módon aktormetódusok meghívására szolgál, a kapcsolat minden metódusának Feladat-visszatérőnek kell lennie.

A metódus-meghívások és azok válaszai végső soron hálózati kérelmeket eredményeznek a fürtön keresztül, így a visszaadott feladatok argumentumainak és eredménytípusainak a platform által szerializálhatónak kell lenniük. Különösen az [adatszerződésekszerializálhatónak](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)kell lenniük.

### <a name="the-actor-proxy"></a>Az aktor proxyja
A Reliable Actors ügyfél API-t egy aktor példány és egy aktor ügyfél közötti kommunikációt biztosít. Aszereplővel való kommunikációhoz az ügyfél létrehoz egy aktor proxyobjektumot, amely megvalósítja az aktor felületét. Az ügyfél a proxyobjektummetódusok meghívásával lép kapcsolatba az aktorral. Az aktor proxy ügyfél-színész és a színész-színész kommunikáció használható.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


Vegye figyelembe, hogy az aktor proxyobjektum létrehozásához használt két információ az aktorazonosítója és az alkalmazás neve. Az aktor azonosítója egyedileg azonosítja az aktor, míg az alkalmazás neve azonosítja a [Service Fabric-alkalmazást,](service-fabric-reliable-actors-platform.md#application-model) ahol az aktor telepítve van.

A `ActorProxy`(C#) `ActorProxyBase`/ (Java) osztály az ügyféloldalon elvégzi a szükséges felbontást, hogy keresse meg az aktor azonosító val, és nyisson meg vele egy kommunikációs csatornát. Azt is újrapróbálkozik, hogy keresse meg az aktor kommunikációs hibák és feladatátvételek esetén. Ennek eredményeképpen az üzenetek kézbesítése a következő jellemzőkkel rendelkezik:

* Az üzenetek kézbesítése a legjobb erőfeszítés.
* Az szereplők ismétlődő üzeneteket kaphatnak ugyanattól az ügyféltől.

## <a name="concurrency"></a>Egyidejűség
A Reliable Actors futásidejű egy egyszerű, többalapú hozzáférési modell az aktor metódusok eléréséhez. Ez azt jelenti, hogy egy aktorobjektum kódján belül egyszerre legfeljebb egy szál lehet aktív. A turn-based hozzáférés nagymértékben leegyszerűsíti az egyidejű rendszereket, mivel nincs szükség szinkronizálási mechanizmusokra az adatok hozadékához. Ez azt is jelenti, hogy a rendszereket az egyes aktorpéldányok egyszálas hozzáférési jellegére vonatkozó különleges szempontok figyelembevételével kell megtervezni.

* Egyetlen aktorpéldány egyszerre csak egy kérelmet dolgozhat fel. Az aktorpéldány átviteli szűk keresztmetszetet okozhat, ha várhatóan egyidejű kérelmeket kell kezelnie.
* A szereplők holtpontra juttathatják egymást, ha két szereplő között körkörös kérelem érkezik, miközben az egyik szereplőnek egyidejűleg külső kérés érkezik. Az aktor futásidejű automatikusan időbeli ki az aktor hívások, és egy kivételt a hívó fél megszakítása esetleges holtponti helyzetekben.

![Megbízható szereplők kommunikáció][3]

### <a name="turn-based-access"></a>Átaszta-alapú hozzáférés
A turn egy aktor metódus teljes végrehajtásából áll, más szereplők vagy ügyfelek kérésére adott válaszként, vagy egy [időzítő/emlékeztető](service-fabric-reliable-actors-timers-reminders.md) visszahívás teljes végrehajtásából. Annak ellenére, hogy ezek a módszerek és visszahívások aszinkron, az Actors futásidejű nem interleave őket. Egy kanyarnak teljesen be kell fejeződnie, mielőtt új fordulatot engedélyezne. Más szóval egy aktor metódus vagy időzítő/emlékeztető visszahívás, amely jelenleg futtatva kell teljesen befejeződött, mielőtt egy új hívás egy metódus vagy visszahívás engedélyezett. Egy metódus vagy visszahívás akkor tekinthető befejezettnek, ha a végrehajtás a metódusból vagy visszahívásból érkezett, és a metódus vagy visszahívás által visszaadott feladat befejeződött. Érdemes hangsúlyozni, hogy a turn-based egyidejűség tiszteletben tartják még a különböző módszerek, időzítők, és visszahívások.

Az Actors futásidejű kényszeríti a turn-alapú egyidejűség megszerzésével egy aktorzár elején egy fordulatot, és felszabadítja a zárat a forduló végén. Így a turn-based egyidejűség érvényesítése egy szereplőnként, és nem a szereplők között. Aktor metódusok és időzítő/emlékeztető visszahívások egyszerre hajthatók végre a különböző szereplők nevében.

A következő példa a fenti fogalmakat mutatja be. Vegyünk egy aktortípust, amely két aszinkron metódust (például *Method1* és *Method2),* egy időzítőt és egy emlékeztetőt valósít meg. Az alábbi ábra egy példát mutat be egy ütemtervet a végrehajtás ezen módszerek és visszahívások nevében két szereplő (*ActorId1* és *ActorId2 ),* amelyek ehhez az aktortípushoz tartoznak.

![Megbízható szereplők futásidejű, többre épülő egyidejűség és hozzáférés][1]

Ez az ábra az alábbi konvenciókat követi:

* Minden függőleges vonal egy metódus vagy visszahívás végrehajtásának logikai áramlását mutatja egy adott szereplő nevében.
* Az egyes függőleges sorokon jelzett események időrendi sorrendben fordulnak elő, az újabb a régebbiek alatt következnek be.
* Különböző színeket használnak a különböző szereplőknek megfelelő idővonalakhoz.
* A kiemelés azt jelzi, hogy a metódus vagy visszahívás nevében milyen időtartamalatt tartják az egyktorzárat.

Néhány fontos szempont, hogy fontolja meg:

* Míg *a Method1* az *ActorId2* nevében hajt végre *az xyz789*ügyfélkérésére válaszolva, egy másik ügyfélkérés (*abc123*) érkezik, amely szintén megköveteli *a Method1-et* *az ActorId2*által végrehajtandó . A *Method1* második végrehajtása azonban csak az előző végrehajtás befejezése után kezdődik meg. Hasonlóképpen, egy emlékeztető által regisztrált *ActorId2* tüzek míg *Method1* végrehajtása során válaszul az ügyfél kérésére *xyz789*. Az emlékeztető visszahívása csak a *Method1* mindkét végrehajtása után kerül végrehajtásra. Mindez annak köszönhető, hogy a turn-based egyidejűség, hogy végre *a ActorId2*.
* Hasonlóképpen, turn-based egyidejűség is kikényszeríti *a ActorId1*, amint azt a végrehajtás *Method1*, *Method2*, és az időzítő visszahívás nevében *ActorId1* történik egy soros módon.
* *Az* *ActorId1* nevében történő *1.* Ennek az az oka, hogy a többre épülő egyidejűség csak egy szereplőn belül van kényszerítve, és nem a szereplők között.
* Néhány metódus/visszahívási végrehajtások, a `Task`(C#) `CompletableFuture`/ (Java) által visszaadott metódus/visszahívás befejeződik, miután a metódus visszatér. Másokban az aszinkron művelet már befejeződött, mire a metódus/visszahívás visszatér. Mindkét esetben a per-aktorzár csak akkor szabadul fel, ha mind a metódus/visszahívás visszatér, mind az aszinkron művelet befejeződik.

### <a name="reentrancy"></a>Újbóli belépés
Az Actors futásidejű lehetővé teszi a reentrancy alapértelmezés szerint. Ez azt jelenti, hogy ha az *Aktor aktor aktora* metódusa meghívja a *"B" aktor*egy metódusát, ami viszont egy másik módszert hív meg az *A aktoron,* akkor a metódus futtathat. Ennek az az oka, hogy ugyanannak a logikai híváslánc-környezetnek a része. Minden időzítő- és emlékeztetőhívás az új logikai hívási környezettel kezdődik. További részletekért tekintse meg a [Megbízható szereplők reentrancy](service-fabric-reliable-actors-reentrancy.md) című témakörét.

### <a name="scope-of-concurrency-guarantees"></a>Az egyidejűségi garanciák hatálya
Az Actors futásidejű biztosítja ezeket az egyidejűségi garanciákat olyan helyzetekben, amikor szabályozza ezeknek a módszereknek a meghívását. Ezeket a garanciákat például az ügyfélkérésre adott válaszként végzett metódus-meghívásokra, valamint az időzítő- és emlékeztetővisszahívásokra vonatkozóan biztosítja. Ha azonban az aktorkód közvetlenül meghívja ezeket a metódusokat az Actors futásidejű által biztosított mechanizmusokon kívül, majd a futásidejű nem nyújthat egyidejűségi garanciákat. Ha például a metódust olyan feladat környezetében hívja meg, amely nincs társítva az aktor metódusok által visszaadott feladathoz, akkor a futásidejű nem tud egyidejűségi garanciákat nyújtani. Ha a metódus meghívása egy szál, amely az aktor saját maga hoz létre, majd a futásidejű is nem nyújthat egyidejűségi garanciákat. Ezért a háttér-műveletek végrehajtásához a szereplőknek [aktoridőzítőket és aktor-emlékeztetőket kell használniuk,](service-fabric-reliable-actors-timers-reminders.md) amelyek tiszteletben tartják a többalapú egyidejűséget.

## <a name="next-steps"></a>További lépések
Első lépések az első Reliable Actors szolgáltatás létrehozásával:
   * [A Reliable Actors with .NET – első lépések](service-fabric-reliable-actors-get-started.md)
   * [A Megbízható szereplők a Java-n való ismerkedése](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
