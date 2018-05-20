---
title: Az Azure Service Fabric architektúrája |} Microsoft Docs
description: A Service Fabric egy elosztottrendszer platform, méretezhető, megbízható, és könnyen által felügyelt alkalmazások a felhő létrehozásához használt. Ez a cikk a Service Fabric architektúráját mutatja be.
services: service-fabric
documentationcenter: .net
author: rishirsinha
manager: timlt
editor: rishirsinha
ms.assetid: 6b554243-70cb-4c22-9b28-1a8b4703f45e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/12/2017
ms.author: rsinha
ms.openlocfilehash: 5e69d4b09261c90fd3c33e60645fe484b816e369
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-architecture"></a>A Service Fabric-architektúra
A Service Fabric-t a réteges alrendszereket. Alrendszereket lehetővé teszik a alkalmazások, amelyek írni:

* Magas rendelkezésre állású
* Méretezhető
* Kezelhető
* Testable

Az alábbi ábrán látható, a Service Fabric a fő alrendszerei.

![A Service Fabric-architektúra ábrája](media/service-fabric-architecture/service-fabric-architecture.png)

Elosztott rendszer esetén a fürt csomópontjai közötti biztonságos kommunikációhoz lehessen elengedhetetlen. A halom alján az átviteli alrendszer, amely biztosít a biztonságos kommunikáció a csomópontok között van. Az átvitel fent alrendszer értékű az összevonási alrendszer, amely a különböző csomópontokon clusters (fürtök nevű) egyetlen entitás az, hogy a Service Fabric észlelésére, hajtsa végre a vezető választás és egységes útválasztást. A megbízhatóság alrendszer, az összevonási alrendszer rétegre felelős a Service Fabric szolgáltatások, például a replikáció, az erőforrás-kezelés és a feladatátvételi mechanizmusokon keresztül megbízhatóságát. Az összevonási alrendszer is alapjául szolgáló az üzemeltető és aktiválási alrendszer, amely egycsomópontos az alkalmazások életciklusát felügyeli. A hálózatfelügyeleti alrendszer szolgáltatások és alkalmazások életciklusának kezelését végzi. A tesztelhetőségi alrendszer a fejlesztőket alkalmazás tesztelése szimulált hibák a szolgáltatásaikat előtt és után alkalmazások és szolgáltatások telepítése éles környezetekben. A Service Fabric lehetővé teszi a szolgáltatási helyek keresztül a kommunikációs alrendszer megoldásához. A fejlesztők számára elérhetővé tett alkalmazás programozási modellek vannak rétegre alrendszereket együtt az alkalmazásmodell tooling engedélyezéséhez.

## <a name="transport-subsystem"></a>Átviteli alrendszer
A szállítási alrendszer valósítja meg a pont-pont kommunikáció datagramcsatornából. Ez a csatorna belül service fabric-fürtök és a service fabric-fürt és az ügyfelek közötti kommunikációt szolgál. Az egyirányú támogatja-e és a kérelem-válasz kommunikációs kombinációját, amely alapjául szolgáló szórás és a csoportos küldés valósít meg az összevonási rétegben. Az átviteli alrendszer X509 használatával titkosítja a kommunikációs tanúsítványok vagy a Windows biztonsági. Ezt az alrendszer belsőleg Service Fabric és nem közvetlenül elérhető a fejlesztők számára az alkalmazásprogramozási.

## <a name="federation-subsystem"></a>Összevonási alrendszer
Elosztott rendszer az okból kapcsolatos meg csomópontok, meg kell rendelkeznie a rendszer következetes nézetét. Az összevonási alrendszer használja a kommunikációs primitívek biztosítja az átviteli alrendszer és összefűzi a különböző csomópontokon, amely azt az okból kapcsolatos is egyetlen egyesített fürtbe. Az elosztott rendszerek primitívek igényli a más alrendszerek - tárhelyhiba-észlelés vezető választás és egységes útválasztási biztosít. Az összevonási alrendszer elosztott kivonattáblákkal 128 bites token szóközzel épül. Az alrendszer egy körgyűrűs topológia keresztül a csomópontokat, a minden egyes csomópont a gyűrűben tulajdonosát a token terület részhalmazának lefoglalt hoz létre. A tárhelyhiba-észlelés a réteg zúzása szív és egyeztetési alapján bérlési mechanizmus használja. Az összevonási alrendszer is biztosítja, hogy bonyolult csatlakozási és, hogy csak egy tulajdonoshoz jogkivonat létezik bármikor indító protokollok keresztül. Így lehetővé teszi a vezető választási és egységes útválasztási garanciát.

## <a name="reliability-subsystem"></a>Megbízhatóság alrendszer
A megbízhatóság alrendszer biztosítja a mechanizmust, amely a Service Fabric-szolgáltatás használatával magas rendelkezésre állású legyen a *replikátor*, *Feladatátvevőfürt-kezelő*, és *erőforrás-elosztó*.

* A replikátor biztosítja, hogy a rendszer automatikusan másodlagos replikák replikálja a állapotváltozások az elsődleges replika az elsődleges és másodlagos replikák a szolgáltatás replika közötti konzisztencia fenntartása. A replikátor felelős kvórumkezelés a replikákat a replika között. Hatással van az a failover unit replikálásához műveletek listájának lekérdezése, és az újrakonfigurálás ügynök biztosítja azt a replikakészlethez konfigurációjával. Ez a konfiguráció azt jelzi, hogy mely replikák műveletek kell replikálni. A Service Fabric biztosít egy alapértelmezett replikációs háló replikátor, amelyek segítségével a programozási modell API ellenőrizze a szolgáltatás állapotának magas rendelkezésre állású és megbízható nevezik.
* A Feladatátvevőfürt-kezelő biztosítja, hogy amikor csomópontot ad hozzá, vagy eltávolították a fürtből, a terhelés eloszlik a rendelkezésre álló csomópontjai között. Ha a fürtben egy csomópont meghibásodik, a fürt automatikusan konfigurálja úgy a szolgáltatás replikák rendelkezésre álljon.
* Az erőforrás-kezelő szolgáltatás replikák helyezi el a fürt hiba tartományon keresztül, és biztosítja, hogy az összes feladatátvételi egységek működési. Az erőforrás-kezelő szolgáltatás erőforrásainak is kiegyensúlyozza az alapul szolgáló megosztott készletéhez eléréséhez, az optimális egységes terheléselosztási csomópont között.

## <a name="management-subsystem"></a>Hálózatfelügyeleti alrendszer
A hálózatfelügyeleti alrendszer végpont szolgáltatás és alkalmazás-életciklus kezelésének biztosít. PowerShell-parancsmagok és a felügyeleti API-k lehetővé teszik kiépítéséhez, telepítheti, javítás, frissítése és leépíti a rendelkezésre állás elvesztése nélkül alkalmazások. A hálózatfelügyeleti alrendszer hajtja végre ezt a következő szolgáltatások segítségével.

* **Fürtkezelő**: Ez az elsődleges szolgáltatás, amely együttműködik a a Feladatátvevőfürt-kezelő a megbízhatóság számára, hogy az alkalmazások a csomópontokon, a szolgáltatás egy elhelyezési korlátozás alapján. Az erőforrás-kezelő feladatátvételi alrendszerben biztosítja, hogy a megkötések soha nem sérült. A kezelő kezeli az alkalmazások életciklusa leépíti azokat. Az állapotkezelő annak biztosítására, hogy rendelkezésre állásának nem szemantikai állapotfigyelő szempontjából frissítéskor integrálható.
* **Állapotkezelő**: Ez a szolgáltatás lehetővé teszi, hogy az alkalmazások, szolgáltatások és a fürt entitások állapotfigyelés. Fürt entitások (például a csomópontok szolgáltatáspartíciók és replikák) jelenthetik-e a központi a health Store adatbázisban majd összesítve állapotinformációkat. A állapotinformációkat biztosít egy átfogó-időpontban állapotfigyelő pillanatkép, a szolgáltatások és a fürt, így lehetővé teszi bármilyen szükséges javítási műveletet több csomópontja pontjain csomópontok. Az állapotfigyelő alrendszer jelentett állapotfigyelő lekérdezés API-k lehetővé teszik a rendszerállapot-események. A rendszerállapot-lekérdezés API-k a health Store adatbázisban vagy a összesített tárolt nyers üzemállapot-adatait adja vissza egy adott fürt entitás állapotadatok értelmezi.
* **Image Store**: Ez a szolgáltatás biztosítja, tárolás és a bináris alkalmazásfájlokat terjesztése. Ez a szolgáltatás egy egyszerű elosztott fájlrendszer-tároló, ahol az alkalmazások fel van töltve, és le: biztosítja.

## <a name="hosting-subsystem"></a>Üzemeltetési alrendszer
A kezelőt arról értesíti az üzemeltetési alrendszer (minden egyes csomóponton futó) szolgáltatásokat kell egy adott csomópont a kezelése. Az üzemeltetési alrendszer majd kezeli az életciklusa az alkalmazás ezen a csomóponton. Győződjön meg arról, hogy a replikákat megfelelően kerülnek, és a kifogástalan állapotú a megbízhatóság és a rendszerállapot összetevők kommunikál.

## <a name="communication-subsystem"></a>Kommunikációs alrendszer
Biztosítja a fürt és a szolgáltatás beállításait a Naming Service belül, megbízható üzenetküldés. A Naming service szolgáltatásnevek megszünteti a fürt helyre, és lehetővé teszi a felhasználók kezeléséhez a szolgáltatás nevét és tulajdonságait. Az elnevezési szolgáltatással ügyfelek biztonságosan kommunikálni a szolgáltatás nevét és szolgáltatás metaadatok lekérése a fürt bármely csomópontjára. Egy egyszerű elnevezési ügyfél API-ja használ, a Service Fabric felhasználók fejleszthet szolgáltatások és az aktuális hálózati helyét, annak ellenére, hogy a csomópont dinamizmusát vagy a fürt ismételt méretezése feloldása alkalmas ügyfelek.

## <a name="testability-subsystem"></a>Tesztelhetőségi alrendszer
Tesztelhetőségi olyan eszközkészlet a kifejezetten a Service Fabric épülő szolgáltatások tesztelése. Az eszközök segítségével a fejlesztők könnyedén idéz elő a jelentéssel bíró hibák, és futtassa a tesztesetek megadásával, és érvényesítse a számos állapotok és átmenetek, amelyeknek a szolgáltatás teljes élettartamuk, az összes felügyelt és biztonságos módon. Tesztelhetőségi is lehetővé teszi a különféle lehetséges hibák rendelkezésre állásának elvesztése nélkül többször is hosszabb teszteket átszervezhető. Ez lehetővé teszi a teszt az éles környezetben.

