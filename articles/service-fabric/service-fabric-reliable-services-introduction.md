---
title: A Service Fabric Reliable Services programozási modell áttekintése |} A Microsoft Docs
description: További tudnivalók a Service Fabric Reliable Services programozási modell és kezdheti meg a saját szolgáltatások írása.
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
ms.openlocfilehash: bc4e4c9137003dad9cfd96772b7ebe231cd6eace
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171723"
---
# <a name="reliable-services-overview"></a>A Reliable Services áttekintése
Azure Service Fabric megkönnyíti a írása és állapot nélküli és állapotalapú Reliable Services kezeléséhez. Ez a témakör ismerteti:

* A Reliable Services programozási modell állapot nélküli és állapotalapú szolgáltatásokhoz.
* A lehetőségek, akkor el kell végeznie egy Reliable Services írásakor.
* Bizonyos forgatókönyvek és a Reliable Services használata, és hogyan írt példák.

A Reliable Services egyike az elérhető a Service Fabric programozási modellek. A másik pedig a Reliable Actors programozási modell, amely fölött a Reliable Services-modell virtuális Actors programozási modellt biztosít. A Reliable Actors programozási modell további információkért lásd: [Bevezetés a Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

A Service Fabric-n keresztül kezeli a szolgáltatások, a kiépítés és üzembe helyezést, frissítési és törlési, teljes élettartama [Service Fabric-alkalmazásfelügyelet](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Mik azok a Reliable Services?
A Reliable Services egy egyszerű, hatékony, legfelső szintű programozási modell segítségével az express, fontos, hogy az alkalmazás milyen segítséget nyújt. A Reliable Services programozási modell a következőket kínálja:

* A Service Fabric programozási API-k a többi való hozzáférést. Ellentétben modellezve a Service Fabric Services [futtatható Vendégalkalmazás](service-fabric-guest-executables-introduction.md), Reliable Services juthat el a Service Fabric API-k a többi közvetlenül használni. Ez lehetővé teszi, hogy a szolgáltatások:
  * a rendszer lekérdezése
  * a fürt entitásokról jelentés állapota
  * konfiguráció és a kód változásaira vonatkozó értesítések fogadása
  * Keresse meg és más szolgáltatásokkal kommunikálni
  * (opcionális) használja a [a Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
  * ...és hozzáférést kaphatnak sok egyéb funkciói mellett minden a számos programozási nyelven első osztályú programozási modellt.
* Programozási modelleket, amellyel egy egyszerű modellt, amely a saját kód futtatásához tűnik. A kód jól definiált belépési pont és egyszerűen kezelhető életciklus rendelkezik.
* Egy moduláris kommunikációs modellt. Használja a szállítás megírhatja, például HTTP- [webes API-t](service-fabric-reliable-services-communication-webapi.md), a websockets protokoll, egyéni TCP protokollt, vagy bármi más. A Reliable Services-a-beépített beállításokat használhatja, vagy megadhatja a saját néhány nagyszerű adja meg.
* Az állapotalapú szolgáltatások esetében a Reliable Services programozási modell lehetővé teszi, hogy következetes és megbízható tárolását használatával közvetlenül a szolgáltatáson belül az állapot [a Reliable Collections](service-fabric-reliable-services-reliable-collections.md). A Reliable Collections olyan egyszerű, magas rendelkezésre állású és megbízható gyűjteményosztályok, amelyeket mindenki, aki már használt jól lesz C# gyűjteményeket. Hagyományosan szolgáltatások megbízható állapot felügyeletéhez szükséges külső rendszerekkel. Reliable Collections használata is tárolhatja az állapot mellett a számítási erőforrásokat az azonos magas rendelkezésre állás és a megbízhatóság jár várhatóan magas rendelkezésre állású külső áruházakból származó. Ez a modell is javítani tudja a késés, mert, amelyek közös helyen való elhelyezése a számítási és -állapot működéséhez.

## <a name="what-makes-reliable-services-different"></a>A Reliable Services különböző teszi?
A Service Fabric Reliable Services szolgáltatások előtt lehet, hogy írt eltérnek. A Service Fabric megbízhatóságát, rendelkezésre állást, konzisztencia és méretezhetőséget biztosít.

* **Megbízhatóság** – a szolgáltatás fel marad, még akkor is, ha a gépek nem, vagy nyomja le a hálózati problémák megbízhatatlan környezetek, vagy olyan esetekben, ahol a szolgáltatások előforduló hibákat és az összeomlási, vagy sikertelen. Az állapotalapú szolgáltatások esetében az állapot megmarad, még a hálózati vagy más hibák folytonosságát.
* **Rendelkezésre állási** -e a szolgáltatása, elérhető és válaszkész legyen. A Service Fabric a kívánt számú példányt futtató tart.
* **Méretezhetőség** - szolgáltatások vannak választva a adott hardverekhez, és azok alatt megnöveljék vagy csökkentsék a szükséges keresztül hozzáadását és eltávolítását a hardver- vagy egyéb erőforrásokat is. Szolgáltatások egyszerűen particionáltak (különösen az állapot-nyilvántartó eset) annak érdekében, hogy a szolgáltatás méretezhetők, és a részleges hibáinak a kezelése. Szolgáltatások hozhatók létre, és dinamikusan keresztül code-hoz létre szükség esetén további példányok engedélyezésével törli az ügyfelek kéréseire reagálva tegyük fel, hogy. Végül a Service Fabric services az egyszerűsített javasolja. A Service Fabric lehetővé teszi, hogy a több ezer olyan szolgáltatások belül egyetlen folyamat, és egy szolgáltatás egyetlen példánya folyamatok helyett megkövetelését vagy dedikálni teljes operációsrendszer-példányt.
* **Konzisztencia** – ebben a szolgáltatásban tárolt összes adatot is garantáltan konzisztens. Ez a még több megbízható gyűjteményt egy szolgáltatás között. A szolgáltatásokon belüli gyűjtemények között módosítás atomi tranzakciós szempontból módon.

## <a name="service-lifecycle"></a>Szolgáltatás-életciklusának
A szolgáltatás működik, állapotalapú és állapot nélküli, hogy a Reliable Services egy egyszerű életciklussal, amely lehetővé teszi a kód gyorsan beépülő modul és az első lépések adja meg.  Meg kell valósítania a szolgáltatás beolvasásához, majd futtassa, csak egy vagy két módszer van.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** – Ez a módszer, ahol a szolgáltatás határozza meg a kommunikációs stack(s), amely azt szeretne használni. A kommunikációs verem, mint például [webes API-t](service-fabric-reliable-services-communication-webapi.md), van, a figyelő végpont vagy a végpontok a szolgáltatás (hogyan ügyfelek elérni a szolgáltatást) határozza meg. Azt is meghatározza, az üzenetek jelennek meg, hogyan használják a szolgáltatást kód többi.
* **RunAsync** – Ez a módszer, ahol a szolgáltatás fut, az üzleti logikát, és ahol azt szeretné indíthat bármely háttérfeladatokat, amelyek a szolgáltatás teljes élettartama futnia kell. A megszakítás token biztosított egy olyan jelet, amikor le kell állnia, a munka esetében. Például ha a szolgáltatás üzenetek megbízható várólistáról és feldolgozni azokat, ez a ahol a munka történik.

Ha első alkalommal a reliable services tárgyául, olvassa el a! Ha részletes leírását a reliable services életciklusa keres, akkor is látogasson el [Ez a cikk](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Példa a szolgáltatásokra
A programozási modell ismerete, két különböző szolgáltatások megtekintéséhez, hogyan működnek ezek darab együtt egy gyors pillantást vessünk.

### <a name="stateless-reliable-services"></a>Állapot nélküli Reliable Services
Állapotmentes szolgáltatás, amelyikben nincs állapot, a szolgáltatásban a hívások között megtartott van. Bármilyen állapot, amely akkor jelenik teljesen pótolhatók, és nem igényel, szinkronizálás, replikáció, adatmegőrzés vagy magas rendelkezésre állás.

Vegyük példaként egy számító eszköz, amely nincs elég memóriája, és megkapja a feltételeket és a műveletek végrehajtásához egyszerre.

Ebben az esetben a `RunAsync()` (C#) vagy `runAsync()` (Java) a szolgáltatás lehet üres, mert nincs háttérben történő feladat-feldolgozás, amely a szolgáltatás szüksége van. A kalkulátor szolgáltatás létrehozásakor, akkor adja vissza egy `ICommunicationListener` (C#) vagy `CommunicationListener` (Java) (például [webes API-t](service-fabric-reliable-services-communication-webapi.md)), amely egy bizonyos porton figyel-e végpontot nyit meg. Ez a figyelő végpont hooks a különböző számítási metódusok (Példa: "Hozzáadása (n1, n2)"), amely meghatározása a díjkalkulátorban feltüntetett nyilvános API-t.

Amikor egy ügyfél kezdeményezték, a megfelelő metódus meghívásakor kerül, és a Számológép szolgáltatás a megadott adatok műveleteket hajt végre, és visszaadja az eredményt. Minden olyan állapotban, nem tárolja.

Ne tárolja az összes belső állapot leegyszerűsíti a példa díjkalkulátort. De a legtöbb szolgáltatás nem igazán állapot nélküli. Ehelyett azok externalize néhány egyéb tároló az állapotuk. (Például minden olyan webalkalmazást üzemeltet, amely tárolja a munkamenet-állapot a mögöttes tároló vagy gyorsítótár támaszkodik nem áll állapotmentes.)

Gyakori például hogyan állapotmentes szolgáltatások használhatók a Service Fabric van, mint egy előtér-, amely közzéteszi a nyilvános API webalkalmazás számára. Az előtér-szolgáltatás majd műsorgazdája állapotalapú szolgáltatások egy felhasználói kérelem végrehajtásához. Ebben az esetben az ügyfelek hívásait irányítja egy ismert port, pl. 80-as, ahol az állapotmentes szolgáltatás figyel. Az állapotmentes szolgáltatás fogadja a hívást, és határozza meg, hogy a hívás egy megbízható entitás, és azt kiszolgálni szánt.  Az állapotmentes szolgáltatás ezután továbbítja a hívást a megfelelő partícióba, az állapotalapú szolgáltatásból, és a válaszra vár. Az állapotmentes szolgáltatás választ kap, ha az eredeti ügyfél válaszol. Ilyen szolgáltatás például a minták tallózása a [ C# ](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)  /  [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started). Ez csak egy példa ezt a mintát, a minták, léteznek egyéb más mintákban.

### <a name="stateful-reliable-services"></a>A stateful Reliable Services
Az állapotalapú szolgáltatások egyike, hogy az állapot egységes és ahhoz, hogy a szolgáltatás jelenlegi tartani a függvény valamely része. Érdemes lehet egy szolgáltatás, amely folyamatosan kiszámítja az egyes értékek alapján kap frissítéseket mozgóátlaga. Ehhez a bejövő kérelmek, a folyamat és a jelenlegi átlag kell az aktuális készletét kell legyen. Minden olyan szolgáltatás, amely beolvassa, feldolgozza és információkat tárolja egy külső tároló (például egy Azure blob vagy table store ma) állapot-nyilvántartó. Csak a külső állapotuk tárolójában tartja a állapotában.

A legtöbb szolgáltatás még ma állapotuk kívülről, tárolására, mivel a külső tároló mit kínál megbízhatóságát, rendelkezésre állást, méretezhetőséget és konzisztencia az állapotban. A Service Fabric services nem szükséges külső állapotuk tárolja. A Service Fabric gondoskodik ezeknek a feltételeknek, a szolgáltatás kódot és a szolgáltatás állapotát.

Tegyük szeretnénk írhat olyan szolgáltatás, amely képeket dolgoz fel. Ehhez a szolgáltatás fogadja a kép és átalakítások sorozata a rendszerkép végrehajtásához. Ez a szolgáltatás adja vissza egy kommunikációs figyelőjének (Most tegyük fel, hogy a WebAPI szó), hogy rendelkezik egy API-t, például `ConvertImage(Image i, IList<Conversion> conversions)`. Egy kérést kap, ha a szolgáltatás menti egy `IReliableQueue`, és visszaadja az ügyfélnek bizonyos azonosítója, azt is nyomon követheti a kérelem.

Ebben a szolgáltatásban `RunAsync()` összetettebb lehet. A szolgáltatásnak van egy ciklus található a `RunAsync()` , amely lekéri a kérések közül `IReliableQueue` és hajtja végre a kért átalakítás. Az eredmények lekérése tárolja egy `IReliableDictionary` , hogy ha az ügyfél ismét hozzáférhetnek a konvertált képek. Annak érdekében, hogy akkor is, ha hiba lép fel a képet nem vesznek el, a Reliable Services lenne a várólistáról lekéréses, az átalakítás végrehajtható, és tárolja az eredmény egy tranzakció. Ebben az esetben az üzenet el van távolítva az üzenetsorból, és az eredmények tárolása az eredménye szótárkészítéses csak ha az átalakítás befejeződött. Azt is megteheti a szolgáltatás képes a várólistáról a rendszerkép lekérése, és azonnal egy távoli tárolóban tárolja. Ez csökkenti az állapot kezelése a szolgáltatás rendelkezik, de növeli összetettséget óta a szolgáltatás rendelkezik, így a szükséges metaadatokat a távoli tároló kezelése. Bármelyik módszert használja Ha valami nem sikerült, a középső a kérelem marad a feldolgozásra váró várólistában.

Egy változás, hogy a szolgáltatásról, hogy, megosztásánál normál .NET szolgáltatás! Az egyetlen különbség, hogy a adatstruktúrák használt (`IReliableQueue` és `IReliableDictionary`) a Service Fabric által biztosított, és nagymértékben megbízható, elérhető és konzisztens.

## <a name="when-to-use-reliable-services-apis"></a>A Reliable Services API-k használata
Ha az alkalmazás szolgáltatásokra van szüksége, az alábbi jellemezhető, akkor érdemes lehet a Reliable Services API-kat:

* A webszolgáltatás kódjához szeretné (és opcionálisan állapot) kell magas rendelkezésre állású és megbízható
* Tranzakciós garanciák több egységet (például megrendelések és a rendelés) állapotban van szüksége.
* Az alkalmazás állapota természetes módon modellezhető megbízható szótárakban és üzenetsorok.
* Az alkalmazások kódja vagy állapotban kell lennie az alacsony késés olvasási és írási magas rendelkezésre állású.
* Az alkalmazásnak kell szabályozhatja a párhuzamosság vagy a tranzakciós műveletek közül egy vagy több megbízható gyűjtemények között.
* Érdemes kommunikáció kezelése, illetve a szolgáltatás a particionálási séma vezérlését.
* A kód egy szabad szállal futtatási környezetet kell.
* Az alkalmazásnak kell dinamikusan létrehozni, vagy szüntesse meg a megbízható szótárakban vagy az üzenetsorok vagy a teljes szolgáltatások futásidőben.
* Programozott módon a Service Fabric által biztosított biztonsági mentés vezérlőelemet, és állítsa vissza a szolgáltatás állapotának funkciói kell.
* Az alkalmazásnak kell változások nyomon követése az egységek állapotának karbantartása.
* Szeretné fejlesztése vagy harmadik féltől származó fejlesztett, egyéni állapotszolgáltatója felhasználását.

## <a name="next-steps"></a>További lépések
* [A Reliable Services – gyorsútmutató](service-fabric-reliable-services-quick-start.md)
* [A Reliable collections](service-fabric-reliable-services-reliable-collections.md)
* [A Reliable Actors programozási modell](service-fabric-reliable-actors-introduction.md)
