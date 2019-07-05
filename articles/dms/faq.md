---
title: Az Azure Database Migration Service használatával kapcsolatos gyakori kérdések |} A Microsoft Docs
description: Ismerje meg, Azure Database Migration Service-adatbázis-migrálási parancsait végrehajtására kapcsolatos gyakori kérdésekre.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/04/2019
ms.openlocfilehash: f59af0bd0297b0413673fc42b26654c36351b815
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566240"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Azure Database Migration Service használatával kapcsolatos gyakori kérdések

Ez a cikk felsorolja a kapcsolódó válaszokat együtt az Azure Database Migration Service használatával kapcsolatos gyakori kérdésekre.

### <a name="q-what-is-azure-database-migration-service"></a>K. Mi az Azure Database Migration Service?

Azure Database Migration Service egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a zökkenőmentes migrálást a több adatbázis-forrásokhoz, az Azure-adatplatformokra minimális állásidővel. A szolgáltatás jelenleg általánosan elérhető az összpontosított a folyamatos fejlesztéshez:

* Megbízhatóságát és teljesítményét.
* Forrás – cél párok migrációját iteratív hozzáadásával.
* Folyamatos befektetést áttelepítések fennakadások nélkül használható.

### <a name="q-what-source-target-pairs-does-azure-database-migration-service-currently-support"></a>K. Milyen forrás – cél párok migrációját Azure Database Migration Service jelenleg támogatja?

A szolgáltatás jelenleg támogatja a különböző áttelepítési forgatókönyveinek. Állapotát az egyes érhető el áttelepítési forgatókönyvek teljes listáját lásd: a cikk [állapotát az Azure Database Migration Service által támogatott áttelepítési forgatókönyvek](https://docs.microsoft.com/azure/dms/resource-scenario-status). Más áttelepítési forgatókönyvek előzetes verzióként érhetők el, és a DMS Preview-helyeken keresztül nevezés beküldése megkövetelése. Az előzetes verzióban érhető el, és ezeket az ajánlatokat részt regisztráció forgatókönyvek teljes listáját lásd: a [DMS Preview-hely](https://aka.ms/dms-preview/).

### <a name="q-how-does-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>K. Azure Database Migration Service összehasonlítása a más Microsoft adatbázis-áttelepítési eszközök például a Database Migration Assistant (DMA) vagy az SQL Server Migrálási Assistant (SSMA)

Azure Database Migration Service legyen az előnyben részesített módszere ipari méretekben a Microsoft Azure adatbázis-migrálást. További részletek az Azure Database Migration Service hogyan hasonlítja össze a más Microsoft-adatbázis-áttelepítési eszközök és kapcsolatos javaslatokat a szolgáltatás különböző forgatókönyvek segítségével, tekintse meg a blog könyvelés [sokoldalúbbá Microsoft adatbázis-Migrálás Eszközök és szolgáltatások](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

### <a name="q-how-does-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>K. Azure Database Migration Service összehasonlítása, az Azure Migrate ajánlat

Az Azure Migrate segít a migrálás a helyszíni virtuális gépek az Azure iaas-ben. A szolgáltatás felméri a migrálásra való alkalmasságát, és a teljesítményalapú, és költségbecslések a helyszíni virtuális gépek futtatásához az Azure biztosít. Az Azure Migrate hasznos lift-and-shift áttelepítéséhez a helyszíni Virtuálisgép-alapú számítási feladatok Azure IaaS virtuális gépekre. Azonban eltérően az Azure Database Migration Service, Azure Migrate nem egy speciális adatbázis-áttelepítési szolgáltatás, amely az Azure PaaS relációs adatbázis platformokhoz, mint az Azure SQL Database vagy Azure SQL Database felügyelt példányain.

### <a name="q-what-versions-of-sql-server-does-azure-database-migration-service-support-as-a-source"></a>K. Az SQL Server mely verzióit támogatja az Azure Database Migration Service forrásként?

Ha SQL Server-ről, Azure Database Migration Service támogatja a SQL Server 2005, SQL Server 2017-ig.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-azure-database-migration-service-to-perform-a-database-migration"></a>K. Mi az Azure Database Migration Service használatához egy adatbázis-áttelepítés végrehajtásához szükséges lépések összefoglalása?

Egy tipikus, egyszerű adatbázist a migrálás során meg:

1. Hozzon létre egy cél-adatbázisokat.
2. Az adatbázis-séma áttelepítése a [Database Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3. Hozzon létre egy Azure Database Migration Service példányát.
4. Hozzon létre egy migrálási projekt megadása a forrásadatbázis (ok), a cél adatbázisának/adatbázisainak szolgáltatásparitási és a táblák áttelepíteni.
5. Indítsa el a teljes terhelés.
6. Válassza ki az ezt követő érvényesítése.
7. Hajtsa végre a manuális Váltás az éles környezet az új felhőalapú adatbázishoz.

### <a name="q-what-are-the-prerequisites-for-using-azure-database-migration-service"></a>K. Mik az Azure Database Migration Service használatának előfeltételei?

Nincsenek győződjön meg arról, hogy az Azure Database Migration Service zökkenőmentesen adatbázis-migrálások végrehajtása során kell néhány előfeltételnek. Egyes Előfeltételek a alkalmazni közben egyéb előfeltételek egyediek egy adott forgatókönyv a szolgáltatás által támogatott összes forgatókönyv (forrás-cél párok) között.

Az Azure Database Migration Service előfeltételei, amelyek közösek az összes támogatott áttelepítési forgatókönyvek között kell tartalmaznia:

* Azure Database Migration Service virtuális hálózat létrehozása helyek közötti kapcsolatot biztosít annak a helyszíni adatforrás-kiszolgálók használatával az Azure Resource Manager üzemi modell használatával [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Ellenőrizze, hogy az Azure Virtual Network (VNet) hálózati biztonsági csoport szabályai nem blokkolja a következő kommunikációs portokat a 443-as, 53-as és 9354-es, 445-ös, 12000. Az Azure VNet NSG-forgalom szűrése további részletekért tekintse meg a cikket [hálózati biztonsági csoportokkal a hálózati forgalom szűrése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* A forrásadatbázis (ok) elé egy tűzfalkészülék használata esetén előfordulhat, hogy hozzá kell tűzfalszabályokban, Azure Database Migration Service az áttelepítéshez a forrásadatbázis (ok) eléréséhez.

Jelenlegi versenyének adott áttelepítési forgatókönyvek használatával az Azure Database Migration Service szükséges összes előfeltételt listáját lásd: a kapcsolódó oktatóanyagok az Azure Database Migration Service [dokumentáció](https://docs.microsoft.com/azure/dms/dms-overview) a docs.microsoft.com webhelyen.

### <a name="q-how-do-i-find-the-ip-address-for-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>K. Hogyan találhatom meg az IP-cím az Azure Database Migration Service úgy, hogy létre tudok hozni egy engedélyezési listája a tűzfalszabályok az áttelepítéshez a forrás-adatbázis eléréséhez használt?

Előfordulhat, hogy a tűzfalszabályok engedélyezése az Azure Database Migration Service eléréséhez a forráshely adatbázisára az áttelepítéshez hozzá kell. A szolgáltatás IP-címét a dinamikus, de ha Express Route használja, ezt a címet közvetlenül a Microsoftnak a vállalati hálózat által hozzárendelt. Azonosítsa a megfelelő IP-címet a legegyszerűbb módja, hogy ugyanazt az erőforráscsoportot és a kiépített Azure Database Migration Service-erőforrásnak a társított hálózati adapter található hely. A hálózati adapter erőforrás neve általában a hálózati adapter előtaggal kezdődik, és egy egyedi karaktert, és a sorrend, például NIC-jj6tnztnmarpsskr82rbndyp követ. Az IP-cím, amelyet a portál oldala az Azure erőforrás-áttekintés az engedélyezési listán szereplő megjelenik a hálózati adapter erőforrás kijelölésével.

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

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>K. Hogyan állíthatok be egy Azure virtuális hálózaton?

Több Microsoft-oktatóprogram végigvezeti az egy Azure virtuális hálózat beállításának folyamatán, miközben a hivatalos dokumentáció jelenik meg a cikk [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).


### <a name="q-im-setting-up-a-migration-project-in-dms-and-im-having-difficulty-connecting-to-my-source-database-what-should-i-do"></a>K. A DMS a migrálási projekt beállítom, és léptek fel a csatlakozni kívánó a forráshely adatbázisára. Mit tegyek?

Ha nem lehet csatlakozni a forrásrendszerben adatbázis munka az áttelepítés közben, hozzon létre egy virtuális gépet, amellyel állítsa be a DMS-példány a virtuális hálózaton. A virtuális gépen futtatható a connect egy tesztet, például a MongoDB-kapcsolatok tesztelése Robo 3T letöltés vagy UDL-fájl használatával tesztelje a kapcsolatot az SQL Server kell lennie. Ha a kapcsolat tesztelése sikeres volt, hogy nem a forrásadatbázis való csatlakozás hibája. Ha a kapcsolat tesztelése nem sikerül, forduljon a hálózati rendszergazdához.

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-azure-database-migration-service"></a>K. Azure Database Migration Service teljesítményének optimalizálásáért javaslatok vannak?

Lehetőség van néhány az adatbázis-migrálást a szolgáltatás használatával felgyorsítása érdekében:

* A többszörös CPU általános célú Tarifacsomag használja, a szolgáltatáspéldány, hogy a szolgáltatás előnyeit a párhuzamos feldolgozás és gyorsabb adatátvitelt több vcpu-k létrehozásakor.
* Ideiglenesen fel a cél Azure SQL Database példányt során az áttelepítési művelet minimalizálása érdekében a szabályozás, az Azure SQL Database prémium szintű Termékváltozat méretezési hatással lehet az átvitel tevékenységeket alacsonyabb szintű termékváltozatok használata esetén.

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>K. Nem érhető el, vagy leállt, mi az Azure Database Migration Service?

Ha a felhasználónak explicit módon leállítja az Azure Database Migration Service (DMS), vagy ha egy 24 órás időszakban a szolgáltatás inaktív, a szolgáltatás el egy leállított vagy szüneteltetett állapot automatikus. Minden esetben a szolgáltatás nem érhető el, és a egy leállított állapotban lesz.  Az aktív-migrálások folytatásához, indítsa újra a szolgáltatást.

### <a name="q-where-can-i-leave-feedback-about-azure-database-migration-service"></a>K. Ahol hagyhatja az Azure Database Migration Service visszajelzést?

Szívesen meghallgatnánk a véleményét. Küldjön visszajelzést és / ötleteire van az Azure Database Migration Service User Voice, keresztül kapcsolatban [Itt](https://feedback.azure.com/forums/906100-azure-database-migration-service), vagy kapcsolatba lép a csapatának [kérje meg az Azure adatbázis-Migrálási](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="next-steps"></a>További lépések

Az Azure Database Migration Service és a régiónkénti rendelkezésre állás áttekintését lásd: a cikk [Mi az az Azure Database Migration Service](dms-overview.md).
