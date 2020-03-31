---
title: GYAKORI KÉRDÉSEK – Azure adatbázis-áttelepítési szolgáltatás
description: Gyakori kérdések az Azure Database Migration Service használatával kapcsolatban az adatbázis-áttelepítések végrehajtásához.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: a664f12843585ac7524cf8d51aef156d15d32504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650981"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Gyakori kérdések az Azure Database Migration Service használatával kapcsolatban

Ez a cikk az Azure Database Migration Service használatával kapcsolatos gyakori kérdéseket, valamint a kapcsolódó válaszokat sorolja fel.

## <a name="overview"></a>Áttekintés

**K. Mi az Azure database migration service?**
Az Azure Database Migration Service egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a zökkenőmentes áttelepítést több adatbázisforrásból az Azure Data-platformokra minimális állásidővel. A szolgáltatás jelenleg általános anamszer, a folyamatos fejlesztési erőfeszítések középpontjában:

* Megbízhatóság és teljesítmény.
* Forrás-cél párok iteratív hozzáadása.
* A súrlódásmentes migrációba történő folyamatos beruházás.

**K. Jelenleg milyen forrás-/célpárokat támogat jelenleg az Azure Database Migration Service?**
A szolgáltatás jelenleg támogatja a különböző forrás/cél párok, vagy az áttelepítési forgatókönyvek. Az egyes elérhető áttelepítési forgatókönyvek állapotának teljes listáját az [Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapota](https://docs.microsoft.com/azure/dms/resource-scenario-status)című témakörben olvashat.

Más áttelepítési forgatókönyvek előzetes verzióban vannak, és a DMS preview webhelyen keresztül kell benyújtani a jelölést. Az előzetes verzióban elérhető forgatókönyvek teljes listáját és az ilyen ajánlatok valamelyikében való részvételre való feliratkozást a [DMS preview webhelyen](https://aka.ms/dms-preview/)tekintheti meg.

**K. Az SQL Server mely verzióit támogatja forrásként az Azure Database Migration Service?**
Az SQL Server kiszolgálóról való áttelepítéskor az Azure Database Migration Service támogatott forrásai az SQL Server 2005 és az SQL Server 2019.

**K: Az Azure Database Migration Service használatakor mi a különbség az offline és az online áttelepítés között?**
Az Azure Database Migration Service segítségével offline és online áttelepítéseket hajthat végre. Kapcsolat *nélküli* áttelepítés esetén az alkalmazás állásideje az áttelepítés kezdetekor kezdődik. Az *online* áttelepítés sel az állásidő a migráció végén leépítésre rendelkezésre álló időre korlátozódik. Azt javasoljuk, hogy próbálja ki az offline migrálást, és határozza meg, hogy elfogadható-e az állásidő; ha nem, végezzen online migrálást.

> [!NOTE]
> Az Azure Database Migration Service online áttelepítés végrehajtásához létre kell adnia egy példányt a prémium díjszabási szint alapján. További információkért tekintse meg az Azure Database Migration Service [díjszabási](https://azure.microsoft.com/pricing/details/database-migration/) lap.

**K. Hogyan viszonyul az Azure Adatbázis-áttelepítési szolgáltatás más Microsoft adatbázis-áttelepítési eszközökhöz, például az adatbázis-áttelepítési segédhez (DMA) vagy az SQL Server Áttelepítési segédhez (SSMA)?**
Az Azure Database Migration Service az elsődleges módszer az adatbázis-áttelepítés a Microsoft Azure-ba nagy méretekben. Ha többet szeretne tudni arról, hogy az Azure Database Migration Service hogyan viszonyul a Microsoft adatbázis-áttelepítési eszközeihez, és hogy miként szeretné használni a szolgáltatást különböző esetekben, olvassa el a [Microsoft adatbázis-áttelepítési eszközeinek és szolgáltatásainak differenciáló blogjában.](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)

**K. Hogyan viszonyul az Azure Database Migration Service az Azure Áttelepítési ajánlathoz?**
Az Azure Migrate segíti a helyszíni virtuális gépek azure IaaS-be való migrálását. A szolgáltatás felméri az áttelepítés alkalmasságát és a teljesítményalapú méretezést, és költségbecsléseket biztosít a helyszíni virtuális gépek Azure-beli futtatásához. Az Azure Migrate hasznos a helyszíni virtuálisgép-alapú számítási feladatok az Azure IaaS virtuális gépekre történő lift-and-shift áttelepítéséhez. Az Azure Database Migration Service-től eltérően azonban az Azure Migrate nem egy speciális adatbázis-áttelepítési szolgáltatás, amely az Azure PaaS relációs adatbázis-platformokhoz, például az Azure SQL Database-hez vagy az Azure SQL Database Felügyelt példányhoz készült.

## <a name="setup"></a>Telepítés

**K. Melyek az Azure Database Migration Service használatának előfeltételei?**
Számos előfeltétele van annak biztosításához, hogy az Azure Database Migration Service zökkenőmentesen futjon az adatbázis-áttelepítések végrehajtásakor. Az előfeltételek egy része a szolgáltatás által támogatott összes forgatókönyvre (forrás-cél párokra) vonatkozik, míg más előfeltételek egyediek egy adott forgatókönyvhöz.

Az Azure Database Migration Service előfeltételei, amelyek az összes támogatott áttelepítési forgatókönyvben gyakoriak, a következőket foglalják magukban:

* Hozzon létre egy Microsoft Azure virtuális hálózatot az Azure adatbázis-áttelepítési szolgáltatáshoz az Azure Resource Manager telepítési modelljével, amely az [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával biztosít helyek közötti kapcsolatot a helyszíni forráskiszolgálókhoz.
* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport szabályai nem blokkolják a következő 443, 53, 9354, 445, 12000 kommunikációs portokat. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részleteket a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal című témakörben olvashat.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)
* Ha a forrásadatbázis(ok) előtt tűzfalberendezést használ, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia ahhoz, hogy az Azure Database Migration Service hozzáférhessen a forrásadatbázis(ok)hoz az áttelepítéshez.

Az Azure Database Migration Service használatával az Azure Database Migration Service használatával adott áttelepítési forgatókönyvek versenyéhez szükséges előfeltételek listáját az Azure Database Migration Service [dokumentációjában,](https://docs.microsoft.com/azure/dms/dms-overview) a docs.microsoft.com kapcsolatos oktatóanyagokban találja.

**K. Hogyan találhatom meg az Azure Database Migration Service IP-címét, hogy létrehozhassak egy engedélyezési listát az áttelepítéshez használt tűzfalszabályokhoz?**
Előfordulhat, hogy olyan tűzfalszabályokat kell hozzáadnia, amelyek lehetővé teszik az Azure Database Migration Service számára a forrásadatbázishoz való hozzáférést az áttelepítéshez. A szolgáltatás IP-címe dinamikus, de ha ExpressRoute-ot használ, ezt a címet a vállalati hálózat magánszemélyként rendeli hozzá. A megfelelő IP-cím azonosításának legegyszerűbb módja, ha ugyanabban az erőforráscsoportban keres, mint a kiépített Azure Database Migration Service erőforrás a társított hálózati illesztő megkereséséhez. A hálózati adapter erőforrás neve általában a hálózati adapter előtaggal kezdődik, amelyet egy egyedi karakter és számsorozat követ, például NIC-jj6tnztnmarpsskr82rbndyp. A hálózati csatoló erőforrás kiválasztásával láthatja az IP-címet, amelyet meg kell adni az erőforrás-áttekintés Azure Portal lap engedélyezési listájában.

Előfordulhat, hogy az SQL Server által figyelt portforrást is meg kell adnia az engedélyezési listához. Alapértelmezés szerint az 1433-as port, de a forrás SQL Server lehet beállítani, hogy figyelje a többi porton is. Ebben az esetben ezeket a portokat is fel kell tüntetnie az engedélyezési listán. Dinamikus felügyeleti nézet lekérdezéssel határozhatja meg azt a portot, amelyet az SQL Server figyel:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Az SQL Server hibanaplójának lekérdezésével is meghatározhatja az SQL Server által figyelt portot:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**K. Hogyan állíthatok be Microsoft Azure virtuális hálózatot?**
Bár több Microsoft oktatóanyag, amely végigvezeti a virtuális hálózat létrehozásának folyamatán, a hivatalos dokumentáció az [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)című cikkben jelenik meg.

## <a name="usage"></a>Használat

**K. Mi az Azure Database Migration Service adatbázis-áttelepítéshez szükséges lépések összegzése?**
Egy tipikus, egyszerű adatbázis-áttelepítés során a következőket kell:

1. Hozzon létre egy céladatbázis(oka)t.
2. A forrásadatbázis(ok) felmérése.
    * Homogén áttelepítések esetén értékelje a meglévő adatbázis(oka)t a [DMA](https://www.microsoft.com/download/details.aspx?id=53595)használatával.
    * Heterogén áttelepítések esetén (versenyforrásokból) értékelje a meglévő adatbázis(oka)t az [SSMA-val.](https://aka.ms/get-ssma) Az SSMA segítségével adatbázis-objektumokat is konvertálhat, és áttelepítheti a sémát a célplatformra.
3. Hozzon létre egy Azure Database Migration Service-példányt.
4. Hozzon létre egy áttelepítési projektet, amely megadja a forrásadatbázis(oka)t, a céladatbázis(oka)t és az áttelepítendő táblákat.
5. Indítsd el a teljes terhelést.
6. Válassza ki a későbbi érvényesítést.
7. Az éles környezet manuális átkapcsolása az új felhőalapú adatbázisra.

## <a name="troubleshooting-and-optimization"></a>Hibaelhárítás és optimalizálás

**K. Áttelepítési projektet állítok fel a DMS-ben, és nehezen tudok csatlakozni a forrásadatbázishoz. Mit kell tennem?**
Ha az áttelepítés során nem sikerül csatlakoznia a forrásadatbázis-rendszerhez, hozzon létre egy virtuális gépet abban a virtuális hálózatban, amellyel beállította a DMS-példányt. A virtuális gép, képesnek kell lennie arra, hogy futtasson egy csatlakozási teszt, például egy UDL-fájl segítségével, hogy teszteljék a kapcsolatot az SQL Server vagy a robot 3T letöltése teszt MongoDB kapcsolatok. Ha a kapcsolatteszt sikeres, nem lehet probléma a forrásadatbázishoz való csatlakozással kapcsolatban. Ha a csatlakozási teszt nem sikerül, forduljon a hálózati rendszergazdához.

**K. Miért nem érhető el vagy nem érhető el az Azure-adatbázis-áttelepítési szolgáltatásom?**
Ha a felhasználó explicit módon leállítja az Azure Database Migration Service (DMS) szolgáltatást, vagy ha a szolgáltatás 24 órán keresztül inaktív, a szolgáltatás leállított vagy automatikusan szüneteltetésre kerül. A szolgáltatás minden esetben nem lesz elérhető, és leállított állapotban lesz.  Az aktív áttelepítések folytatásához indítsa újra a szolgáltatást.

**K. Vannak-e javaslatok az Azure Database Migration Service teljesítményének optimalizálására?**
Néhány dolog, hogy gyorsítsa fel az adatbázis-áttelepítés a szolgáltatás használatával:

* Használja a több processzor általános célú díjszabási szint, amikor létrehozza a szolgáltatáspéldány, hogy a szolgáltatás kihasználhatja a több vCPU-k a párhuzamosítás és a gyorsabb adatátvitel.
* Az azure-i SQL Database-célpéldány tág ítására ideiglenesen skálázhatja az adatáttelepítési művelet során a prémium szintű termékváltozatra, hogy minimalizálja az Azure SQL Database szabályozását, amely hatással lehet az adatátviteli tevékenységekre alacsonyabb szintű termékváltozatok használatakor.

## <a name="next-steps"></a>További lépések

Az Azure Database Migration Service és a regionális elérhetőség áttekintését a [Mi az Azure-adatbázis-áttelepítési szolgáltatás](dms-overview.md)című cikkben találja.
