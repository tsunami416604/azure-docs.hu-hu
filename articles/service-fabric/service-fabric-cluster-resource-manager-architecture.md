---
title: Erőforrás-kezelő architektúrája
description: Az Azure Service Fabric fürterőforrás-kezelő szolgáltatás ának áttekintése és architekturális információi.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 94ed906533d108081d620e9b183ecfee249d85ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551692"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Fürterőforrás-kezelő architektúrájának áttekintése
A Service Fabric fürterőforrás-kezelő egy központi szolgáltatás, amely fut a fürtben. Kezeli a fürtben lévő szolgáltatások kívánt állapotát, különösen az erőforrás-felhasználás és az elhelyezési szabályok tekintetében. 

A fürt erőforrásainak kezeléséhez a Service Fabric fürterőforrás-kezelőnek több információval kell rendelkeznie:

- Mely szolgáltatások léteznek jelenleg
- Az egyes szolgáltatások aktuális (vagy alapértelmezett) erőforrás-felhasználása 
- A fennmaradó fürtkapacitás 
- A fürt csomópontjainak kapacitása 
- Az egyes csomópontokon felhasznált erőforrások mennyisége

Egy adott szolgáltatás erőforrás-felhasználása idővel változhat, és a szolgáltatások általában több erőforrástípussal törődnek. A különböző szolgáltatások között valós fizikai és fizikai erőforrások is mérhetők. A szolgáltatások nyomon követhetik a fizikai metrikákat, például a memóriát és a lemezfelhasználást. Gyakrabban, a szolgáltatások törődhetnek a logikai metrikák - például a "WorkQueueDepth" vagy a "TotalRequests". Logikai és fizikai metrikák is használhatók ugyanabban a fürtben. A mérőszámok megoszthatók számos szolgáltatás között, vagy egy adott szolgáltatásra jellemzőek lehetnek.

## <a name="other-considerations"></a>Egyéb szempontok
A fürt tulajdonosai és üzemeltetői különbözhetnek a szolgáltatás- és alkalmazásszerzőktől, vagy legalább ugyanazok a személyek, akik különböző kalapot viselnek. Amikor fejleszti az alkalmazást, tud néhány dolgot arról, hogy mit igényel. Van egy becslést az erőforrásokat fog használni, és hogyan kell telepíteni a különböző szolgáltatásokat. Például a webes réteg kell futtatni a csomópontok az internetnek kitett, míg az adatbázis-szolgáltatások nem. Egy másik példa, a webszolgáltatások valószínűleg korlátozza a CPU és a hálózat, míg az adatréteg szolgáltatások jobban törődnek a memória és a lemezhasználat. Azonban az a személy, aki az éles környezetben lévő szolgáltatás élő hely incidensét kezeli, vagy aki a szolgáltatás frissítését kezeli, más feladattal rendelkezik, és különböző eszközöket igényel. 

Mind a fürt, mind a szolgáltatások dinamikusak:

- A fürtben lévő csomópontok száma növekedhet és zsugorodhat
- Különböző méretű és típusú csomópontok jöhetnek és mehetnek
- A szolgáltatások létrehozhatók, eltávolíthatók, és módosíthatják a kívánt erőforrás-allokációkat és elhelyezési szabályokat
- A frissítések vagy más felügyeleti műveletek az alkalmazáson keresztül az infrastruktúra szintjén gördülhetnek át a fürtön
- Hibák bármikor előfordulhatnak.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Fürterőforrás-kezelő összetevői és adatfolyamai
A fürterőforrás-kezelőnek nyomon kell követnie az egyes szolgáltatások követelményeit és az erőforrások felhasználását az egyes szolgáltatásobjektumok által az adott szolgáltatásokon belül. A fürterőforrás-kezelő két fogalmi részből áll: az egyes csomópontokon futó ügynökökből és egy hibatűrő szolgáltatásból. Az ügynökök minden csomópont pálya terhelési jelentések a szolgáltatások, összesítése, és rendszeresidőközönként jelenteni őket. A fürterőforrás-kezelő szolgáltatás összesíti a helyi ügynököktől származó összes információt, és az aktuális konfiguráció alapján reagál.

Nézzük meg az alábbi ábrát:

<center>

![Erőforrás-kiegyensúlyozó architektúrája][Image1]
</center>

Futásidőben számos változás történhet. Tegyük fel például, hogy egyes szolgáltatások milyen erőforrásokat használnak fel, egyes szolgáltatások meghibásodnak, és egyes csomópontok csatlakoznak a fürthöz, és kilépnek a fürtből. A csomópont összes módosítása összesítésre kerül, és rendszeres időközönként elküldi a fürterőforrás-kezelő szolgáltatásnak (1,2), ahol újra összesítik, elemzik és tárolják őket. Néhány másodpercenként a szolgáltatás megvizsgálja a módosításokat, és meghatározza, hogy szükség van-e valamilyen műveletre (3). Például észreveheti, hogy néhány üres csomópont ot adott hozzá a fürthöz. Ennek eredményeképpen úgy dönt, hogy áthelyez bizonyos szolgáltatásokat ezekre a csomópontokra. A fürterőforrás-kezelő azt is észreveheti, hogy egy adott csomópont túlterhelt, vagy hogy bizonyos szolgáltatások meghibásodtak vagy töröltek, így máshol szabadítfel erőforrásokat.

Nézzük meg az alábbi ábrát, és nézzük meg, mi történik ezután. Tegyük fel, hogy a fürterőforrás-kezelő határozza meg, hogy szükség van-e módosításokra. A szükséges módosítások végrehajtása érdekében más rendszerszolgáltatásokkal (különösen a feladatátvétel-kezelővel) egyeztet. Ezután a szükséges parancsokat elküldi a megfelelő csomópontok (4). Tegyük fel például, hogy az Erőforrás-kezelő észrevette, hogy az 5. Az újrakonfigurálás végén (5) a fürt a következőképpen néz ki:

<center>

![Erőforrás-kiegyensúlyozó architektúrája][Image2]
</center>

## <a name="next-steps"></a>További lépések
- A fürterőforrás-kezelő számos lehetőséget kínál a fürt leírására. Ha többet szeretne megtudni róluk, olvassa el ezt a cikket a [Service Fabric-fürt leírásáról](./service-fabric-cluster-resource-manager-cluster-description.md)
- A fürterőforrás-kezelő elsődleges feladata a fürt újraegyensúlyozása és az elhelyezési szabályok érvényesítése. A viselkedések konfigurálásáról a [Service Fabric-fürt kiegyensúlyozása](./service-fabric-cluster-resource-manager-balancing.md) című témakörben talál további információt.

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
