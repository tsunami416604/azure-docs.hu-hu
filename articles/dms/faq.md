---
title: Gyakori kérdések – Azure Database Migration Service
description: Gyakori kérdések a Azure Database Migration Service használatáról az adatbázis-áttelepítések végrehajtásához.
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
ms.openlocfilehash: 3383a16624ecf045753609865931f9d0c44b6b3f
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196588"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Gyakori kérdések a Azure Database Migration Service használatáról

Ez a cikk a Azure Database Migration Service és a kapcsolódó válaszok együttes használatával kapcsolatos gyakori kérdéseket sorolja fel.

## <a name="overview"></a>Áttekintés

**K. Mi az Azure Database Migration Service?**
A Azure Database Migration Service egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi, hogy a zökkenőmentes áttelepítések több adatbázisból az Azure-beli adatplatformokra minimális állásidővel. A szolgáltatás jelenleg általánosan elérhető, és a folyamatos fejlesztési erőfeszítések az alábbiakra összpontosítanak:

* Megbízhatóság és teljesítmény.
* A forrás-cél párok iterációs hozzáadása.
* Folyamatos beruházás a súrlódások közötti áttelepítéshez.

**K. milyen forrás/cél párokat Azure Database Migration Service jelenleg a támogatás?**
A szolgáltatás jelenleg számos különböző forrás-, cél-vagy áttelepítési forgatókönyvet támogat. Az egyes elérhető áttelepítési forgatókönyvek állapotának teljes listáját lásd a [Azure Database Migration Service által támogatott áttelepítési forgatókönyvek állapotáról szóló](https://docs.microsoft.com/azure/dms/resource-scenario-status)cikkben.

Más áttelepítési forgatókönyvek előzetes verzióban érhetők el, és a DMS előzetes verziójának használatával kell benyújtani a jelölést. Az előzetes verzióban elérhető forgatókönyvek teljes listáját és az egyik ajánlatban való részvételre való regisztrációt a [DMS előzetes](https://aka.ms/dms-preview/)verziójának webhelyén tekintheti meg.

**K. a SQL Server mely verziói Azure Database Migration Service támogatják a forrást?**
SQL Server rendszerből való áttelepítéskor a Azure Database Migration Service támogatott forrásai SQL Server 2005 SQL Server 2019.

**K: Azure Database Migration Service használata esetén mi a különbség az offline és az online áttelepítés között?**
A Azure Database Migration Service használatával offline és online áttelepítést végezhet el. *Offline* áttelepítés esetén az alkalmazás leállása az áttelepítés megkezdése után kezdődik. Az *online* áttelepítéssel az állásidő az áttelepítés végén eltelt időre korlátozódik. Azt javasoljuk, hogy próbálja ki az offline migrálást, és határozza meg, hogy elfogadható-e az állásidő; ha nem, végezzen online migrálást.

> [!NOTE]
> A Azure Database Migration Service használata az online áttelepítés végrehajtásához a prémium szintű díjszabás alapján kell létrehoznia egy példányt. További információkért tekintse meg a Azure Database Migration Service [díjszabását](https://azure.microsoft.com/pricing/details/database-migration/) ismertető oldalt.

**K. Hogyan hasonlítható össze Azure Database Migration Service a Microsoft Database áttelepítési eszközökhöz, például az adatbázis-Migration Assistant (DMA) vagy a SQL Server Migration Assistanthoz (SSMA)?**
A Azure Database Migration Service az adatbázis áttelepítésének előnyben részesített módszere a Microsoft Azure méretekben. További információ arról, hogy Azure Database Migration Service hogyan hasonlítható össze a Microsoft adatbázis-áttelepítési eszközeivel, illetve a szolgáltatás különböző forgatókönyvekhez való használatával kapcsolatos javaslatokért tekintse meg a [Microsoft adatbázis-áttelepítési eszközeit és szolgáltatásait megkülönböztető](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)blogbejegyzéseket.

**K. milyen Azure Database Migration Service összehasonlítani a Azure Migrate ajánlattal?**
Azure Migrate segíti a helyszíni virtuális gépek Azure IaaS való áttelepítését. A szolgáltatás felméri az áttelepítési alkalmasságot és a teljesítmény-alapú méretezést, és a helyszíni virtuális gépek Azure-ban való futtatásának becsült költségeit is tartalmazza. Azure Migrate hasznos a helyszíni virtuálisgép-alapú számítási feladatok Azure IaaS virtuális gépekre történő áttelepítésének és átváltásának elvégzéséhez. A Azure Database Migration Servicetól eltérően azonban Azure Migrate nem egy speciális adatbázis-áttelepítési szolgáltatás, amely az Azure-beli, vagy az Azure SQL felügyelt példányaihoz, például a Azure SQL Databasehoz és az Azure SQL-hez készült

## <a name="setup"></a>Telepítés

**K. Mik a Azure Database Migration Service használatának előfeltételei?**
Több előfeltétel szükséges annak biztosításához, hogy Azure Database Migration Service zökkenőmentesen fusson az adatbázis-áttelepítés végrehajtásakor. Néhány előfeltétel a szolgáltatás által támogatott összes forgatókönyv (forrás-cél párok) esetében érvényes, míg más előfeltételek egyediek egy adott forgatókönyv esetében.

Azure Database Migration Service az összes támogatott áttelepítési forgatókönyv esetében gyakori előfeltételek közé tartozik a következők szükségesek:

* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Service számára a Azure Resource Manager üzemi modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával.
* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport szabályai nem gátolják meg a következő kommunikációs portokat: 443, 53, 9354, 445, 12000. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)című cikket.
* Ha a forrásadatbázis (ok) előtt tűzfal-berendezést használ, előfordulhat, hogy olyan tűzfalszabályok hozzáadására van szükség, amelyek lehetővé teszik a Azure Database Migration Service számára a forrás-adatbázis (ok) elérését az áttelepítéshez.

Az egyes áttelepítési forgatókönyvek Azure Database Migration Service használatával való versenyéhez szükséges előfeltételek listáját a docs.microsoft.com-ben található Azure Database Migration Service [dokumentációban](https://docs.microsoft.com/azure/dms/dms-overview) találhatja meg a kapcsolódó oktatóanyagokat.

**K. Hogyan megkeresni a Azure Database Migration Service IP-címét, hogy Létrehozhatok egy engedélyezési listát a forrásadatbázis áttelepítéshez való hozzáféréséhez használt tűzfalszabályok számára?**
Előfordulhat, hogy olyan tűzfalszabályok hozzáadására van szükség, amelyek lehetővé teszik a Azure Database Migration Service számára, hogy hozzáférjenek a forrás-adatbázishoz az áttelepítés során. A szolgáltatás IP-címe dinamikus, de ha a ExpressRoute-t használja, ezt a címet a vállalati hálózat is saját maga rendeli hozzá. A megfelelő IP-cím azonosításának legegyszerűbb módja, ha ugyanazt az erőforráscsoportot tekinti meg, mint a kiépített Azure Database Migration Service erőforrás a társított hálózati adapter megtalálásához. Általában a hálózati adapter erőforrásának neve a NIC előtaggal kezdődik, majd egy egyedi karakter-és számsorozattal, például a NIC-jj6tnztnmarpsskr82rbndyp. A hálózati adapter erőforrásának kiválasztásával megtekintheti azt az IP-címet, amelyet az erőforrás-áttekintés Azure Portal lapon szerepeltetni kell az engedélyezési listán.

Előfordulhat, hogy meg kell adnia azt a forrásport-forrást is, amelyet a SQL Server figyel az engedélyezési listán. Alapértelmezés szerint az 1433-es port, a forrás SQL Server azonban más portok figyelésére is konfigurálható. Ebben az esetben ezeket a portokat is fel kell venni az engedélyezési listára. Megadhatja azt a portot, amelyet a SQL Server figyel a dinamikus felügyeleti nézet lekérdezése segítségével:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Azt is megadhatja, hogy a SQL Server milyen portot figyel, ha lekérdezi a SQL Server hibanapló:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**K. Hogyan beállítja a Microsoft Azure Virtual Networkt?**
Noha több Microsoft-oktatóanyag is van, amely végigvezeti a virtuális hálózat beállításának folyamatán, a hivatalos dokumentáció az [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)cikkben jelenik meg.

## <a name="usage"></a>Használat

**K. milyen összefoglalja a szükséges lépéseket az adatbázisok áttelepítésének elvégzéséhez Azure Database Migration Service használatához?**
Egy tipikus, egyszerű adatbázis-áttelepítés során a következőket végezheti el:

1. Hozzon létre egy céladatbázis (eke) t.
2. Mérje fel a forrás-adatbázis (oka) t.
    * A homogén áttelepítések esetében a meglévő adatbázis (oka) t a [DMA](https://www.microsoft.com/download/details.aspx?id=53595)használatával értékelheti ki.
    * Heterogén áttelepítések esetén (a versengő forrásokból) mérje fel a meglévő adatbázis (oka) t a [SSMA](https://aka.ms/get-ssma). A SSMA használatával is konvertálhatja az adatbázis-objektumokat, és áttelepítheti a sémát a célként megadott platformra.
3. Hozzon létre egy Azure Database Migration Service-példányt.
4. Hozzon létre egy áttelepítési projektet, amely meghatározza a forrásadatbázis (oka) t, a célként megadott adatbázist és az áttelepítendő táblákat.
5. Indítsa el a teljes betöltést.
6. Válassza ki a következő érvényesítést.
7. Végezze el az éles környezet manuális áttelepítését az új felhőalapú adatbázisra.

## <a name="troubleshooting-and-optimization"></a>Hibaelhárítás és optimalizálás

**K. létrehozok egy áttelepítési projektet a DMS-ben, és nehezen tudok csatlakozni a forrás-adatbázishoz. Mit tegyek?**
Ha nem sikerül csatlakoznia a forrás-adatbázis rendszeréhez az áttelepítés során, hozzon létre egy virtuális gépet a virtuális hálózaton, amellyel beállíthatja a DMS-példányt. A virtuális gépen képesnek kell lennie egy csatlakozási teszt futtatására, például egy UDL-fájl használatával SQL Server vagy a Robo 3T letöltésére a MongoDB-kapcsolatok teszteléséhez. Ha a kapcsolat tesztelése sikeres, a forrás-adatbázishoz való csatlakozás nem jelent problémát. Ha a kapcsolat tesztelése nem sikerül, forduljon a hálózati rendszergazdához.

**K. Miért nem érhető el vagy leáll a Azure Database Migration Service?**
Ha a felhasználó explicit módon leállítja Azure Database Migration Service (DMS) szolgáltatást, vagy ha a szolgáltatás 24 órán keresztül inaktív, a szolgáltatás leállított vagy automatikusan szüneteltetve állapotba kerül. A szolgáltatás minden esetben elérhetetlenné válik, és leállított állapotban van.  Az aktív Migrálás folytatásához indítsa újra a szolgáltatást.

**K. vannak javaslatok a Azure Database Migration Service teljesítményének optimalizálására?**
A szolgáltatás használatával felgyorsíthatja az adatbázis áttelepítését:

* Használja a többprocesszoros általános célú díjszabását a szolgáltatási példány létrehozásakor, hogy a szolgáltatás kihasználja a párhuzamos és gyorsabb adatátvitelt biztosító több vCPU előnyeit.
* Az adatáttelepítési művelet során átmenetileg felskálázás a Azure SQL Database Target-példányról a prémium szintű SKU-ra, hogy csökkentse Azure SQL Database szabályozást, amely hatással lehet az adatátviteli tevékenységekre, amikor alacsonyabb szintű SKU-ket használ.

## <a name="next-steps"></a>További lépések

A Azure Database Migration Service és a regionális elérhetőség áttekintését lásd: [Mi a Azure Database Migration Service](dms-overview.md).
