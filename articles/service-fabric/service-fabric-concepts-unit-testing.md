---
title: Állapotalapú szolgáltatások tesztelése az Azure Service Fabricben
description: Ismerje meg a fogalmakat és gyakorlatokat az egység tesztelése Service Fabric állapotalapú szolgáltatások.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 12e8a47d9685dee12594f4e2afaa848d9688d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433915"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Állapotalapú szolgáltatások tesztelése a Service Fabricben

Ez a cikk ismerteti a fogalmak és gyakorlatok az egység tesztelése Service Fabric állapotalapú szolgáltatások. A Service Fabric-en belüli egységtesztelés saját szempontokat érdemel, mivel az alkalmazáskód aktívan fut több különböző környezetben. Ez a cikk ismerteti a használt eljárások biztosítása alkalmazáskód a különböző környezetek futtatható tartozó.

## <a name="unit-testing-and-mocking"></a>Egységtesztelés és gúnyolódás
Az egységtesztelés e cikk kontextusában olyan automatizált tesztelés, amely egy tesztfutó, például az MSTest vagy az NUnit környezetében hajtható végre. Az ebben a cikkben található egységtesztek nem hajtanak végre műveleteket egy távoli erőforrás, például egy adatbázis vagy restful API ellen. Ezeket a távoli erőforrásokat ki kell gúnyolni. A cikk kontextusában a gúnyolódás hamis, rekord és vezérlő a távoli erőforrások visszatérési értékeit.

### <a name="service-fabric-considerations"></a>A Service Fabric szempontjai
A Service Fabric állapotalapú szolgáltatás egy adott állapotú szolgáltatás tesztelése számos szempontot. Először is, a szolgáltatáskód végrehajtása több csomóponton, de különböző szerepkörök alatt. Az egységteszteknek ki kell értékelniük az egyes szerepkörek szerinti kódot a teljes lefedettség elérése érdekében. A különböző szerepkörök az elsődleges, az aktív másodlagos, az alapjárati másodlagos és az ismeretlen szerepkör ök. A Nincs szerepkör általában nem igényel speciális lefedettséget, mivel a Service Fabric ezt a szerepkört érvénytelennek vagy null szolgáltatásnak tekinti. Másodszor, minden csomópont megváltoztatja a szerepét egy adott ponton. A teljes lefedettség elérése érdekében a kódvégrehajtási útvonalat a szerepkör-változások kal kell tesztelni.

## <a name="why-unit-test-stateful-services"></a>Miért érdemes az egységteszt állapotalapú szolgáltatásait? 
Az állapotalapú szolgáltatások egységtesztelése segíthet feltárni néhány olyan gyakori hibát, amelyek et nem feltétlenül a hagyományos alkalmazás vagy a tartományspecifikus egységtesztelés fog. Ha például az állapotalapú szolgáltatás nak van-e memóriabeli állapota, az ilyen típusú tesztelés ellenőrizheti, hogy ez a memórián belüli állapot szinkronban van-e az egyes replika között. Az ilyen típusú tesztelés is ellenőrizheti, hogy egy állapotalapú szolgáltatás megfelelően reagál-e a Service Fabric vezénylési által átadott megszakítási jogkivonatokra. A lemondások aktiválásakor a szolgáltatásnak le kell állítania minden hosszú futást és/vagy aszinkron műveletet.  

## <a name="common-practices"></a>Közös gyakorlat

A következő szakasz tanácsot ad az állapotalapú szolgáltatás egységtesztelésének leggyakoribb gyakorlatával kapcsolatban. Azt is javasolja, hogy mi a gúnyos réteg kell szorosan igazodnia a Service Fabric vezénylési és állapotkezelés. [ServiceFabric.Mocks,](https://www.nuget.org/packages/ServiceFabric.Mocks/) mint a 3.3.0 vagy újabb egy ilyen könyvtár, amely biztosítja a gúnyos funkció ajánlott, és követi az alábbi eljárásokat.

### <a name="arrangement"></a>Elrendezése

#### <a name="use-multiple-service-instances"></a>Több szolgáltatáspéldány használata
Az egységteszteknek egy állapotalapú szolgáltatás több példányát kell végrehajtaniuk. Ez szimulálja, hogy ténylegesen mi történik a fürtön, ahol a Service Fabric a szolgáltatás különböző csomópontokon futó több replikákat tartalmaz. Ezen példányok mindegyike azonban más-más környezetben lesz végrehajtva. A teszt futtatásakor minden példányt a fürtön várt szerepkör-konfigurációval kell alapba venni. Például ha a szolgáltatás várhatóan 3-as célreplika mérete, a Service Fabric három replikák különböző csomópontokon. Az egyik az elsődleges, a másik kettő pedig az Aktív Középfokú.

A legtöbb esetben a szolgáltatás végrehajtási útvonal a kismértékben változik az egyes szerepkörök. Ha például a szolgáltatás nem fogadja el az aktív másodlagos tól érkező kéréseket, előfordulhat, hogy a szolgáltatás ellenőrzi ezt az esetet, hogy visszadobjon egy tájékoztató kivételt, amely azt jelzi, hogy egy másodlagos kérést kíséreltek meg. Több példány esetén ez a helyzet tesztelése lehetővé teszi.

Emellett több példány lehetővé teszi, hogy a tesztek váltsa nak a szerepkörök szerepkörei, hogy ellenőrizze a válaszok konzisztensek ellenére a szerepkör-változások.

#### <a name="mock-the-state-manager"></a>Gúnyolódni az államvezetővel
Az állapotkezelőt távoli erőforrásként kell kezelni, ezért gúnyolni kell. Ha kigúnyolja az állapotkezelőt, a memórián belüli tárolónak kell lennie ahhoz, hogy nyomon követhesse, mi menthető az állapotkezelőbe, hogy olvasható és ellenőrizhető legyen. Ennek eléréséhez egyszerű, hogy hozzon létre modell példányok az egyes típusú megbízható gyűjtemények. Ezeken a gúnyolódásokon belül használjon olyan adattípust, amely szorosan igazodik a gyűjteményhez végrehajtott műveletekhez. Az alábbiakban néhány javasolt adattípust taszunk fel minden egyes megbízható

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue\<T> -> System.Collections.Generic.Queue\<T>
- IReliableConcurrentQueue\<T> -> System.Collections.Concurrent.ConcurrentQueue\<T>

#### <a name="many-state-manager-instances-single-storage"></a>Számos state manager-példány, egyetlen tároló
Mint már említettük, az állapotkezelő és a megbízható gyűjtemények kell kezelni, mint egy távoli erőforrás. Ezért ezeket az erőforrásokat az egységteszteken belül meg kell és kell kigúnyolni. Azonban egy állapotalapú szolgáltatás több példányának futtatásakor kihívást jelent az egyes kigúnyolt állapotkezelők szinkronizálása a különböző állapotalapú szolgáltatáspéldányok között. Ha az állapotalapú szolgáltatás fut a fürtön, a Service Fabric gondoskodik attól, hogy minden másodlagos replika állapotkezelő konzisztens az elsődleges replika. Ezért a teszteknek ugyanúgy kell viselkedniük, hogy szimulálhassák a szerepkör-módosításokat.

Egy egyszerű módja ennek a szinkronizálás érhető el, az, hogy egy singleton mintát az alapul szolgáló objektum, amely tárolja az egyes megbízható gyűjteményírási adatokat. Ha például egy állapotalapú szolgáltatás `IReliableDictionary<string, string>`a . A modell állami menedzser vissza `IReliableDictionary<string, string>`kell adnia egy ál -ból. Ez a mock `ConcurrentDictionary<string, string>` használhatja az a nyomon követni a kulcs / érték párok írt. A `ConcurrentDictionary<string, string>` kell egy singleton által használt összes példánya a szolgáltatásnak átadott állapotkezelők.

#### <a name="keep-track-of-cancellation-tokens"></a>A törlési tokenek nyomon követése
A törlési jogkivonatok az állapotalapú szolgáltatások fontos, de gyakran figyelmen kívül hagyott aspektusai. Amikor a Service Fabric elindítja az állapotalapú szolgáltatás elsődleges replikát, egy lemondási jogkivonat biztosított. Ez a törlési jogkivonat a szolgáltatás nak jelez, ha eltávolítják vagy lefokozzák egy másik szerepkörbe. Az állapotalapú szolgáltatás le kell állítania minden hosszú ideig futó vagy aszinkron műveleteket, hogy a Service Fabric befejezhesse a szerepkör-módosítási munkafolyamatot.

Az egységtesztek futtatásakor a RunAsync, A ChangeRoleAsync, az OpenAsync és a CloseAsync számára biztosított törlési jogkivonatokat a tesztvégrehajtása során kell tartani. Ezek a jogkivonatok megtartása lehetővé teszi, hogy a teszt szimulálja a szolgáltatás leállítását vagy lefokozását, és ellenőrizze, hogy a szolgáltatás megfelelően reagál-e.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Végpontok utáni tesztelés kigúnyolt távoli erőforrásokkal
Az egységteszteknek annyi alkalmazáskódot kell végrehajtaniuk, amely a lehető legnagyobb mértékben módosíthatja az állapotalapú szolgáltatás állapotát. Javasoljuk, hogy a tesztek több end-to-end jellegű. A létezési csak a távoli erőforrás-interakciók rögzítése, szimulálása és/vagy ellenőrzése. Ez magában foglalja az állapotkezelővel és a megbízható gyűjteményekkel folytatott interakciókat. A következő kódrészlet egy példa az usirta egy teszt, amely bemutatja a végpontok között végzett vizsgálat:

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

Ez a teszt azt állítja, hogy az egyik replika rögzített adatok elérhetők egy másodlagos replika, amikor előléptetik az elsődleges. Feltételezve, hogy egy megbízható gyűjtemény az alkalmazotti adatok háttértárolója, az Aa potenciális hiba, `CommitAsync` amely ezzel a teszttel elkapható, ha az alkalmazáskód nem hajtott végre a tranzakción az új alkalmazott mentéséhez. Ebben az esetben az alkalmazottak lekérésére irányuló második kérelem nem ad vissza az első kérésáltal hozzáadott alkalmazottat.

### <a name="acting"></a>Eljáró
#### <a name="mimic-service-fabric-replica-orchestration"></a>Mimiszta Service Fabric replika vezénylési
Több szolgáltatáspéldány kezelése esetén a tesztek inicializálni, és bontsa le ezeket a szolgáltatásokat ugyanúgy, mint a Service Fabric vezénylési. Ha például egy szolgáltatás jön létre egy új elsődleges replika, Service Fabric meghívja CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync és RunAsync. Az életciklus-eseményeket a következő cikkek dokumentálják:

- [Állapotalapú szolgáltatás indítása](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Állapotalapú szolgáltatás leállítása](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Állapotalapú szolgáltatás elsődleges csereügyletei](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Replikaszerepkör-módosítások futtatása
Az egységteszteknek a service-példányok szerepköreit ugyanúgy kell módosítaniuk, mint a Service Fabric vezénylési. A szerepkör állapotgép a következő cikkben van dokumentálva:

[Replika szerepkör állapotának számítógépe](service-fabric-concepts-replica-lifecycle.md#replica-role)

A szerepkör-módosítások szimulálása a tesztelés egyik kritikusabb aspektusa, és feltárhatja azokat a problémákat, amelyeknél a replika állapota nem egyezik meg egymással. Inkonzisztens replikaállapot a memórián belüli állapot statikus vagy osztályszintű példányváltozókban való tárolása miatt fordulhat elő. Ilyenek lehetnek például a érvénytelenítési jogkivonatok, felsorakat és konfigurációs objektumok/értékek. Ez azt is biztosítja, hogy a szolgáltatás tiszteletben tartja a RunAsync során biztosított megszakítási jogkivonatokat, hogy a szerepkör módosítása bekövetkezzen. A szerepkör-módosítások szimulálása olyan problémákat is feltárhat, amelyek akkor merülhetnek fel, ha a kód nincs megírva, hogy a RunAsync többszöris meghívást engedélyezzen.

#### <a name="cancel-cancellation-tokens"></a>Lemondási tokenek visszavonása
Léteznie kell olyan egységteszteknek, ahol a RunAsync számára biztosított megszakítási jogkivonat megszakad. Ez lehetővé teszi a teszt, hogy ellenőrizze, hogy a szolgáltatás szabályosan leáll. Ez alatt a leállítás során minden hosszú ideig futó vagy aszinkron műveleteket le kell állítani. Például egy szolgáltatáson esetleg létező hosszú ideig futó folyamat az, amely egy megbízható várólistán lévő üzeneteket figyel. Ez közvetlenül a RunAsync-en vagy egy háttérszálon belül is létezhet. A megvalósításnak tartalmaznia kell a műveletből való kilépés logikáját, ha a megszakítási jogkivonat ot megszakítja.

Ha az állapotalapú szolgáltatások olyan gyorsítótárat vagy memórián belüli állapotot használnak, amely csak az elsődleges en létezhet, akkor ebben az időben kell ártalmatlanítani. Ez biztosítja, hogy ez az állapot konzisztens legyen, ha a csomópont később ismét elsődlegesvé válik. A törlési vizsgálat lehetővé teszi a vizsgálat számára, hogy ellenőrizze, hogy az állapot megfelelően van-e ártalmatlanítva.

#### <a name="execute-requests-against-multiple-replicas"></a>Kérések végrehajtása több replikára
Az érvényesítési teszteknek ugyanazt a kérést kell végrehajtaniuk a különböző replika ellen. Ha a szerepkör-módosításokkal párosítva a konzisztenciaproblémák at fedezheti fel. Egy példateszt a következő lépéseket hajthatja végre:
1. Írási kérelem végrehajtása az aktuális elsődleges sel szemben
2. Az 1.
3. Másodlagos elsődlegeselőléptetés. Ennek le kell fokoznia az aktuális elsődleges és másodlagos
4. Hajtsa végre ugyanazt az olvasási kérelmet a 2.

Az utolsó lépésben a teszt azt érvényesítheti, hogy a visszaadott adatok konzisztensek. Egy lehetséges probléma, hogy ez feltárhatja, hogy a szolgáltatás által visszaadott adatok lehetnek a memóriában, de végül egy megbízható gyűjtemény által támogatott. Előfordulhat, hogy a memóriában lévő adatok nem lesznek megfelelően szinkronban a megbízható gyűjteményben található adatokkal.

A memórián belüli adatokat általában másodlagos indexek vagy megbízható gyűjteményben lévő adatok összesítésének létrehozására használják.

### <a name="asserting"></a>Azt állítja
#### <a name="ensure-responses-match-across-replicas"></a>A válaszok egyezésének biztosítása a replikák között
Az egységteszteknek azt kell állítaniuk, hogy egy adott kérelemre adott válasz konzisztens több replikák között az elsődlegesre való áttérés után. Ez felszínre potenciális problémák, ahol a válaszban megadott adatok vagy nem támogatja a megbízható gyűjtemény, vagy a memóriában tárolt mechanizmus nélkül, hogy az adatok replikák között. Ez biztosítja, hogy a szolgáltatás konzisztens válaszokat küld, miután a Service Fabric újraegyensúlyozza, vagy átadja a feladatát egy új elsődleges replika.

#### <a name="verify-service-respects-cancellation"></a>A szolgáltatás tiszteletben tartásának ellenőrzése
A hosszú ideig futó vagy aszinkron folyamatokat, amelyeket meg kell szüntetni, amikor egy megszakítási jogkivonatot megszakítanak, ellenőrizni kell, hogy azok a megszakítás után ténylegesen megszűntek-e. Ez biztosítja, hogy a replika változó szerepkörök, folyamatok, amelyek nem célja, hogy továbbra is fut a nem elsődleges replika leállítása, mielőtt az átmenet befejeződik. Ez is feltárhatja azokat a problémákat, ahol egy ilyen folyamat blokkolja a Service Fabric szerepkör-változási vagy leállítási kérelmét.

#### <a name="verify-which-replicas-should-serve-requests"></a>Annak ellenőrzése, hogy mely replikák szolgálják a kérelmeket
A teszteknek érvényesíteniük kell a várt viselkedést, ha egy kérelem nem elsődleges replikára van irányítva. A Service Fabric lehetővé teszi, hogy másodlagos replikák szolgálják a kérelmeket. Azonban a megbízható gyűjtemények írási adatok csak az elsődleges replika. Ha az alkalmazás célja csak az elsődleges replikák a kérelmek kiszolgálására, vagy csak egy részhalmaza a kérelmek kezelhető egy másodlagos, majd a tesztek érvényesítenie kell a várt viselkedést mind a pozitív és negatív esetekben. A negatív eset, hogy egy kérelem van irányítva egy replika, amely nem kezeli a kérelmet, és a pozitív, hogy az ellenkezője.

## <a name="next-steps"></a>További lépések
További információ az [állapotalapú szolgáltatások egységes teszteléséről.](service-fabric-how-to-unit-test-stateful-services.md)
