---
title: "Service Fabric Reliable Actors áttekintése |} Microsoft Docs"
description: "A Service Fabric Reliable Actors programozási modell bemutatása."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 7fdad07f-f2d6-4c74-804d-e0d56131f060
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 640e051a909b1b9457b20cbd507b418342297c6e
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>A Service Fabric Reliable Actors bemutatása
Reliable Actors a Service Fabric alkalmazási keretrendszer alapján a [virtuális szereplő](http://research.microsoft.com/en-us/projects/orleans/) mintát. A megbízható Actors API a Service Fabric skálázhatóságát és megbízhatóságát garanciák épülő programozási egyszálas modellt biztosít.

## <a name="what-are-actors"></a>Mik azok a szereplője?
Egy szereplő a számítási műveletek és rendelkező egyszálas végrehajtási állapot egy elkülönített, független egység. A [szereplő mintát](https://en.wikipedia.org/wiki/Actor_model) olyan számítási modellt egyidejű vagy elosztott rendszerek, amelyek nagyszámú ezek szereplője végrehajtható egyidejűleg, és egymástól függetlenül. Szereplője kommunikálhatnak egymással, és további szereplője hozhatnak létre.

### <a name="when-to-use-reliable-actors"></a>Mikor érdemes használni a Reliable Actors
Service Fabric Reliable Actors szereplő kialakítási minta megvalósítása. Minden szoftver a kialakítási mintában a a döntést, egy adott minta használ-e történik-e a szoftverfrissítések tervezése probléma alapján megfelel a mintának.

Bár a szereplő kialakítási minta is jó alkalmasnak elosztott rendszerek problémák és forgatókönyvek, gondosan kell tenni a minta és bevezetné, a keretrendszer korlátozásait figyelembevételével számú. Általános útmutatásként fontolja meg a probléma vagy forgatókönyv modellezését, ha a szereplő mintát:

* A probléma tárhely magában foglalja a sok (több ezer vagy több) kis, független és elkülönített állapot és a logikai egységek.
* Azt szeretné, külső összetevők, beleértve az állapot lekérdezése szereplője csoportja között jelentős beavatkozást nem igénylő egyszálas objektumok.
* A szereplő példányok nem blokkolja a hívók előre nem látható késlelteti az i/o-műveletek kiállításával.

## <a name="actors-in-service-fabric"></a>A Service Fabric actors
A Service Fabric szereplője valósíthatók meg a Reliable Actors keretrendszer: a beépített szereplő minta-alapú alkalmazás-keretrendszer [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md). Minden megbízható szereplő szolgáltatás ír egy ténylegesen egy particionált, állapot-nyilvántartó megbízható szolgáltatás.

Minden aktor egy szereplő típusú, a .NET-objektum egy .NET-típus egy példánya nem módosul azonos típusúként van definiálva. Például előfordulhat, hogy egy olyan Számológép funkcióit megvalósító szereplő típus, és előfordulhat, hogy sok szereplője az adott típusú fürt elosztott különböző csomópontokon. Minden ilyen szereplő egyedileg azonosít egy szereplő.

### <a name="actor-lifetime"></a>Aktor élettartama
A Service Fabric szereplője virtuális, ami azt jelenti, hogy az élettartamuk nem kapcsolódna a memórián belüli megjelenítésre. Ennek eredményeképpen nincs szükségük explicit módon létrehozott vagy megsemmisül. A Reliable Actors futásidejű automatikusan aktiválja a szereplő első ideje kap egy adott szereplő azonosítóval. Ha egy szereplő nem használják-e egy adott időn belül, a Reliable Actors futásidejű szemétgyűjtési-gyűjti a memóriában lévő objektum. Is megőrzi a szereplő fenntartása ismerete ki kellene később újra kell aktiválni. További részletekért lásd: [szereplő életciklust és a szemétgyűjtési gyűjtemény](service-fabric-reliable-actors-lifecycle.md).

A virtuális szereplő élettartama absztrakciós hordoz magában, ha bizonyos korlátozásokkal miatt a virtuális szereplő modell, és valójában a Reliable Actors megvalósítási néha eltér a modell.

* Egy szereplő automatikusan aktiválódik (egy szereplő objektumot kell kialakítani, amely) először egy üzenetet küld az aktor azonosítóját. Néhány bizonyos idő eltelte után a szereplő objektum szemétgyűjtő. A jövőben a azonosítójával szereplő újra, a hozható létre egy új szereplő objektumot. Egy aktorállapot outlives az objektumok az állapotkezelő található.
* Adott szereplő bármely aktormetódus hívja a szereplő Azonosítóval aktiválja. Emiatt a szereplő típusokhoz az implicit módon a futtatókörnyezet által meghívott konstruktor. Ezért Ügyfélkód nem adhatók át paraméterek az aktor típus konstruktora, bár paraméterek továbbítható a szereplő konstruktor által a szolgáltatás. Az eredménye, hogy gyakrabban lehet úgy részben inicializált állapotban által a más módszerekkel hívják meg, ha a szereplő inicializálási paramétereket az ügyfél igényel. Nincs az ügyfél egy szereplő aktiválásának egy belépési pont.
* Bár a Reliable Actors implicit létrehozása szereplő objektumok; lehetősége nyílik explicit módon törli egy szereplő és annak állapotát.

### <a name="distribution-and-failover"></a>Telepítési és a feladatátvétel
Adja meg a skálázhatóságát és megbízhatóságát, a Service Fabric elosztja a fürt teljes szereplője, majd automatikusan áttelepíti őket a meghibásodott csomópontok szükség szerint kifogástalan meglévők közül. Ez az absztrakciós keresztül egy [particionált, állapot-nyilvántartó megbízható szolgáltatás](service-fabric-concepts-partitioning.md). Terjesztési, méretezhetőség, megbízhatóság és automatikus feladatátvételt-e minden megadott a tényen szereplője belül futó állapot-nyilvántartó megbízható szolgáltatás hívása a *szereplő szolgáltatás*.

A partíciók az Aktor szolgáltatás szereplője elosztott, és ezek a partíciók elosztott a Service Fabric-fürt csomópontja. Minden szolgáltatás partíció szereplője tartalmaz. Kezeli a Service Fabric terjesztési és a feladatátvétel szolgáltatás partíciók.

Például az alapértelmezett szereplő partíció elhelyezés használatával három csomópontjaira telepített kilenc partíciókkal rendelkező szereplő szolgáltatásnak volna terjeszteni thusly:

![Megbízható szereplője terjesztési][2]

Az Aktor keretrendszer partíció protokollt és a kulcs tartomány beállításainak kezelése meg. Ez egyszerűbbé teszi a néhány, de is hordoz magában, ha néhány szempont:

* Megbízható szolgáltatások lehetővé teszi, hogy a particionálási sémát, a kulcs tartományon (particionálási sémát széles használatakor) kiválasztását, és a partíció száma. Reliable Actors férhetnek hozzá a particionálási sémát (az egységes Int64 séma) tartomány, és megköveteli, hogy a teljes Int64-tartományt használja.
* Alapértelmezés szerint szereplője véletlenszerűen kerülnek, ami egységes terjesztési partíciókra.
* Szereplője véletlenszerűen kerülnek, mert ez várható szereplő műveletek mindig megkövetelik a hálózati kommunikációt, beleértve a szerializálás és a deszerializálás metódus hívása adatok nélül késleltetés és a terhelést.
* Speciális forgatókönyvekhez is lehet vezérlő szereplő partíció elhelyezési Int64 szereplő, amelyek adott partíciókra azonosítók használatával. Azonban ezzel úgy eredményezheti egy egyenetlen eloszlását szereplője partíciók között.

A aktorszolgáltatások particionálásáról további információkért tekintse meg [fogalmak particionálás szereplője](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

### <a name="actor-communication"></a>Aktor kommunikáció
Az aktor, amely megvalósítja a felületet, és proxy lekérdezi egy szereplő ugyanazon a felületen keresztül az ügyfél által közösen használt illesztőfelület szereplő kapcsolati vannak definiálva. Mivel ez az interfész szereplő módszerek meghívására aszinkron módon van használatban, a felület minden metódusa feladatot visszaadó kell lennie.

Metódus meghívásához és a válaszok végső soron a hálózati kérelmek a fürtön, így az argumentumok és eredményezi a eredménytípusai feladat, amely akkor adja vissza a platform szerializálhatónak kell lennie. Különösen kell [adategyezmény-szerializálható](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

#### <a name="the-actor-proxy"></a>Az aktor proxy
A Reliable Actors ügyfél API-ja biztosít szereplő példány és egy szereplő ügyfél közötti kommunikációhoz. Egy szereplő kommunikálni, egy ügyfél hoz létre, amely megvalósítja az aktor felületet szereplő proxy objektum. Az aktor módszerek proxy objektumon, hogy az ügyfél kommunikál. Az aktor proxy ügyfél-aktor és aktor szereplő kommunikációhoz használható.

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


Vegye figyelembe, hogy a két szereplő proxy objektum létrehozásához használt információnak a szereplő azonosító és az alkalmazás nevét. Az aktor azonosító egyedileg azonosítja a szereplő, az alkalmazás neve azonosítja a [Service Fabric-alkalmazás](service-fabric-reliable-actors-platform.md#application-model) a szereplő telepítési helyét.

A `ActorProxy`(C#) / `ActorProxyBase`(Java) osztály ügyféloldali hajtja végre a szükséges névfeloldást keresse meg a szereplő-azonosító szerint, és nyissa meg azt a kommunikációs csatornát. Újra megpróbálja a szereplő keresse meg a kommunikációs hibák és a feladatátvétel. Üzenet kézbesítési következtében a következő jellemzőkkel rendelkezik:

* Üzenet kézbesítési a lehető legkedvezőbb módon.
* Szereplője duplikált üzenetek fogadása is ugyanazt az ügyfelet.

### <a name="concurrency"></a>Egyidejűség
A Reliable Actors futásidejű szereplő módszerek eléréséhez egyszerű kapcsolja-alapú hozzáférés modellt biztosít. Ez azt jelenti, hogy legfeljebb egy szál lehet aktív belső az aktor objektum kódot bármikor. Kapcsolja a szerepköralapú hozzáférés egyidejű rendszerek jelentősen egyszerűbb, mivel nincs szükség a szinkronizálási mechanizmus az adatelérési. Azt is jelenti, rendszerek minden szereplő példány egyszálas hozzáférés jellegét szempontot kell megtervezni.

* A szereplő egyetlen példánya több kérelem nem dolgozható fel egyszerre. Aktor példánya a teljesítmény szűk keresztmetszetek okozhat, ha a várható egyidejű kérelmek kezeléséhez.
* Gyakrabban is kölcsönös kizárás egymástól, ha két szereplője, amíg egy külső kérelem egy a szereplője egyidejűleg között körkörös kérelmet. Az aktor futásidejű automatikusan lévő aktorhívások időtúllépéssel fejeződött be, és kivételt jelez a hívónak feleslegesen zavarni holtpont lehetséges helyzetben.

![Megbízható szereplője kommunikációt][3]

#### <a name="turn-based-access"></a>Kapcsolja a szerepköralapú hozzáférés
Egy kapcsolja más szereplője vagy az ügyfelek egy irányuló kérelemre adott válasz egy aktormetódus teljes végrehajtását, vagy a teljes végrehajtását tartalmaz egy [időzítő/emlékeztető](service-fabric-reliable-actors-timers-reminders.md) visszahívás. Annak ellenére, hogy ezen metódusok és a visszahívások aszinkron, a gyakrabban futásidejű nem interleave őket. Egy teljesen kész kell, mielőtt új kapcsolja engedélyezett. Ez azt jelenti jelenleg feldolgozás alatt álló egy szereplő metódus vagy időzítő/emlékeztető visszahívás teljesen befejeződött egy új metódus hívása előtt kell lennie, vagy visszahívási engedélyezett. A metódus vagy a visszahívási tekinthető végzett, ha a végrehajtás tért vissza a metódusból vagy visszahívási és a feladat a metódust vagy a visszahívási által visszaadott befejeződött. Érdemes fogalmazás, hogy kapcsolja-alapú feldolgozási tiszteletben tartják még különböző módszereket, időzítők és visszahívások között.

A szereplője futásidejű kapcsolja-alapú feldolgozási által az beszerzése egy aktoronkénti zárolásra egy kapcsolja elején és végén található a kapcsolja a zárolás feloldása érvénybe lépteti. Ebből kifolyólag kapcsolja-alapú feldolgozási aktoronkénti alapon és szereplője között nem érvényes. Aktor módszerek egyidejűleg végrehajtható időzítő/emlékeztető visszahívások, különböző szereplője nevében.

Az alábbi példában látható a fenti fogalmakat. Vegye figyelembe az aktor típusa, amely megvalósítja az két aszinkron metódusok (tegyük fel például, *Method1* és *Method2*), időzítő, valamint egy emlékeztető. Az alábbi ábra szemlélteti, két szereplője nevében ezen módszerek és a visszahívások végrehajtásának ütemterv (*ActorId1* és *ActorId2*), amely a szereplő típus tartozik.

![Megbízható szereplője futásidejű kapcsolja-alapú feldolgozási és a hozzáférés][1]

Ez az ábra ezeket a szabályokat követi:

* Minden egyes függőleges vonal szemlélteti a logikai metódust, illetve egy visszahívás végrehajtása egy adott szereplő nevében.
* A függőleges soronként jelölésű esemény újabb régieket alatt bekövetkező események időrendi sorrendben következik be.
* Különböző színek különböző szereplője megfelelő ütemtervek használ.
* Jelzi az időtartam, amelynek az aktoronkénti zárolásra nevében metódust vagy visszahívási tárolt kiemelés szolgál.

Néhány fontos tényezőt kell figyelembe venni:

* Amíg *Method1* metódus végrehajtása a következő nevében: *ActorId2* ügyfél irányuló kérelemre adott válasz *xyz789*, egy másik ügyfélkérés (*abc123*) érkezik, amely megköveteli azt is, *Method1* hajtja végre *ActorId2*. Azonban a második végrehajtása *Method1* nem kezdődik, amíg a korábbi végrehajtása nem fejeződött be. Hasonlóképpen, a emlékeztető által regisztrált *ActorId2* következik be, amikor *Method1* ügyfél irányuló kérelemre adott válasz végrehajtott *xyz789*. A felszólítás visszahívás végrehajtása mindkét végrehajtások, miután *Method1* befejeződött. Mindez okozza-e a kényszerítést kapcsolja-alapú feldolgozási *ActorId2*.
* Hasonlóképpen, kapcsolja-alapú feldolgozási is az érvényes *ActorId1*, amint azt a végrehajtása *Method1*, *Method2*, és a időzítő visszahívás nevében *ActorId1* soros módon történik.
* Végrehajtásának *Method1* nevében *ActorId1* átfedésben van a végrehajtása a következő nevében: *ActorId2*. Ennek az az oka kapcsolja-alapú feldolgozási csak egy szereplő belül és szereplője között nem kényszeríti ki.
* Egyes metódus/visszahívási végrehajtások a `Task`(C#) / `CompletableFuture`(Java) a módszer/visszahívási befejeződik által visszaadott, miután a metódus visszaadja. A más az aszinkron művelet már befejeződött a időpontjára, a módszer/visszahívási adja vissza. Mindkét esetben az aktoronkénti zárolásra felszabadul csak mind a módszer/visszahívási adja vissza, és az aszinkron művelet befejeződése után.

#### <a name="reentrancy"></a>Rögzítve
A szereplője futásidejű rögzítve alapértelmezés szerint lehetővé teszi. Ez azt jelenti, hogy ha egy aktormetódus a *Aktor A* metódus meghívja *Aktor B*, amely meghívja a másik módszer a *Aktor A*, hogy engedélyezi-e módszer futtatásához. Ennek az az oka az azonos logikai hívás-lánc-környezet részét képezi. Az összes időzítő és felszólítás hívás az új logikai hívás környezetben kezdődik. Tekintse meg a [Reliable Actors rögzítve](service-fabric-reliable-actors-reentrancy.md) további részleteket.

#### <a name="scope-of-concurrency-guarantees"></a>Párhuzamossági garanciák hatókör
A szereplője futásidejű ezek párhuzamossági garanciákat olyan esetekben, ahol azt szabályozza, hogy ezek a metódusok meghívását nyújt. Például azzal a garanciákat nyújt, amely egy ügyfél irányuló kérelemre adott válasz végzett a metódus meghívásához, valamint időzítő és felszólítás visszahívások. Azonban a szereplő kódot közvetlenül hív meg, ezek a módszerek a szereplője futtatókörnyezet által megadott mechanizmusok kívül, ha a futtatókörnyezet nem adja meg minden párhuzamossági garanciák. Például ha a metódus hivatkoznak, néhány feladatot, amely nincs társítva a tevékenység aktor módszerek által visszaadott a környezetében, a futtatókörnyezet nem adja meg párhuzamossági garanciát. Ha a metódus egy olyan szálból, amely a szereplő hoz létre a saját hivatkoznak, a futtatókörnyezet is nem adja meg párhuzamossági garanciát. Ezért háttérbeli műveletek végrehajtásához szereplője használandó [szereplő időzítők és szereplő emlékeztetők](service-fabric-reliable-actors-timers-reminders.md) , figyelembe vegyék kapcsolja-alapú feldolgozási.

## <a name="next-steps"></a>Következő lépések
Ismerkedés az első Reliable Actors szolgáltatás épület:
   * [Bevezetés a Reliable Actors a .NET használatába](service-fabric-reliable-actors-get-started.md)
   * [Ismerkedés a Java a Reliable Actors](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
