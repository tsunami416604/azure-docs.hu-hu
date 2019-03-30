---
title: Az Azure Service Fabric-architektúra |} A Microsoft Docs
description: Service Fabric egy elosztott rendszerplatform, skálázható, megbízható és könnyen kezelhető alkalmazások a felhőben használható a. Ez a cikk a Service Fabric architektúráját mutatja be.
services: service-fabric
documentationcenter: .net
author: rishirsinha
manager: chackdan
editor: rishirsinha
ms.assetid: 6b554243-70cb-4c22-9b28-1a8b4703f45e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/12/2017
ms.author: rsinha
ms.openlocfilehash: a1e68e2e39ea6f1c8cf8669e2e02d8dacaf0f284
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664590"
---
# <a name="service-fabric-architecture"></a>A Service Fabric-architektúra
A Service Fabric beépített többrétegű alrendszerek van. Ezek az alrendszerek alkalmazásokat készíthet, amelyek lehetővé teszik:

* Magas rendelkezésre állású
* Méretezhető
* Felügyelhető
* Testable

Az alábbi ábrán látható, a Service fabric nagyobb alrendszerek.

![A Service Fabric-architektúra ábrája](media/service-fabric-architecture/service-fabric-architecture.png)

Az elosztott rendszerekben rendkívül fontos arra, hogy a fürt csomópontjai közötti biztonságos kommunikációhoz. A verem alap van az átviteli alrendszer, amely biztosít a biztonságos kommunikáció a csomópontok között. Átviteli fent alrendszer működik az összevonási alrendszer, amely fürtöket a különböző csomópontokon egyetlen entitás (a fürtök neve), Service Fabric hibák észlelése, hajtsa végre a vezetőválasztás és egységes útválasztást. A megbízhatóság alrendszer, egymás felett az összevonási alrendszer felelős a Service Fabric-szolgáltatások, például a replikáció, az erőforrás-kezelés és feladatátvételi mechanizmusokon keresztül megbízhatóságát. Az összevonási alrendszer is az üzemeltetési és aktiválási alrendszer, egyetlen csomóponton az alkalmazás életciklusát felügyelő alapjául szolgál. A management alrendszertől az alkalmazások és szolgáltatások életciklusát felügyeli. A testability alrendszer segítségével az alkalmazásfejlesztők a szimulált hibák szolgáltatásaikat tesztelése előtt és után, alkalmazások és szolgáltatások telepítése vagy éles környezetekre. A Service Fabric lehetővé teszi szolgáltatási helyeken keresztül a kommunikációs alrendszer megoldásához. A fejlesztők elérhetővé tett alkalmazás programozási modellekről vannak egymás mellett az eszközök engedélyezéséhez alkalmazásmodell alrendszereket felett.

## <a name="transport-subsystem"></a>Átviteli alrendszer
Az átviteli alrendszer valósítja meg a pont-pont típusú datagram kommunikációs csatornát. Ezen a csatornán belül a service fabric-fürtök és a service fabric-fürt és az ügyfelek közötti kommunikációt szolgál. Támogatja az egyirányú és a kérés-válasz kommunikációs mintái, amelyek alapjául szolgáló szórásos és a csoportos küldés megvalósítása az összevonási rétegre. Az átviteli alrendszer X509 használatával titkosítja a kommunikációs tanúsítványokat vagy a Windows biztonsági. Ez az alrendszer belsőleg Service Fabric, és nem érhető el közvetlenül a fejlesztők számára az alkalmazások programozását.

## <a name="federation-subsystem"></a>Összevonási alrendszer
Annak érdekében, hogy az elosztott rendszerekben az okból kapcsolatos csomópontok egy készletét, szüksége lesz egy egységes nézetben a rendszer. Az összevonási alrendszer a a szállítás alrendszer által biztosított kommunikációs primitívek használ, és összefűzi a különböző csomópontokhoz, amelyek azt is okból kapcsolatos egyetlen egységes fürtbe való. Az elosztott rendszerek primitívek szükséges az egyéb alrendszerek - hibaészlelés, vezetőválasztási és egységes útválasztást biztosít. Az összevonási alrendszer elosztott kivonattáblák 128 bites token szóközzel épül. Az alrendszer egy körgyűrűs topológia a csomópontokon, a tulajdonjogát egy része a token terület lefoglalásra gyűrű lévő mindegyik csomópont feletti hoz létre. A réteg hibaészlelés, használja a szív zúzása és választottbírósági alapján bérlési mechanizmus. Az összevonási alrendszer bonyolult csatlakozási és indító protokollok, amely csak egyetlen tulajdonosának jogkivonatot bármikor keresztül is garantálja. Ez biztosítja, vezetőválasztási és egységes útválasztási garanciát.

## <a name="reliability-subsystem"></a>Megbízhatóság alrendszer
A megbízhatóság alrendszer biztosít a Service Fabric-szolgáltatás állapotának magas rendelkezésre állásúvá használatával mechanizmus a *replikátor*, *Feladatátvevőfürt-kezelő*, és *erőforrás A terheléselosztó*.

* A replikátor biztosítja, hogy a szolgáltatás elsődleges replikáját állapotváltozások automatikusan replikálja a másodlagos replikákon egy szolgáltatás replika az elsődleges és másodlagos replikák közötti konzisztencia megőrzése érdekében. A replikátor felelős kvórumkezelés a replikákat a replika között. Az újrakonfigurálási ügynök nyújt a replikakészlet-konfigurációval, és azt beküldje a feladatátvételi egység számára replikálásához műveletek listájának beolvasása. Ez a konfiguráció azt jelzi, hogy milyen műveleteket kell replikálni replikák. A Service Fabric biztosít egy alapértelmezett replikátor nevű Fabric-replikátor, amely, hogy a szolgáltatás állapotát, magas rendelkezésre állású és megbízható a programozási modell API által használható.
* A Feladatátvevőfürt-kezelő biztosítja, hogy csomópontok hozzáadásakor vagy eltávolítja a fürtről, a terhelés eloszlik a rendelkezésre álló csomópontok között. Ha a fürtben egy csomópont meghibásodik, a fürt automatikusan konfigurálja újra a szolgáltatást replikák rendelkezésre állását.
* Az erőforrás-kezelő szolgáltatás replikák elhelyezi a fürt meghibásodása tartományon keresztül, és biztosítja, hogy az összes feladatátvételi egységek működési. Az erőforrás-kezelő szolgáltatás-erőforrások is elosztja a fürtcsomópontok egységes optimális terheléselosztást eléréséhez a mögöttes megosztott készleten belül.

## <a name="management-subsystem"></a>Hálózatfelügyeleti alrendszer
A management alrendszertől biztosít teljes körű szolgáltatás és application lifecycle management. PowerShell-parancsmagok és a felügyeleti API-k lehetővé teszik kiépítéséhez, üzembe helyezése, patch, frissítése és megszüntetése a rendelkezésre állás elvesztése nélkül alkalmazásokat. A management alrendszertől végez, ez az alábbi szolgáltatásokon keresztül.

* **Kezelő**: Ez az az elsődleges szolgáltatás, amely együttműködik a feladatátvételi Managerrel a megbízhatóság számára, hogy az alkalmazások a csomópontok a szolgáltatás-elhelyezési megkötések alapján. Az erőforrás-kezelő feladatátvételi alrendszerben biztosítja, hogy a megkötések soha nem sérült. A fürtkezelő kezeli az alkalmazások életciklusa a kiépítés megszüntetése. Integrálható a health manager győződjön meg arról, hogy rendelkezésre állásának nem a szemantikai állapota szempontjából elveszett frissítések során.
* **Ügyfélállapot-kezelő**: A szolgáltatás lehetővé teszi alkalmazások, szolgáltatások és a fürt entitások szolgáltatásállapot-figyelést. Fürt entitások (például a csomópontok, a szolgáltatás partícióinak és replikák) jelenthetik egészségügyi információk, amelyek majd összesített értéket jelenít meg a központi health store-bA. Az állapotinformációkat biztosít egy teljes-időponthoz egészségügyi pillanatképet a szolgáltatások és a csomópontok elosztott több csomóponton a fürtben, lehetővé téve bármilyen szükséges javítási műveleteket. Az egészségügyi alrendszer jelentett állapot lekérdezési API-k lehetővé teszik a Szolgáltatásállapot-események lekérdezése. A health lekérdezés API-k az a health Store adatbázisban vagy az összesített tárolt nyers rendszerállapot-adatokat ad vissza egy adott fürtben entitás állapotadatok értelmezi.
* **Store kép**: Ez a szolgáltatás biztosítja a storage és az alkalmazás bináris fájljainak terjesztését. Ezt a szolgáltatást egy egyszerű, elosztott fájlrendszer-tárolót, ahol az alkalmazások feltöltött és letöltött biztosít.

## <a name="hosting-subsystem"></a>Videotár alrendszere
A kezelőt arról tájékoztatja, hogy mely szolgáltatások kell egy adott csomópont kezelése üzemeltetési alrendszer (minden egyes csomóponton fut). A videotár alrendszere majd ezen a csomóponton az alkalmazás életciklusának kezelése. Győződjön meg arról, hogy a replikák megfelelően kerülnek, és kifogástalan állapotú a megbízhatóság és egészségügyi összetevőket kommunikál.

## <a name="communication-subsystem"></a>Kommunikációs alrendszer
Ez az alrendszer belül az elnevezési szolgáltatásban keresztül a fürt és a szolgáltatás felderítés megbízható üzenetküldést biztosít. Az elnevezési szolgáltatásban oldja fel a szolgáltatásneveket a fürt egy olyan helyre, és lehetővé teszi a felhasználók kezeléséhez a szolgáltatás nevét és tulajdonságait. Használja az elnevezési szolgáltatásban, az ügyfelek biztonságosan kommunikálhat oldja meg a szolgáltatás nevét, és a szolgáltatás metaadatok lekérése a fürt bármely csomópontjának. Egy egyszerű elnevezési ügyfél API-t használ, a Service Fabric-felhasználók fejleszthet szolgáltatásokat és megoldása, annak ellenére, hogy a csomópont dinamizmusát az aktuális hálózati helyét, vagy a fürt ismételt méretezése alkalmas ügyfelek.

## <a name="testability-subsystem"></a>Testability alrendszer
Testability olyan kifejezetten a Service Fabric beépített szolgáltatásokat tesztelési eszközök. Az eszközök lehetővé teszik a fejlesztők könnyen értelmezhető hibák idéz elő, és futtassa a gyakorlását és a számos állapotok és átmenetek, amelyeknek a szolgáltatás teljes élettartamuk, az összes olyan ellenőrzött és biztonságos módon ellenőrizze a tesztelési helyzetek. Testability különféle lehetséges hibák ismételt futtatásával is hosszabb tesztek futtatása rendelkezésre állás elvesztése nélkül mechanizmust is biztosít. Ez ad tesztelése éles környezetben.

