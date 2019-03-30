---
title: Az Azure Service Fabric állapotalapú szolgáltatások egységtesztelés |} A Microsoft Docs
description: Ismerje meg a fogalmakkal és módszerekkel tesztelése a Service Fabric Stateful Services egység.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: ca473b9947a9b0df610a9c3dac66914b06cc9217
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662567"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>A Service Fabric állapotalapú szolgáltatások egységtesztelés

Ez a cikk ismerteti a fogalmakkal és módszerekkel tesztelése a Service Fabric Stateful Services egység. A Service Fabric belül egységtesztelés sajátosak a szempontjai hozhatja létre az oka, hogy az az alkalmazás kódja aktívan fut a több különböző környezetekben. Ez a cikk az alkalmazáskód hatálya alá tartozó minden Futtatás a különböző környezetek biztosításához használt eljárásokat ismerteti.

## <a name="unit-testing-and-mocking"></a>Tesztelés és a szimulálás egység
Ez a cikk kontextusában egységtesztelés automatizált tesztelés a tesztfuttató például MSTest vagy NUnit keretén belül végrehajtható. Ebben a cikkben az egységteszteket nem végeznek műveleteket végeznek egy távoli erőforrás, például egy adatbázis vagy a RESTFul API-t. Távoli erőforrásokon mocked kell lennie. Ez a cikk kontextusában utánzási hamis, rekord, és szabályozhatja a távoli erőforrások által visszaadott értékeket.

### <a name="service-fabric-considerations"></a>A Service Fabric szempontok
Egységtesztelés egy Service Fabric állapotalapú szolgáltatás több szempontok rendelkezik. Először is a webszolgáltatás kódjához végrehajtja a több csomóponton, de különböző szerepkörök. Egységtesztek ki kell értékelni a kód teljes lefedettség érdekében minden egyes szerepkör alatt. A különféle szerepkörök elsődleges, másodlagos aktív, tétlen másodlagos és ismeretlen lenne. A nincs szerepkör nem általában kell semmilyen speciális lefedettség, a Service Fabric is figyelembe veszi ezt a szerepkört a szolgáltatás érvénytelen vagy null értékű lehet. Másodszor minden egyes csomópont megváltoztatja az adott szerepkör. Teljes lefedettség érdekében, kód végrehajtási útvonalat kell tesztelni előforduló szerepkör módosítása.

## <a name="why-unit-test-stateful-services"></a>Miért állapotalapú szolgáltatások vetünk? 
Fedje fel néhány gyakori hibák fordulnak elő, akkor nem feltétlenül kell érint a hagyományos alkalmazás vagy a tartomány-specifikus egységtesztelés egységtesztelés állapotalapú szolgáltatások segítségével. Például ha az állapotalapú szolgáltatással memórián belüli munkalehetőségek, az ilyen típusú tesztelés ellenőrizheti, hogy a memórián belüli állapota minden replika között szinkronban tartani. Az ilyen típusú tesztelés is ellenőrizheti, hogy az állapotalapú szolgáltatások válaszoljon a megszakítási tokeneket által átadott a Service Fabric vezénylési megfelelően. Lemondás esetén, ha a szolgáltatás mennyi ideig fut, illetve aszinkron műveleteket kell halt.  

## <a name="common-practices"></a>Gyakori eljárások

Az alábbi szakasz tanácsolja egységtesztelés az állapotalapú szolgáltatások esetében a leggyakoribb eljárásait. Tanácsolja is mi egy utánzási réteg kell rendelkeznie a Service Fabric vezénylési és állapot-felügyeleti szorosan igazodva. Utánzási kódtárak léteznek-kódtárak együttese ezt a funkciót. [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) kezdődően 3.3.0 vagy újabb verzió van egy ilyen kódtár, amely az ajánlott utánzási funkciókat biztosít, és az alább ismertetett eljárásokat követi.

### <a name="arrangement"></a>Elrendezés

#### <a name="use-multiple-service-instances"></a>Szolgáltatás több példány üzemeltetéséhez
Egységtesztek végre kell hajtani egy állapotalapú szolgáltatás több példánya. Ez szimulálja, valójában mi a fürtön, ahol a Service Fabric látja el a szolgáltatást futtató másik csomóponton több replika. Minden esetben fogja végrehajtani egy másik környezetben azonban. A teszt futtatásakor minden példány kell felkészül a fürtön a várt szerepkör-konfigurációval. Például ha a szolgáltatás várhatóan 3 cílová velikost repliky rendelkezik, a Service Fabric vennének igénybe három replika készül különböző csomópontokon. Ilyen például folyamatban van az elsődleges és a másik kettőt aktív másodlagos a folyamatban.

A legtöbb esetben a szolgáltatás végrehajtási útvonalat némileg az egyes ezek a szerepkörök függ. Például a szolgáltatás nem kell egy aktív másodlagos kéréseket fogad, ha a szolgáltatás ellenőrzi a állíthatunk munkába vissza, amely azt jelzi, hogy egy kérés informatív kivétel történt kísérlet egy másodlagos ebben az esetben előfordulhat, hogy rendelkezik. Több példánnyal rendelkező lehetővé teszi a vizsgálandó ebben a helyzetben.

Emellett több példánnyal rendelkező lehetővé teszi a vizsgálatok váltáshoz minden esetben ellenőrizze annak ellenére, hogy a szerepkör-módosításokat összhangban-e a válaszokat a szerepköröket.

#### <a name="mock-the-state-manager"></a>Az állapot manager utánzása
A State Manager kell kezelni, mint egy távoli erőforrás és ezért utánzott. Ha az állapot manager utánzási, kell lennie nyomon követésére, mi a állapot-kezelőhöz, mentett, olvassa el és ellenőrzése néhány alapul szolgáló memórián belüli tároló. Ennek elérése egy egyszerű módja, hogy minden a Reliable Collections típusú utánzatként funkcionáló példányának létrehozása. Ezek mocks belül adattípusú, amely szorosan illeszkedjen az adott gyűjteményen végrehajtott műveletek használata. Az alábbiakban néhány javasolt adattípusok, az egyes megbízható gyűjtemények

- IReliableDictionary < TKey, TValue >-< TKey, TValue > System.Collections.Concurrent.ConcurrentDictionary >
- IReliableQueue<T> -> System.Collections.Generic.Queue<T>
- IReliableConcurrentQueue<T> -> System.Collections.Concurrent.ConcurrentQueue<T>

#### <a name="many-state-manager-instances-single-storage"></a>Sok állapot Manager példányok, egyetlen tárolási
Ahogy korábban említettük, a State Manager és a Reliable Collections távoli erőforrásként kell kezelni. Ezért ezeket az erőforrásokat kell, és az egységteszteket belül fog kell mocked. Azonban amikor fut egy állapotalapú szolgáltatás több példánya lesz kihívást egyes utánzott állapotkezelője szinkronban tartásához különböző állapot-nyilvántartó szolgáltatási példányai között. Az állapotalapú szolgáltatás fut a fürtön, amikor a Service Fabric gondoskodik, hogy az összes másodlagos replikát állapotkezelője az elsődleges replika konzisztens. Ezért a tesztek kell úgy viselkedik, hogy azok szimulálhatja a szerepkör-módosításokat.

A szinkronizálás érhető el, egy egyszerű módja, hogy egy singleton-mintát az alapul szolgáló objektum, amely tárolja az adatokat írni az egyes megbízható gyűjtemények. Ha például egy állapotalapú szolgáltatás használ egy `IReliableDictionary<string, string>`. A utánzatként funkcionáló állapotkezelője kell visszaadnia, egy helyettem `IReliableDictionary<string, string>`. Felhasználhatja az adott helyettem egy `ConcurrentDictionary<string, string>` a kulcs/érték párok írt nyomon követéséhez. A `ConcurrentDictionary<string, string>` kell lennie az állapot-kezelők összes példánya által használt egypéldányos át a szolgáltatásnak.

#### <a name="keep-track-of-cancellation-tokens"></a>Nyomon követheti, megszakítási tokeneket
Megszakítási tokeneket olyan, egy fontos még gyakran kihagyott állapotalapú szolgáltatások része. Indulásakor a Service Fabric állapotalapú szolgáltatás egy elsődleges másodpéldány, akkor a megszakítás jogkivonat van megadva. Ez a megszakítás token célja, hogy jelezze a szolgáltatáshoz, eltávolítva, vagy egy másik szerepkör visszalép. Az állapotalapú szolgáltatásból kell állítsa le a hosszan tartó vagy aszinkron műveleteket, hogy a Service Fabric befejezheti a szerepkör megváltoztatása munkafolyamat.

Tesztek futtatása a egység, bármely megszakítási tokeneket RunAsync, amelyet ChangeRoleAsync, a OpenAsync CloseAsync és kell maradnia a teszt végrehajtása során. Ezek a jogkivonatok alakzatot üzem lehetővé teszi, hogy a szolgáltatás leállítása vagy a lefokozási szimulálhat, és megfelelően ellenőrizze a szolgáltatás reagál a teszt.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Test-végpontok utánzott távoli erőforrásokkal
Egységtesztek végre kell hajtani, annyi az alkalmazáskódot, módosíthatja az állapotalapú szolgáltatásból, a lehető állapotát. Javasoljuk, hogy a vizsgálatok lehet több – teljes körű jellegűek. A létező csak mocks jegyezze fel, szimulálhat, és/vagy ellenőrizze a távoli erőforrás interakciók. Ez magában foglalja a State Manager és a Reliable Collections folytatott interakciót. A következő kódrészlet azt mutatja be, teljes körű tesztelés teszteléséhez gherkin példája:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Ez a vizsgálat használjon esetleg imperatív állításokat, hogy az adatok egy replikát captured esetén érhető el egy másodlagos replikára elsődleges fejlécekké. Feltételezve, hogy egy megbízható gyűjteményben az alkalmazotti adatokat a háttértárban, Aa lehetséges hiba és a teszt sikerült kifogó, ha az alkalmazás kódja nem lehetett végrehajtani `CommitAsync` a tranzakcióban, mentse az újonnan felvettek betanítása. Ebben az esetben a második kérés beolvasni az alkalmazottak nem termékazonosítóhoz alkalmazott hozzáadta az első kérésre.

### <a name="acting"></a>Szerep
#### <a name="mimic-service-fabric-replica-orchestration"></a>A Service Fabric replika vezénylési utánzására
Több szolgáltatáspéldány kezelésekor a tesztek kell inicializálni vagy üzemen kívül ezeket a szolgáltatásokat a Service Fabric vezénylési azonos módon. Ha például egy szolgáltatás létrehozásakor egy új elsődleges replikán található, a Service Fabric meghívja CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync és RunAsync. Az életciklus-események vannak dokumentálva az alábbi cikkeket:

- [Állapotalapú szolgáltatás indítása](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Állapotalapú szolgáltatás leállítása](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Állapotalapú szolgáltatás elsődleges cseréje](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Futtassa a replika szerepkörök változása
Az egységteszteket a Service Fabric vezénylési azonos módon módosítani kell a szerepkörök szolgáltatás-példánya. A szerepkör állapotú gép leírása itt található a következő cikket:

[Replika szerepkör állapotú gép](service-fabric-concepts-replica-lifecycle.md#replica-role)

Szerepkörök változása szimuláló tesztelés kritikus fontosságú aspektusainak-problémákat, ha nem állnak egymással konzisztenssé a replikát állapot részletes elemzést. Inkonzisztens replika tárolása a memóriában lévő statikus vagy osztály szolgáltatásiszint-példány változók miatt fordulhat elő. Erre vonatkozó példákat megszakítási tokeneket, enumerálások és konfigurációs objektumok/érték lehet. Ez biztosítja, hogy a szolgáltatás van tiszteletben tartja a megszakítási tokeneket RunAsync, hogy megtörténjen a szerepkör módosítása során. Szerepkör-módosításokat szimuláló is tárhat fel, ha a kód, nem készül, hogy a hívás RunAsync, több alkalommal esetleg felmerülő problémákat.

#### <a name="cancel-cancellation-tokens"></a>Megszakítási tokeneket megszakítása
Amennyiben a megszakítás jogkivonat RunAsync meg lett szakítva egységteszteket kell létezik. Ez lehetővé teszi, hogy ellenőrizze, hogy a szolgáltatás szabályosan leállítja a teszt. A hosszan tartó vagy aszinkron műveleteket leállítás során le kell állítani. Például előfordulhat, hogy létezik egy-egy hosszú ideig futó folyamat, amelyik egy megbízható üzenetsor üzeneteit figyeli. Ez előfordulhat, hogy létezik közvetlenül belül RunAsync, illetve egy háttérszálból történik. A megvalósítás tartalmaznia kell logika kilép a műveletet, ha ez a megszakítás token meg lett szakítva.

Ha az állapotalapú szolgáltatások, amelyek csak léteznie kell az elsődleges vagy a memórián belüli gyorsítótár állam, azt most kell értékesíteni. Ez a annak érdekében, hogy egységes, ha a csomópont újra lesz egy elsődleges ebben az állapotban. Megszakítás tesztelés lehetővé teszi a teszt ellenőrzése ebben az állapotban megfelelően fel van szabadítva.

#### <a name="execute-requests-against-multiple-replicas"></a>Több replika irányuló kérések végrehajtásához.
Vyhodnocení tesztek a másik replika azonos kérelemben kell végrehajtani. Szerepkör módosítása-párosítva konzisztenciabeli problémákat ne maradjon lefedetlen felelősségi is lehet. Egy példa tesztelési előfordulhat, hogy hajtsa végre az alábbi lépéseket:
1. Az aktuális elsődleges szembeni írási kérelmek
2. Hajtsa végre egy olvasási kérést, amely visszaadja az aktuális elsődleges elleni 1. lépésben írt adatok
3. Léptesse elő elsődleges, másodlagos. Ez is kell fokoznia az aktuális elsődleges, másodlagos
4. Hajtsa végre az azonos olvasási kérést szemben az új másodlagos 2. lépés.

A legutóbbi lépésben a teszt támaszthat összhangban az adatok lekérendők. Egy potenciális problémát, hogy ez sikerült tárhat fel, lehet, hogy az adatokat a szolgáltatás által visszaadott memóriát, de végső soron biztonsági szerint egy megbízható gyűjteményben. Memóriában lévő adatok nem lehetséges, hogy szinkronban megfelelően mi szerepel a megbízható gyűjteményben tartható.

Memóriában lévő adatok általában segítségével hozzon létre másodlagos indexek vagy összesítéseket az adatok, hogy létezik egy megbízható gyűjteményben.

### <a name="asserting"></a>Amely azt mutatja
#### <a name="ensure-responses-match-across-replicas"></a>Győződjön meg arról, válaszok replikák telepített verziója
Egységtesztek vyhodnocení kell, hogy egy adott kéréshez tartozó válasz esetén egységes több replika azok elsődleges áttérés után. Ez a potenciális problémákat, ahol a válaszban megadott adatok nem egy megbízható gyűjteményben által támogatott, vagy a memóriában tartott merülhet nélkül egy mechanizmust, amely az adatokat szinkronizálja a replikákat között. Ez biztosítja, hogy a szolgáltatás küld vissza konzisztens válaszok után a Service Fabric rebalances, vagy egy új elsődleges replika feladatait.

#### <a name="verify-service-respects-cancellation"></a>Ellenőrizze a szolgáltatás ügyel megszakítás
Hosszan futó vagy aszinkron folyamat, meg kell szüntetni, amikor megszakítás jogkivonatot meg lett szakítva ellenőrizni kell, hogy azok ténylegesen megszakadt lemondás után. Ez biztosítja, hogy annak ellenére, hogy a replika szerepkört változtat, folyamatokat, amelyek nem alkalmasak a replika nem elsődleges futtatásának folytatásához le az átmenet befejezése előtt. Ez is részletes elemzést problémák, ahol egy ilyen folyamatot letiltja a Service Fabric szerepkör módosítása vagy -leállítás kérése befejeződését.

#### <a name="verify-which-replicas-should-serve-requests"></a>Ellenőrizze, hogy melyik replika kell szolgálnia kérelmek
A tesztek kell vyhodnocení a normális működés, ha egy nem elsődleges replikára irányítja a a kérelmeket. A Service Fabric lehetővé teszi, hogy rendelkezik a másodlagos replikák kiszolgálni a kérelmeket. Azonban a reliable collections írási műveletek csak akkor fordulhat elő az elsődleges replikából. Ha az alkalmazás sporteseményt csak elsődleges replikák kiszolgálni a kérelmeket, vagy a kérelmek csak egy része egy másodlagos tudja kezelni, a tesztek a várt működése a pozitív és negatív esetekben kell vyhodnocení. Folyamatban van egy kérelmet a negatív eset irányítja a rendszer nem kezeli a kérelmet, és a pozitív replika ennek az ellenkezője folyamatban van.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [egység teszt állapotalapú szolgáltatások](service-fabric-how-to-unit-test-stateful-services.md).