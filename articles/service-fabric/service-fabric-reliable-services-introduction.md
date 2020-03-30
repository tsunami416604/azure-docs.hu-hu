---
title: A Megbízható szolgáltatás programozási modell áttekintése
description: Ismerje meg a Service Fabric megbízható szolgáltatás programozási modelljét, és kezdje el megírni a saját szolgáltatásait.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: 58259b0d19d68c468779a579bd9c86e77106c18d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083517"
---
# <a name="reliable-services-overview"></a>A Reliable Services áttekintése

Az Azure Service Fabric leegyszerűsíti az állapotnélküli és állapotalapú szolgáltatások írását és kezelését. Ez a témakör a következőkkel foglalkozik:

* A Reliable Services programozási modell állapot nélküli és állapotalapú szolgáltatások.
* A megbízható szolgáltatás írásakor meghozandó döntések.
* Néhány forgatókönyv és példák, hogy mikor kell használni a Reliable Services és hogyan vannak írva.

*A Reliable Services* a Service Fabric en elérhető programozási modellek egyike. Egy másik a *reliable actor* programozási modell, amely egy [virtuális szereplő](https://research.microsoft.com/en-us/projects/orleans/) alkalmazás keretrendszer a Reliable Services modell tetején. A megbízható szereplőkkel kapcsolatos további információkért [lásd: Bevezetés a szolgáltatásfabric megbízható szereplőinek bemutatása című témakörben.](service-fabric-reliable-actors-introduction.md)

A Service Fabric kezeli a szolgáltatások élettartamát, a kiépítéstől és üzembe helyezéstől a frissítésig és a törlésig, a [Service Fabric alkalmazáskezelésen](service-fabric-deploy-remove-applications.md)keresztül.

## <a name="what-are-reliable-services"></a>Mik azok a megbízható szolgáltatások

A Megbízható szolgáltatások egyszerű, hatékony, legfelső szintű programozási modellt kínál, amely segít kifejezni, hogy mi fontos az alkalmazásához. A Reliable Services programozási modella következőket kapja:

* Hozzáférés a Service Fabric API-khoz. A [vendég végrehajtható fájlként](service-fabric-guest-executables-introduction.md)modellezett Service Fabric-szolgáltatásokkal ellentétben a Reliable Services közvetlenül használhatja a Service Fabric API-kat. Ez lehetővé teszi a szolgáltatások:
  * A rendszer lekérdezése
  * Állapot jelentése a fürtben lévő entitásokról
  * Értesítések fogadása a konfigurációról és a kódváltozásairól
  * Más szolgáltatások keresése és kommunikációja,
  * A [megbízható gyűjtemények használata](service-fabric-reliable-services-reliable-collections.md)
  * Számos más funkcióhoz is hozzáférhet, mindezt egy első osztályú programozási modellből, több programozási nyelven.
* Egy egyszerű modell fut a saját kódot, hogy úgy érzi, mint más ismerős programozási modellek. A kód jól meghatározott belépési ponttal és könnyen kezelhető életciklussal rendelkezik.
* Dugaszolható kommunikációs modell. Használja az Ön által választott átvitelt, például a HTTP [webes API-val,](service-fabric-reliable-services-communication-webapi.md)websocketekkel, egyéni TCP-protokollokkal vagy bármi mással. A Megbízható szolgáltatások nagyszerű használatra alkalmas lehetőségeket kínálnak, amelyeket használhat, vagy saját ot is megadhat.
* Állapotalapú szolgáltatások esetén a Reliable Services programozási modell lehetővé teszi, hogy következetesen és megbízhatóan tárolja az állapotot közvetlenül a szolgáltatáson belül [a Reliable Collections](service-fabric-reliable-services-reliable-collections.md)használatával. Megbízható gyűjtemények egy egyszerű készlet magas rendelkezésre állású és megbízható gyűjtemény osztályok, amelyek ismerős lesz, hogy bárki, aki használta a C# gyűjtemények. Hagyományosan a szolgáltatások külső rendszereket szükséges a megbízható állapotkezeléshez. A Megbízható gyűjtemények segítségével ugyanolyan magas rendelkezésre állással és megbízhatósággal tárolhatja az állapotát a számításai mellett, mint a magas rendelkezésre állású külső áruházaktól. Ez a modell is javítja a késést, mert közösen helyt helyez el a számítási és állapot, amelyet működnie kell.

## <a name="what-makes-reliable-services-different"></a>Mi teszi a megbízható szolgáltatásokat mássá?

A megbízható szolgáltatások eltérnek a korábban írt szolgáltatásoktól, mivel a Service Fabric a következőket biztosítja:

* **Megbízhatóság** – A szolgáltatás még megbízhatatlan környezetekben is fenn marad, ahol a gépek meghibásodnak vagy hálózati problémákat észlelnek, vagy olyan esetekben, amikor maguk a szolgáltatások hibákat és összeomlást tapasztalnak. Állapotalapú szolgáltatások esetén az állapot hálózati vagy egyéb hibák esetén is megmarad.
* **Elérhetőség** - A szolgáltatás elérhető és reszponzív. A Service Fabric fenntartja a kívánt számú futó példányok.
* **Méretezhetőség** – A szolgáltatások levannak választva az adott hardverről, és szükség szerint növekedhetnek vagy zsugorodhatnak a hardver ek vagy más erőforrások hozzáadásával vagy eltávolításával. A szolgáltatások könnyen particionálhatók (különösen az állapotalapú esetben), hogy a szolgáltatás képes legyen a részleges hibák méretezésére és kezelésére. A szolgáltatások dinamikusan hozhatók létre és törölhetők a kódon keresztül, így szükség szerint több példány is megpördülhet, például az ügyfelek kéréseire válaszul. Végül a Service Fabric arra ösztönzi a szolgáltatásokat, hogy könnyű. A Service Fabric lehetővé teszi, hogy szolgáltatások ezrei legyenek egyetlen folyamaton belül kiépítve, ahelyett, hogy teljes operációsrendszer-példányokat vagy folyamatokat igényelne vagy szentelne egy szolgáltatás egyetlen példányának.
* **Konzisztencia** – A megbízható szolgáltatásban tárolt minden információ garantáltan konzisztens. Ez még több megbízható gyűjteményre is igaz egy szolgáltatáson belül. A szolgáltatáson belüli gyűjtemények közötti módosítások tranzakciós atomi módon hajthatók végre.

## <a name="service-lifecycle"></a>A szolgáltatás életciklusa

Függetlenül attól, hogy a szolgáltatás állapotalapú vagy állapotmentes, a Megbízható szolgáltatások egyszerű életciklust biztosítanak, amely lehetővé teszi a kód gyors csatlakoztatását és az induláshoz.  Az új szolgáltatás működéséhez két módszer szükséges:

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** – Ez a módszer határozza meg a használni kívánt kommunikációs verem(eke)t. A kommunikációs verem, például [a webes API,](service-fabric-reliable-services-communication-webapi.md)az, ami meghatározza a szolgáltatás figyelési végpontját vagy végpontjait (hogyan érik el az ügyfelek a szolgáltatást). Azt is meghatározza, hogy a megjelenő üzenetek hogyan működnek együtt a szolgáltatáskód többi részével.
* **RunAsync** – Ez a módszer, ahol a szolgáltatás fut az üzleti logika, és ahol elindítja a háttérfeladatokat, amelyek a szolgáltatás élettartama alatt kell futnia. A megadott törlési jogkivonat jelzi, hogy a munka mikor nak kell leállnia. Ha például a szolgáltatásnak ki kell húznia az üzeneteket egy megbízható várólistából, és fel kell dolgoznia őket, ez az a hely, ahol a munka történik.

Ha először tanul szanamár megbízható szolgáltatásokról, olvasson tovább! Ha a megbízható szolgáltatások életciklusának részletes áttekintését szeretné kapni, olvassa el a [Megbízható szolgáltatások életciklusának áttekintése című témakört.](service-fabric-reliable-services-lifecycle.md)

## <a name="example-services"></a>Példa szolgáltatások

Vessünk egy közelebbi pillantást, hogyan működik a Reliable Services modell állapot nélküli és állapotalapú szolgáltatások.

### <a name="stateless-reliable-services"></a>Állapotnélküli megbízható szolgáltatások

Az *állapotmentes szolgáltatás* olyan szolgáltatás, ahol nincs állapot a szolgáltatáson belül a hívások között. Minden olyan állapot, amely jelen van, teljesen eldobható, és nem igényel szinkronizálást, replikációt, adatmegőrzést vagy magas rendelkezésre állást.

Vegyünk például egy számológépet, amelynek nincs memóriája, és minden feltételt és műveletet egyszerre kell végrehajtania.

Ebben az esetben `RunAsync()` a szolgáltatás `runAsync()` (C#) vagy (Java) üres lehet, mivel nincs háttérfeladat-feldolgozás, amelyet a szolgáltatásnak meg kell tennie. A számológép szolgáltatás létrehozásakor `ICommunicationListener` egy (C#) vagy `CommunicationListener` (Java) (például webes [API)](service-fabric-reliable-services-communication-webapi.md)értéket ad vissza, amely egy porton megnyit egy figyelési végpontot. Ez a figyelővégpont a számológép nyilvános API-ját meghatározó különböző számítási módszerekhez (például: "Add(n1, n2)") csatlakoztatja.

Amikor egy ügyfél hívása történik, a rendszer meghívja a megfelelő metódust, és a számológép-szolgáltatás végrehajtja a műveleteket a megadott adatokon, és visszaadja az eredményt. Nem tárol semmilyen államot.

Nem tárolja a belső állapot teszi ezt a példát számológép egyszerű. De a legtöbb szolgáltatás nem igazán államnélküli. Ehelyett az állapotukat egy másik üzletbe delegálják. (Például minden olyan webalkalmazás, amely a munkamenetállapot nak egy háttértárolóban vagy gyorsítótárban való megőrzésére támaszkodik, nem állapotnélküli.)

Egy gyakori példa arra, hogy az állapotmentes szolgáltatások a Service Fabric egy előtér,, amely egy webalkalmazás nyilvános API-t tesz elérhetővé. Az előtér-szolgáltatás ezután lekéri az állapotalapú szolgáltatásokat a felhasználói kérelem teljesítéséhez. Ebben az esetben az ügyfelek től érkező hívások egy ismert portra ( például 80) irányulnak, ahol a stateless szolgáltatás figyel. Ez az állapotmentes szolgáltatás fogadja a hívást, és meghatározza, hogy a hívás egy megbízható féltől származik-e, és hogy melyik szolgáltatásra rendeltetett.  Ezután az állapotmentes szolgáltatás továbbítja a hívást az állapotalapú szolgáltatás megfelelő partíciójára, és várja a választ. Amikor az állapotmentes szolgáltatás választ kap, válaszol az eredeti ügyfélnek. Egy ilyen szolgáltatás egy ilyen szolgáltatás a *Service Fabric első lépések* minta ([C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)), többek között a Service Fabric-mintákat, hogy a tárházban.

### <a name="stateful-reliable-services"></a>Állapotalapú megbízható szolgáltatások

Az *állapotalapú szolgáltatás* az, amelynek az állapot bizonyos részét konzisztensnek kell tartania, és jelen kell lennie ahhoz, hogy a szolgáltatás működjön. Fontolja meg egy olyan szolgáltatás, amely folyamatosan kiszámítja a gördülő átlag a frissítések et kap. Ehhez rendelkeznie kell az aktuális bejövő kérelmek feldolgozásához szükséges, és az aktuális átlag. Minden olyan szolgáltatás, amely lekéri, feldolgozza és tárolja az adatokat egy külső tárolóban (például egy Azure blob vagy táblatároló ma) állapotalapú. Csak a külső állapotban tartja az állapotát.

A legtöbb szolgáltatás ma tárolja az állapotkülső, mivel a külső tároló, amely megbízhatóságot, rendelkezésre állást, méretezhetőséget és konzisztenciát az adott állapot. A Service Fabric szolgáltatás nem szükséges az állapot külső tárolása. A Service Fabric gondoskodik ezekről a követelményekről a szolgáltatáskód és a szolgáltatás állapota.

Tegyük fel, hogy olyan szolgáltatást szeretnénk írni, amely feldolgozza a képeket. Ehhez a szolgáltatás egy lemezképet és az adott képen végrehajtandó konverziók sorozatát veszi fel. Ez a szolgáltatás egy kommunikációs figyelőt ad vissza (tegyük fel, `ConvertImage(Image i, IList<Conversion> conversions)`hogy ez egy WebAPI), amely egy API-t tesz elérhetővé. Amikor egy kérést kap, a szolgáltatás `IReliableQueue`tárolja azt a , és visszaad egy azonosítót az ügyfélnek, így nyomon követheti a kérelmet.

Ebben a `RunAsync()` szolgáltatásban, lehetne bonyolultabb. A szolgáltatás egy hurok `RunAsync()` belsejében, amely `IReliableQueue` lekéri a kérelmeket, és végrehajtja a kért konverziók. Az eredmények et tárolnak egy `IReliableDictionary` úgy, hogy amikor az ügyfél visszatér, megkaphassák a konvertált képeket. Annak érdekében, hogy még ha valami nem sikerül a rendszerkép nem vész el, ez a megbízható szolgáltatás húzza ki a várólistából, hajtsa végre a konverziókat, és tárolja az eredményt egyetlen tranzakcióban. Ebben az esetben az üzenet törlődik a várólistából, és az eredmények csak akkor tárolódnak az eredményszótárban, ha a konverziók befejeződtek. Másik lehetőségként a szolgáltatás kihúzhatja a lemezképet a várólistából, és azonnal tárolhatja egy távoli tárolóban. Ez csökkenti a szolgáltatás által kezelni kell a szolgáltatás által kezelhető állapotot, de növeli az összetettséget, mivel a szolgáltatásnak meg kell őriznie a távoli tároló kezeléséhez szükséges metaadatokat. Mindkét megközelítés, ha valami nem sikerült a közepén a kérelem marad a várólistában vár a feldolgozásra vár.

Bár ez a szolgáltatás úgy hangzik, mint egy tipikus .NET`IReliableQueue` `IReliableDictionary`szolgáltatás, a különbség az, hogy a használt adatstruktúrákat ( és ) a Service Fabric biztosítja, és rendkívül megbízhatóak, elérhetők és konzisztensek.

## <a name="when-to-use-reliable-services-apis"></a>Mikor érdemes használni a Reliable Services API-kat?

Fontolja meg a megbízható szolgáltatások API-k, ha:

* Azt szeretné, hogy a szolgáltatás kódja (és opcionálisan állapotban) magas rendelkezésre állású és megbízható legyen.
* Tranzakciós garanciákra van szükség több államegység (például rendelések és rendelési sorok) között.
* Az alkalmazás állapota természetesen modellezhető megbízható szótárak és várólisták.
* Az alkalmazások kódjának vagy állapotának magas rendelkezésre állásúnak kell lennie alacsony késleltetésű olvasási és írási adatokkal.
* Az alkalmazásnak egy vagy több megbízható gyűjteményben kell szabályoznia a tranzakciós műveletek egyidejűségét vagy részletességét.
* Szeretné kezelni a kommunikációt, vagy a szolgáltatás particionálási sémát.
* A kódnak szabad szálas futásidejű környezetre van szüksége.
* Az alkalmazásnak dinamikusan létre kell hoznia vagy meg kell semmisítenie a megbízható szótárakat vagy várólistákat vagy a teljes szolgáltatásokat futásidőben.
* Programozott módon kell vezérelnie a Service Fabric által biztosított biztonsági mentési és visszaállítási funkciókat a szolgáltatás állapotában.
* Az alkalmazásnak meg kell őriznie az állapotegységek változási előzményeit.
* Külső fejlesztésű, egyéni állapotszolgáltatókat szeretne fejleszteni vagy felhasználni.

## <a name="next-steps"></a>További lépések

* [Megbízható szolgáltatások rövid útmutató](service-fabric-reliable-services-quick-start.md)
* [Megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md)
* [A Megbízható Szereplők programozási modell](service-fabric-reliable-actors-introduction.md)
