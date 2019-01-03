---
title: Az Azure Database Migration Service használatával kapcsolatos gyakori kérdések |} A Microsoft Docs
description: Ismerje meg az adatbázis-migrálási parancsait végrehajtásához az Azure Database Migration Service használatával kapcsolatos gyakori kérdésekre.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/19/2018
ms.openlocfilehash: b8001729c85c8447ab22bafffe24a32524a0662a
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714695"
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Az Azure Database Migration Service használatával kapcsolatos gyakori kérdések
Ez a cikk felsorolja az Azure Database Migration Service kapcsolódó válaszokat és használatával kapcsolatos gyakori kérdésekre.

### <a name="q-what-is-azure-database-migration-service"></a>K. Mi az Azure Database Migration Service?
Az Azure Database Migration Service egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a zökkenőmentes migrálást a több adatbázis-forrásokhoz, az Azure-adatplatformokra minimális állásidővel. A szolgáltatás jelenleg általánosan elérhető az összpontosított a folyamatos fejlesztéshez:
- Megbízhatóságát és teljesítményét.
- Forrás – cél párok migrációját iteratív hozzáadásával.
- Folyamatos befektetést áttelepítések fennakadások nélkül használható.

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>K. Milyen forrás – cél párok az Azure Database Migration Service jelenleg támogatja?
A szolgáltatás jelenleg támogatja a különböző áttelepítési forgatókönyveinek. Állapotát az egyes érhető el áttelepítési forgatókönyvek teljes listáját lásd: a cikk [állapotát az Azure Database Migration Service által támogatott áttelepítési forgatókönyvek](https://docs.microsoft.com/azure/dms/resource-scenario-status). Egyéb áttelepítési forgatókönyvek korlátozott előzetes verzióban elérhető és a DMS Preview-helyeken keresztül nevezés beküldése igényel. A korlátozott előzetes verzióban érhető el, és a regisztráció particpate egy ezeket az ajánlatokat a forgatókönyvek teljes listáját lásd: a [DMS Preview-hely](https://aka.ms/dms-preview/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>K. Az Azure Database Migration Service összehasonlítása a más Microsoft adatbázis-áttelepítési eszközök például a Database Migration Assistant (DMA) vagy az SQL Server Migrálási Assistant (SSMA)
Az Azure Database Migration Service legyen az előnyben részesített módszere ipari méretekben a Microsoft Azure adatbázis-migrálást. További részleteket az Azure Database Migration Service hogyan hasonlítja össze a más Microsoft-adatbázis-áttelepítési eszközök és kapcsolatos javaslatokat a szolgáltatás különböző forgatókönyvek segítségével, tekintse meg a blog könyvelés [sokoldalúbbá Microsoft adatbázis Migrálási eszközöket és szolgáltatásokat](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>K. Az Azure Database Migration Service összehasonlítása, az Azure Migrate ajánlat
Az Azure iaas-ben a helyszíni virtuális gépek migrálása az Azure Migrate szolgáltatás segítséget nyújt. A szolgáltatás felméri a migrálásra való alkalmasságát, és a teljesítményalapú, és költségbecslések a helyszíni virtuális gépek futtatásához az Azure biztosít. Az Azure Migrate hasznos lift-and-shift áttelepítéséhez a helyszíni Virtuálisgép-alapú számítási feladatok Azure IaaS virtuális gépekre. Azonban eltérően az Azure Database Migration Service, Azure Migrate nem egy speciális adatbázis-áttelepítési szolgáltatás, amely az Azure PaaS relációs adatbázis platformokhoz, mint az Azure SQL Database vagy SQL Azure vagy az Azure SQL Database felügyelt példányain.

### <a name="q-what-versions-of-sql-server-does-the-azure-database-migration-service-support-as-a-source"></a>K. Az SQL Server mely verzióit támogatja az Azure Database Migration Service forrásként?
Migrálás SQL Serverről, amikor az Azure Database Migration Service támogatja az SQL Server 2005, SQL Server 2017-ig.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>K. Mi az az Azure Database Migration Service használatához egy adatbázis-áttelepítés végrehajtásához szükséges lépések összefoglalása?
Egy tipikus, egyszerű adatbázist a migrálás során meg:
1.  Hozzon létre egy cél-adatbázisokat.
2.  Az adatbázis-séma áttelepítése a [Database Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3.  Egy Azure Database Migration Service-példány létrehozása.
4.  Hozzon létre egy migrálási projekt megadása a forrásadatbázis (ok), a cél adatbázisának/adatbázisainak szolgáltatásparitási és a táblák áttelepíteni.
5.  Indítsa el a teljes terhelés.
6.  Válassza ki az ezt követő érvényesítése.
7.  Hajtsa végre a manuális Váltás az éles környezet az új felhőalapú adatbázishoz. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>K. Mik az Azure Database Migration Service használatára vonatkozó Előfeltételek?
Nincsenek győződjön meg arról, hogy az Azure Database Migration Service zökkenőmentesen adatbázis-migrálások végrehajtása során kell néhány előfeltételnek. Egyes Előfeltételek a alkalmazni közben egyéb előfeltételek egyediek egy adott forgatókönyv a szolgáltatás által támogatott összes forgatókönyv (forrás-cél párok) között.
Az Azure Database Migration Service előfeltételei, amelyek közösek az összes támogatott áttelepítési forgatókönyvek között kell tartalmaznia:
- Hozzon létre egy virtuális hálózatot az Azure Database Migration Service-hez az Azure Resource Manager-alapú üzemi modell használatával, amely a hálózat helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgálóknak [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) használatával.
- Győződjön meg arról, hogy az Azure Virtual Network (VNET) hálózati biztonsági szabályok nem blokkolják a következő kommunikációs portokat: 443, 53, 9354, 445, 12000. További részletek az Azure VNET NSG-forgalom szűréséről: [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
 
Jelenlegi versenyének adott áttelepítési forgatókönyvek használatával az Azure Database Migration Service szükséges összes előfeltételt listáját lásd: a kapcsolódó oktatóanyagok az Azure Database Migration Service [dokumentáció](https://docs.microsoft.com/azure/dms/dms-overview) a a docs.microsoft.com webhelyen.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>K. Hogyan találhatom meg az IP-cím az Azure Database Migration Service úgy, hogy létre tudok hozni egy engedélyezési listája a tűzfalszabályok az áttelepítéshez a forrás-adatbázis eléréséhez használt?
Előfordulhat, hogy a tűzfalszabályok engedélyezése a forráshely adatbázisára az áttelepítéshez eléréséhez az Azure Database Migration Service hozzá kell. A szolgáltatás IP-címét a dinamikus, de ha Express Route használja, ezt a címet közvetlenül a Microsoftnak a vállalati hálózat által hozzárendelt. A legegyszerűbben úgy, hogy azonosítsa a megfelelő IP-címet, nézze meg ugyanazt az erőforráscsoportot és a kiépített Azure Database Migration Service-erőforrásnak a társított hálózati adapter található. A hálózati adapter erőforrás neve általában a hálózati adapter előtaggal kezdődik, és egy egyedi karaktert, és a sorrend, például NIC-jj6tnztnmarpsskr82rbndyp követ. Az IP-cím, amelyet a portál oldala az Azure erőforrás-áttekintés az engedélyezési listán szereplő megjelenik a hálózati adapter erőforrás kijelölésével.

Szükség lehet a port forrás, amely az SQL Server figyel az engedélyezési listáját tartalmazza. Alapértelmezés szerint a 1433-as porton, de a forrás SQL Server konfigurálhatók úgy, hogy más porton figyeljen. Ebben az esetben kell ezeket a portokat, valamint az engedélyezési listán szereplő tartalmazza. Megadhatja, hogy a port, amelyet az SQL Server dinamikus felügyeleti nézet lekérdezéssel figyel:

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
Azt is meghatározhatja, hogy a port, amelyet az SQL Server az SQL Server hibanaplójában lekérdezésével figyeli:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>K. Javaslatok az Azure Database Migration Service teljesítményének optimalizálásáért vannak?
Lehetőség van néhány az adatbázis-migrálást a szolgáltatás használatával felgyorsítása érdekében:
- A többszörös CPU általános célú Tarifacsomag használja, a szolgáltatáspéldány, hogy a szolgáltatás előnyeit a párhuzamos feldolgozás és gyorsabb adatátvitelt több vcpu-k létrehozásakor.
- Ideiglenesen fel a cél Azure SQL Database példányt során az áttelepítési művelet minimalizálása érdekében a szabályozás, az Azure SQL Database prémium szintű Termékváltozat méretezési hatással lehet az átvitel tevékenységeket alacsonyabb szintű termékváltozatok használata esetén.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>K. Hogyan állíthatok be egy Azure virtuális hálózaton?
Több Microsoft-oktatóprogram végigvezeti az egy Azure virtuális hálózat beállításának folyamatán, miközben a hivatalos dokumentáció jelenik meg a cikk [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>K. Nem érhető el, vagy leállt, mi az Azure Database Migration Service?
Ha a felhasználónak explicit módon leállítja az Azure Database Migration Service (DMS), vagy ha egy 24 órás időszakban a szolgáltatás inaktív, a szolgáltatás el egy leállított vagy szüneteltetett állapot automatikus. Minden esetben a szolgáltatás nem érhető el, és a egy leállított állapotban lesz.  Az aktív-migrálások folytatásához, indítsa újra a szolgáltatást.

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>K. Ahol hagyhatja az Azure Database Migration Service visszajelzést?
Szívesen meghallgatnánk a véleményét. Küldjön visszajelzést és / ötleteire van az Azure Database Migration Service User Voice, keresztül kapcsolatban [Itt](https://feedback.azure.com/forums/906100-azure-database-migration-service).

## <a name="next-steps"></a>További lépések
Az Azure Database Migration Service és a régiónkénti rendelkezésre állás áttekintését lásd: a cikk [Mi az az Azure Database Migration Service](dms-overview.md). 
