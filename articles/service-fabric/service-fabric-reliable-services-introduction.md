---
title: A Service Fabric megbízható szolgáltatás programozási modell áttekintése |} Microsoft Docs
description: További tudnivalók a Service Fabric megbízható programozási modell, valamint Ismerkedés a saját szolgáltatások írása.
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 474cc78a4ceb872742ca7eb10837eeb89dcc1bdb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="reliable-services-overview"></a>A Reliable Services áttekintése
Az Azure Service Fabric leegyszerűsíti az írást, és az állapotmentes és állapotalapú Reliable Services kezelése. Ez a témakör ismerteti:

* A Reliable Services programozási modellt állapotmentes és állapotalapú szolgáltatással.
* A választási lehetőségek módosítania kell a megbízható szolgáltatás írása közben.
* Bizonyos esetekben és példákat a Reliable Services használati és megírásának módjától.

Megbízható szolgáltatások az egyik érhető el a Service Fabric programozási modell. A másik pedig a megbízható szereplő programozási modell, amely fölött a Reliable Services modell virtuális szereplő programozási modellt biztosít. További információ a Reliable Actors programozási modell: [Bevezetés a Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

Service Fabric kezeli a szolgáltatások, az üzembe helyezési és frissítését és törlését, a központi telepítés élettartama keresztül [Service Fabric Alkalmazáskezelés](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Mik azok a Reliable Services?
Megbízható szolgáltatások lehetővé teszi egy egyszerű, hatékony, legfelső szintű programozási modell segítségével express a lényeg az alkalmazáshoz. A Reliable Services programozási modell beolvasása:

* A többi a Service Fabric programozási API-k eléréséhez. Service Fabric szolgáltatások modellezve eltérően [Vendég végrehajtható fájlok](service-fabric-guest-executables-introduction.md), Reliable Services eléréséhez a Service Fabric API-k többi közvetlenül használható. Ez lehetővé teszi, hogy a szolgáltatások:
  * a rendszer lekérdezése
  * a fürt entitások vonatkozó jelentés állapotát
  * konfigurációs és a kód módosítása értesítések fogadása
  * Keresse meg és más szolgáltatásokkal kommunikálni
  * (opcionális) használja a [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md)
  * ...és hozzáférést kaphatnak sok egyéb funkciói mellett minden a számos programozási nyelven első osztályú programozási modellt.
* A futó kód, amely egyszerű modell néz programozási modellek segítségével meg. A kód jól meghatározott belépési pont és egyszerűen kezelhető életciklus rendelkezik.
* A moduláris kommunikációs modellt. Használja az Ön által választott, például a HTTP-alapú, átvitel [Web API](service-fabric-reliable-services-communication-webapi.md), websocket elemeket, egyéni TCP protokollt, vagy bármely más. Megbízható szolgáltatások néhány nagy out-of-az-box beállítások is használhatja, vagy megadhatja a saját adja meg.
* Állapotalapú szolgáltatások esetén a Reliable Services programozási modell lehetővé teszi, hogy következetesen és megbízhatóan használatával tárolhatja a jobb oldalon lévő a szolgáltatás állapota [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md). Megbízható gyűjtemények olyan egyszerű magas rendelkezésre állású és megbízható gyűjteményosztály, amely bárki, aki használta a C# gyűjtemények ismerős lesz. Hagyományosan szolgáltatások megbízható állapotkezelés szükséges külső rendszerekkel. Megbízható gyűjteményéhez az állapot tárolhatja mellett a számítási az azonos magas rendelkezésre állás és a megbízhatóság már származnak szolgáltatásokat a magas rendelkezésre állású külső tárolókat. Ebben a modellben a késés is támogatja, mert meg vannak elhelyezése a számítási és a működéséhez szükséges állapot.

Ezt a videót a Microsoft Virtual Academy Reliable services áttekintése: <center>
<a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">
<img src="./media/service-fabric-reliable-services-introduction/ReliableServicesVid.png" WIDTH="360" HEIGHT="244" />
</a>
</center>

## <a name="what-makes-reliable-services-different"></a>Hasznossá a Reliable Services különböző?
A Service Fabric Reliable Services szolgáltatások előtt lehet, hogy írt eltérnek. A Service Fabric megbízhatóságát, rendelkezésre állási, konzisztencia- és méretezhetőséget biztosít.

* **Megbízhatóság** – a szolgáltatás fel marad, még akkor is, nem megbízható környezetekben, ahol a gépek sikertelen, vagy kattintson a hálózati probléma, vagy amennyiben maguknak a szolgáltatások előforduló hibák és összeomlások, vagy sikertelen. Állapotalapú szolgáltatások esetén is a hálózati vagy más hibák mellett megőrzi a állapotát.
* **Rendelkezésre állási** – a szolgáltatás elérhetőségét és a gyors. Service Fabric fenntartja a másolatok futó kívánt száma.
* **Méretezhetőség** - szolgáltatások rendszer leválasztja a adott hardverekhez, és növekedhet és zsugorítása keresztül hozzáadását és eltávolítását a hardver- vagy egyéb erőforrásokat szükség szerint. Szolgáltatások könnyen particionáltak (különösen az állapot-nyilvántartó esetben) annak érdekében, hogy a szolgáltatás is skálázható és a részleges hibák kezelésének. Szolgáltatások hozhatók létre, és dinamikusan keresztül kóddal, szükség szerint hoz létre további példányokat törölt tegyük fel például, az ügyfelek kéréseire reagálva. Végezetül a Service Fabric szolgáltatások egyszerűsített javasolja. A Service Fabric lehetővé teszi, hogy a folyamat egyetlen, helyett a teljes operációs rendszer példányok megkövetelését, vagy hogy vagy a szolgáltatás egyetlen példányra folyamatok építeni szolgáltatások ezer.
* **Konzisztencia** -a szolgáltatásban tárolt összes adatot garantálható, hogy azok konzisztensek. Ez érvényét veszti, még akkor is a szolgáltatáson belül több megbízható gyűjtemények között. A szolgáltatásokon belüli gyűjtemények között módosításokat hajthat atomi tranzakciós úton módon.

## <a name="service-lifecycle"></a>Szolgáltatás életciklusa
Hogy a szolgáltatási állapot-nyilvántartó vagy állapotmentes, a Reliable Services adjon meg egy egyszerű életciklussal, amely lehetővé teszi, hogy gyorsan beépülő modul a kódot és az első lépések.  Nincsenek kell megvalósítani, hogy a szolgáltatás futó csak egy vagy két módszereket.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** – Ez a módszer akkor, ha a szolgáltatás határozza meg, hogy alkalmazza a kommunikációs stack(s). A kommunikációs verem, például a [Web API](service-fabric-reliable-services-communication-webapi.md), akkor a figyelő végpontot, vagy a végpontokat a szolgáltatáshoz (hogyan ügyfelek elérni a szolgáltatást) határozza meg. Is meghatározza, hogyan működnek együtt a megjelenő üzeneteket és a többi a szolgáltatáskód hibáit.
* **RunAsync** – Ez a módszer, ahol a szolgáltatás fut-e az üzleti logika, és amennyiben azt bármely háttér-feladatokhoz, futtasson-e a szolgáltatás teljes indítsa lenne. A megszakítási token biztosított egy jel amikor munka le kell állnia. Például ha a szolgáltatás egy megbízható várósorból kirakni üzenetek lekéréses és dolgozza fel őket, ez az adott munka történik.

Ha első alkalommal megbízható szolgáltatások még megtanulni, olvassa el a! Ha a keresett részletes útmutató az életciklus megbízható szolgáltatásokat, akkor is látogasson el [Ez a cikk](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Példa szolgáltatások
Ezen programozási modell ismerete, a következőkben a két különböző szolgáltatásokat, hogy hogyan ezek illeszkednek egymáshoz az egyes gyorsan át.

### <a name="stateless-reliable-services"></a>Állapot nélküli megbízható szolgáltatások
Egy állapotmentes szolgáltatások akkor egy, ha nincs a szolgáltatáson belül különböző hívások karbantartása állapot. Található állapotokat teljesen rendelkezésre álló, és nem igényel, szinkronizálás, a replikáció, a adatmegőrzési vagy a magas rendelkezésre állású.

Vegyük példaként a Számológép, amelynek nincs elég memória, és megkapja az összes feltételeit és egyszerre végrehajtandó műveletek.

Ebben az esetben a `RunAsync()` (C#) vagy `runAsync()` (Java) a szolgáltatás lehet üres, mert a háttérben történő feladat-feldolgozás nem, amely a szolgáltatás kell tennie. A Számológép szolgáltatás létrehozásakor adja vissza egy `ICommunicationListener` (C#) vagy `CommunicationListener` (Java) (például [Web API](service-fabric-reliable-services-communication-webapi.md)), megnyílik egy figyelő végpont néhány port. A különböző számítási metódusok csatlakozik a figyelő végpontot (Példa: "Add (n1, n2)"), amely a Számológép nyilvános API határozza meg.

Egy ügyfél a rendszer telefonhívást indít, amikor a megfelelő módszert meghívták, és a Számológép szolgáltatás a megadott adatok műveleteket hajt végre, és visszaadja az eredményt. Az olyan állapotokat nem tárolja.

Ne tárolja az összes belső állapot is leegyszerűsíti a példa Számológép. De a legtöbb szolgáltatások nem valóban állapot nélküli. Ehelyett azok externalize állapotukra néhány más tárolójába. (Például a webes alkalmazás, amely megőrzi a munkamenet-állapot biztonsági vagy gyorsítótár támaszkodik nincs állapotmentes.)

Ilyenek például a hogyan állapotmentes szolgáltatásokhoz használt Service Fabric van, mint egy előtér-webalkalmazás a nyilvánosan elérhető API elérhetővé tévő. Az előtér-szolgáltatás majd kiszolgálóhoz csatlakozik állapotalapú szolgáltatások felhasználói kérelem végrehajtásához. Ebben az esetben egy ismert port, pl. 80, ahol az állapotmentes szolgáltatások figyel ügyfelektől érkező hívások van irányítva. Az állapotmentes szolgáltatások fogadja a hívást, és meghatározza azokat a hívás a megbízható entitások van-e, és azt kiszolgálni szánt.  Az állapotmentes szolgáltatások ezután továbbítja a megfelelő partíció az állapotalapú szolgáltatás hívása, és választ vár. Az állapotmentes szolgáltatások választ kap, amikor az válaszol az ügyfélgépről az eredeti ügyfél. Az általunk biztosított mintákat van ilyen szolgáltatás például [C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/Actors/VisualObjectActor/VisualObjectWebService). Ez csak egy példa az ebben a mintában a mintában, valamint egyéb minták mások szerepelnek.

### <a name="stateful-reliable-services"></a>Állapotalapú Reliable Services
Az állapotalapú szolgáltatás egyike, hogy az állapot következetes és ahhoz, hogy a szolgáltatás jelenlegi tartani a függvény egy részét. Érdemes lehet olyan szolgáltatás, amely folyamatosan kiszámítja az egyes frissítések kap alapján érték mozgóátlaga. Ehhez az szükséges, azt kell rendelkeznie az aktuális folyamat és a jelenlegi átlagos kell bejövő kérelmek. Bármely szolgáltatás beolvassa, feldolgozza és információkat tárol egy külső tároló (például egy Azure blob vagy tábla tároló még ma) egy állapotfüggő. Az a külső tárolóban állapotában csak tartja.

Legtöbb szolgáltatás ma állapotukra kívülről, tárolásához, mert a külső áruházban megbízhatóság, rendelkezésre állását, méretezhetőségét és konzisztencia tartalma az adott állapotban. A Service Fabric nem szükséges szolgáltatásokat kívülről az állapot tárolásához. A Service Fabric ezeket a követelményeket mind a szolgáltatáskód hibáit, és a szolgáltatás állapotának gondoskodik.

Tegyük fel, azt szeretnénk, hogy egy szolgáltatás, amely feldolgozza a képek írni. Ehhez az szükséges, a szolgáltatás vesz igénybe a lemezkép és átalakítás sorozatát hajtsa végre ezt a lemezképet. Ez a szolgáltatás adja vissza egy kommunikációs figyelő (Most tegyük fel, hogy egy WebAPI), hogy az API-k tesz elérhetővé, például `ConvertImage(Image i, IList<Conversion> conversions)`. Ha kap, a szolgáltatás tárolja el azt a egy `IReliableQueue`, és néhány-azonosítót adja vissza az ügyfélnek, akkor is a kérelmek nyomon követésére.

Ez a szolgáltatás a `RunAsync()` összetett lehet. A szolgáltatás esetében egy hurok található az `RunAsync()` , amely lekéri a kérelmek `IReliableQueue` és hajtja végre a kért átalakításra. Az eredmények lekérése tárolja egy `IReliableDictionary` , hogy ha az ügyfél ismét elérhető lesz a konvertált képek kaphatnak. Annak érdekében, hogy akkor is, ha valami nem sikerül a kép nem vesznek el, a megbízható szolgáltatás volna a várósorból kirakni lekéréses, hajtsa végre az átalakításhoz, és az eredmény egy tranzakción belül minden tárolja. Ebben az esetben az üzenet törlődik az üzenetsorból, és az eredmények tárolódnak az eredmény szótár csak akkor, ha teljesülnek az átalakításhoz. Azt is megteheti a szolgáltatás képes a lemezképet a várósorból kirakni lekéréses, és azonnal tárolása egy távoli tároló. Ez csökkenti a szolgáltatás nem tud kezelni állapot, de növeli összetettsége óta a szolgáltatás rendelkezik tartani a távoli tároló kezeléséhez szükséges metaadatok. Bármelyik módszert használja Ha valamit a középső nem sikerült a kérelem marad a feldolgozásra váró várólista.

Vegye figyelembe a szolgáltatással kapcsolatos hozzárendelésekor, hogy úgy tűnik, mint egy normál .NET szolgáltatás! Az egyetlen különbség az, hogy a adatstruktúrákhoz használatban (`IReliableQueue` és `IReliableDictionary`) a Service Fabric által biztosított, és megbízható, elérhető, és egységes.

## <a name="when-to-use-reliable-services-apis"></a>Megbízható szolgáltatások API-k használata
Ha az alkalmazás van szüksége, az alábbi jellemző, akkor érdemes lehet megbízható szolgáltatások API-kat:

* Szeretné, hogy a szolgáltatás kódot (és nem kötelezően állapot) kell lennie a magas rendelkezésre állású és megbízható
* Tranzakciós garanciák kell több egységre is kiterjed (például rendeléseket és a sorrend) állapot között.
* Az alkalmazás állapota természetes modellezhető megbízható szótárakat és a várólisták.
* Az alkalmazások kód vagy állapotban kell lennie a kis késleltetésű olvasási és írási magas rendelkezésre állású.
* Az alkalmazás a feldolgozási vagy a lépésköz legyen tranzakciós műveletek szabályozható a megbízható gyűjteményeket kell.
* Azt szeretné, a kommunikáció felügyeletét, vagy a szolgáltatás particionálási sémáját szabályozza.
* A kódot kell szabadszálas futtatókörnyezetben.
* Az alkalmazás dinamikusan létrehozni, vagy megbízható szótárak vagy várólisták, vagy teljes szolgáltatások futási időben törölni kell.
* Programozott módon szabályozhatja a Service Fabric által biztosított biztonsági mentés, és állítsa vissza a szolgáltatás állapotának funkciói kell.
* Az alkalmazás kell fenntartania változások nyomon követése az egységek állapot.
* Fejlesztése vagy harmadik fél fejlett, egyéni állapotszolgáltatója használni kívánt.

## <a name="next-steps"></a>További lépések
* [Megbízható szolgáltatások – első lépések](service-fabric-reliable-services-quick-start.md)
* [Megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md)
* [A Reliable Actors programozási modell](service-fabric-reliable-actors-introduction.md)
