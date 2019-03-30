---
title: Service Fabric Reliable Actors – áttekintés |} A Microsoft Docs
description: A Service Fabric Reliable Actors programozási modell bemutatása.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 7fdad07f-f2d6-4c74-804d-e0d56131f060
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 5a237e23dffed76e6122e17b59c85d20ca7e1baf
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668670"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>A Service Fabric Reliable Actors bemutatása
A Reliable Actors a Service Fabric alkalmazási keretrendszer alapján a [virtuális Aktor](https://research.microsoft.com/en-us/projects/orleans/) mintát. A Reliable Actors API épülő a méretezhetőséget és megbízhatóságot garantálja a Service Fabric által biztosított programozási egyszálas modellt biztosít.

## <a name="what-are-actors"></a>Mik azok az Actors?
Az aktor a számítási műveletek és az egyszálas végrehajtási állapot egy elkülönített, független egység. A [szereplő minta](https://en.wikipedia.org/wiki/Actor_model) számítási modell párhuzamos és elosztott rendszerek, amelyre ezek az aktorok nagy számú hajthat végre egy időben, és egymástól. Aktorok kommunikálhatnak egymással, és a további aktorok hozhatnak létre.

### <a name="when-to-use-reliable-actors"></a>Mikor érdemes használni a Reliable Actors
Service Fabric Reliable Actors az aktor tervezési minta egy megvalósítását. Csakúgy, mint bármely szoftver a kialakítási mintában a döntést, hogy egy adott minta használja-e történik-e a szoftverek tervezése a probléma alapján megfelel a mintának.

Bár az aktor tervezési minta lehet egy jó illeszkednek az elosztott rendszerekkel kapcsolatos és forgatókönyvek, gondosan kell tenni a mintázat és a megvalósítás keretrendszer korlátait figyelembe veszi. Útmutatásra van szüksége fontolja meg az aktor minta modellezésére a probléma vagy a forgatókönyvben, ha:

* A probléma tárhely magában foglalja a nagy számú (több ezer vagy több) kisebb, független és elkülönített állapotot és a logikai egységek.
* Külső összetevők, beleértve az állapot lekérdezése actors több jelentős felhasználói beavatkozás nem szükséges egyszálas objektumokat szeretné.
* Az aktorpéldányokat i/o-műveletek kiállításával nem blokkolja a hívó az késleltetések előre nem látható.

## <a name="actors-in-service-fabric"></a>A Service Fabric actors
A Service Fabric actors megvalósítva, a Reliable Actors keretrendszerben. Actor-minta-alapú alkalmazás-keretrendszer, a beépített [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md). Minden egyes ír Reliable Actors-szolgáltatás ténylegesen egy particionált, állapotalapú Reliable Services.

Minden egyes színész számít, ha az aktor típusát, a .NET-objektumokat kell egy .NET-típus egy példányát módja megegyezik egy példányát. Például előfordulhat, hogy az aktor típusát, amely megvalósítja a Számológépet funkcióit, és lehetséges, hogy sok actors az adott típusú, amely különböző csomópontokon vannak elosztva a fürtben. Minden ilyen színész egyedileg azonosít egy aktor.

## <a name="actor-lifetime"></a>Aktor élettartama
A Service Fabric actors olyan virtuális, ami azt jelenti, hogy azok élettartama a memórián belüli ábrázolás nem kötődik. Ennek eredményeképpen ezek nem kell explicit módon hozható létre vagy megsemmisül. A Reliable Actors-futtatókörnyezet automatikusan aktiválódik egy aktor az első alkalommal egy kérést kap, hogy szereplő azonosítóval. Ha egy ideig nem használ egy szereplő, akkor a Reliable Actors-futtatókörnyezet szemétgyűjtési-gyűjti a memórián belüli objektum. Is megőrzi az aktor meglétét ismerete kell kell később újra kell aktiválni. További részletekért lásd: [Aktor életciklus-kezelés és szemétgyűjtés gyűjtemény](service-fabric-reliable-actors-lifecycle.md).

A virtuális aktor élettartama absztrakciós hajtja bizonyos korlátozásokkal eredményeképpen a virtuális actor modell, és tulajdonképpen a Reliable Actors-megvalósítás esetenként eltér a modell.

* Egy aktor automatikusan aktiválódik (okozó állítható össze az aktor objektum) először egy üzenetet küld az aktor azonosító. Bizonyos idő elteltével az aktor objektum a szemétgyűjtő. A jövőben újra az aktor azonosító használatával a egy új aktor objektumot kell kialakítani. Az aktorok állapotának outlives az objektum élettartamát az az állapot manager található.
* Adott aktor szereplő azonosító bármelyik aktor metódus hívása aktiválódik. Ebből kifolyólag aktor típusok kell az implicit módon hívja meg a futtatókörnyezet konstruktor. Ezért Ügyfélkód nem adhatók át paraméterek az aktor típusát konstruktor, bár előfordulhat, hogy átadni az aktor konstruktor maga a szolgáltatás által. Az eredménye, hogy actors lehet úgy részlegesen inicializált állapotban a ideje más módszerek az úgynevezett rajta, ha az aktor inicializálási paramétereket az ügyfél igényel. Nincs az ügyféltől az aktor aktiválásának egyetlen belépési pont.
* Bár a Reliable Actors implicit módon létrehozhat aktor objektumok; lehetővé teszi egy szereplő és annak állapotát törölhetők külön rendelkezik.

## <a name="distribution-and-failover"></a>Terjesztési és feladatátvétele
Méretezhetőséget és megbízhatóságot biztosít, a Service Fabric actors terjesszen a fürtön osztja el, és automatikusan áttelepíti őket a meghibásodott csomópontok szükség szerint kifogástalan állapotú eszközök. Ez azért absztrakciós egy [particionált, állapotalapú Reliable Services](service-fabric-concepts-partitioning.md). Terjesztési, méretezhetőséget, megbízhatóságot és az Automatikus feladatátvétel az összes megadott actors belül futó a tényen egy állapotalapú Reliable Services nevű a *Aktorszolgáltatás*.

Aktorok vannak elosztva a partíciók az Aktorszolgáltatás, és ezek a partíciók egy Service Fabric-fürtben lévő csomópontok között oszlanak meg. Mindegyik szolgáltatás partíció actors készletét tartalmazza. A Service Fabric terjesztési, valamint a szolgáltatás partícióinak feladatátvételének kezeli.

Például az aktorszolgáltatás, használja az alapértelmezett aktor partíció elhelyezési három csomóponton üzembe helyezik kilenc partícióval rendelkező lenne terjeszthetők thusly:

![A Reliable Actors terjesztési][2]

Az Actors keretrendszerben kezeli a partíciós sémája és a kulcs tartomány-beállításokat. Ez egyszerűbbé teszi az egyes lehetőségek, de néhány szempont is végzi:

* A Reliable Services lehetővé teszi, hogy válassza ki a particionálási sémát, a tartományok (Ha a particionálási séma számos használatával), és a partíció száma. A Reliable Actors a particionálási séma (a egységes Int64 séma) tartomány korlátozódik, és a használatához az teljes Int64 kulcstartományhoz.
* Alapértelmezés szerint actors véletlenszerűen kerülnek eredményez az egyenletes elosztása a partíciókra.
* Aktorok véletlenszerűen kerülnek, mert számíthat, hogy szereplő műveletek mindig szükség van a hálózati kommunikáció, beleértve a szerializálást és deszerializálást metódus hívási adatonként, késés és a terhelés.
* Speciális eseteket is lehet vezérlő aktor partíció elhelyezési Int64 aktor azonosítók, amelyek adott partíciók használatával. Azonban ennek így eredményezhet egy okozta egyenetlen eloszlást szereplők partíciók között.

További információt a hogyan aktorszolgáltatások vannak particionálva, [fogalmak particionálási actors](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Aktor kommunikáció
Az aktor, amely megvalósítja a felületet, és az ügyfél, amely lekérdezi a proxy, egy közös felületen keresztül szereplő által közösen használt illesztőfelületet aktor interakciók vannak definiálva. Mivel ez az interfész aktor metódusokat hívhat meg aszinkron módon van használatban, a felület minden metódust feladat visszaadó kell lennie.

Megpróbálkozni és válaszaik végső soron eredményez hálózati kéréseket a fürtön, így az argumentumok, és a feladatot, amelyet vissza eredményt típusú szerializálható a platform által kell lennie. Különösen kell lennie [adatokat szerződéses szerializálható](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>Az aktor proxy
A Reliable Actors-ügyfél API-JÁNAK aktor példányát és a egy aktor ügyfél közötti kommunikációt tesz lehetővé. Egy aktor kommunikálni, ügyfél meg az aktor illesztőjét aktor proxy objektumot hoz létre. Az ügyfél kommunikál az aktor proxykiszolgáló objektu metódusok meghívásával. Az aktor proxy ügyfél – aktor és az aktor actor kommunikációhoz használható.

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


Vegye figyelembe, hogy az aktor proxykiszolgáló objektu létrehozásához használt kétféle információra aktor Azonosítóját és az alkalmazás nevét. Az aktor azonosító egyedileg azonosítja az aktor, azonosítja a felhasználót az alkalmazás nevét a [Service Fabric-alkalmazás](service-fabric-reliable-actors-platform.md#application-model) az aktor telepítési helyét.

A `ActorProxy`(C#) / `ActorProxyBase`(Java) osztály, az ügyfél oldalán keresse meg az aktor azonosító alapján, és nyissa meg a kommunikációs csatornát, a szükséges megoldás hajt végre. Újra megpróbálja megállapítani az aktor helyét a kommunikációs hibákkal szemben, és folyamatban lévő feladatátvételi teszteket a is. Az üzenetek kézbesítését, ezért a következő jellemzőkkel rendelkezik:

* Az üzenetek kézbesítését a lehető legjobb.
* Aktorok ismétlődő üzeneteket kaphat az ugyanazon ügyféltől érkező.

## <a name="concurrency"></a>Egyidejűség
A Reliable Actors-futtatókörnyezet egyszerű fordulókra hozzáférés modellt biztosít az aktor módszerek eléréséhez. Ez azt jelenti, hogy legfeljebb egy hozzászóláslánc lehet aktív egy szereplő objektum kód belül bármikor. Kapcsolja-alapú hozzáférés megkönnyíti az egyidejű rendszerek, nem szükséges a szinkronizálási mechanizmus az adatok eléréséhez. Azt is jelenti, minden egyes színész példány egyszálas hozzáférés jellegét vonatkozó különleges szempontok a rendszerek úgy kell megtervezni.

* Egy aktor egyetlen példány egyszerre nem tudja feldolgozni az egynél több kérést. Aktor példányát a teljesítmény szűk keresztmetszet okozhat, ha a várható egyidejű kérelmek kezeléséhez.
* Aktorok is kölcsönös kizárás egymással, ha két actors, míg egy külső kérelem érkezik a szereplők közül egyszerre között van. kör alakú kérelmet. Automatikusan időtúllépéssel fejeződött be a aktorhívások és kivételt a hívónak zavarni lehetséges holtpont helyzetekben az actor-futtatókörnyezetben.

![A Reliable Actors-kommunikáció][3]

### <a name="turn-based-access"></a>Kapcsolja-alapú hozzáférés
Egy kapcsolja-aktormetódus egy irányuló kérelemre adott válasz más actors vagy az ügyfelek teljes végrehajtását, vagy a teljes végrehajtását áll egy [időzítő/emlékeztető](service-fabric-reliable-actors-timers-reminders.md) visszahívás. Annak ellenére, hogy ezeket a metódusokat és visszahívások aszinkron, az Actors modul nem interleave őket. Egy teljesen kidolgozott kell, mielőtt új kapcsolja engedélyezett. Más szóval az aktor metódus vagy időzítő/emlékeztető visszahívást, amelyet jelenleg végrehajtás alatt álló teljesen kidolgozott új metódus hívása előtt kell lennie, vagy visszahívás engedélyezett. A metódus vagy visszahívás befejezése után, ha a metódus adott vissza a végrehajtási vagy visszahívás és a feladat a metódus vagy visszahívás által visszaadott befejeződött minősül. Érdemes tárgyalta a fordulókra egyidejűségi akár különböző módjait, időzítők és visszahívások tiszteletben tartását.

Az Aktorok modul fordulókra egyidejűségi meggyorsíthatja a aktoronkénti zárolásra egy kapcsolja be az elején, és a zárolás feloldása a kapcsolja végén érvénybe lépteti. Így fordulókra egyidejűségi aktoronkénti alapon és actors között nem kényszerítése. Aktor módszerek és időzítő/emlékeztető visszahívások egyidejűleg hajtsa végre különböző actors nevében.

Az alábbi példa a fenti fogalmakat mutatja be. Fontolja meg az aktor típusát, amely megvalósítja a két aszinkron metódusok (például *Method1* és *Method2*), időzítő, valamint egy emlékeztetőt. Az alábbi ábra egy ütemterv nevében két actors ezeket a metódusokat és visszahívások végrehajtására példát mutat be (*ActorId1* és *ActorId2*) tartozik, amely az aktor típusát.

![A Reliable Actors futásidejű fordulókra egyidejűség- és hozzáférés][1]

Ez a diagram ezeket a szabályokat követi:

* Minden egyes függőleges vonal nevében egy adott aktor metódust, illetve egy visszahívás végrehajtása logikai folyamata látható.
* Az események függőleges soronként megjelölve az újabb, a régieket alatt bekövetkező események időrendi sorrendben fordulhat elő.
* Különböző színek különböző actors megfelelő ütemtervek szolgálnak.
* Kiemelés szolgál az időtartam, amelynek az aktoronkénti zárolásra tárolt metódus vagy visszahívás nevében jelezze.

Néhány fontos tudnivalók:

* Miközben *Method1* nevében végrehajtása *ActorId2* ügyfél irányuló kérelemre adott válasz *xyz789*, egy másik ügyfél kérése (*abc123*) érkezik, amely is szükséges *Method1* hajtja végre *ActorId2*. Azonban a második végrehajtásának *Method1* nem kezdődik meg addig, amíg az előző végrehajtás befejeződött. Hasonlóképpen, egy emlékeztető által regisztrált *ActorId2* következik be, miközben *Method1* folyamatban van az ügyfél irányuló kérelemre adott válasz *xyz789*. Csak a mindkét a végrehajtás után hajtja végre az emlékeztető visszahívási *Method1* befejeződött. Mindezt van kényszerítve a fordulókra párhuzamosság miatt *ActorId2*.
* Hasonlóképpen, fordulókra egyidejűségi is kényszerítve van a *ActorId1*, amint azt a végrehajtásának *Method1*, *Method2*, és az időzítő visszahívási nevében *ActorId1* soros módon történik.
* Végrehajtásának *Method1* nevében *ActorId1* átfedésben van a nevében végrehajtása *ActorId2*. Ennek oka az, fordulókra egyidejűségi csak egy szereplő belül és között actors nem tartatja be.
* Néhány módszer/visszahívási végrehajtást a `Task`(C#) / `CompletableFuture`(Java) a módszer/visszahívási végeztével által visszaadott, miután a metódus adja vissza. A más az aszinkron művelet már befejeződött a ideje, a metódus vagy visszahívás adja vissza. Mindkét esetben az aktoronkénti zárolásra lesz kiadva, csak a mind a metódus vagy visszahívás adja vissza, és az aszinkron művelet befejeződése után.

### <a name="reentrancy"></a>Újbóli belépés
Az Aktorok modul – újbóli belépés alapértelmezés szerint lehetővé teszi. Ez azt jelenti, hogy ha egy aktor módot a *Aktor A* metódus meghívja a *Aktor B*, amely meghívja a másik módszer a *egy Aktor*, hogy engedélyezett-e módszer futtatásához. Ennek oka az, része a logikai hívásláncot ugyanabban a környezetben. Az új logikai hívás környezet összes időzítő és emlékeztető hívások indítsa el. Tekintse meg a [Reliable Actors – újbóli belépés](service-fabric-reliable-actors-reentrancy.md) további részletekért.

### <a name="scope-of-concurrency-guarantees"></a>Egyidejűségi garantálja a hatókör
Az Aktorok modul ezen egyidejűségi garanciákat helyzetekben, ahol azt szabályozza, hogy ezek a metódusok meghívása nyújt. Például biztosít ezen garanciát az ügyfél kérelemre válaszul végrehajtott metódus meghívásához, valamint időzítőt, és emlékeztető visszahívásokat. Azonban az aktor kódot közvetlenül ezen kívül a mechanizmusok az Actors modul által biztosított módszerek hív meg, ha a futtatókörnyezet nem adja meg minden egyidejűségi garanciákat. Például ha a metódus meghívása összefüggésben néhány feladat, amely az aktor módszerek által visszaadott a feladathoz nem tartozik a futtatókörnyezet nem adja meg egyidejűségi garanciát. Ha a metódus meghívása szállal, amely az aktor hoz létre a saját majd a futtatókörnyezet is nem tud egyidejűségi garanciát. Ezért háttérbeli műveletek végrehajtásához actors használjon [aktor időzítők és emlékeztetők aktor](service-fabric-reliable-actors-timers-reminders.md) , fordulókra egyidejűségi tiszteletben.

## <a name="next-steps"></a>További lépések
Első lépések: az első Reliable Actors-szolgáltatás létrehozásához:
   * [Ismerkedés a Reliable Actors a .NET](service-fabric-reliable-actors-get-started.md)
   * [Java Reliable Actors – első lépések](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
