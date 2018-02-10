---
title: "Gyakori kérdések az Azure-adatbázis áttelepítése szolgáltatással |} Microsoft Docs"
description: "Ismerje meg az Azure-adatbázis áttelepítési szolgáltatás adatbázis-áttelepítés végrehajtásának használatával kapcsolatos gyakori kérdésekre."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: 3c1c259cc58eb1adab39d9c0ca376726b798186e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Az Azure-adatbázis áttelepítése szolgáltatással kapcsolatos gyakori kérdések
Ez a cikk felsorolja a Azure adatbázis áttelepítési szolgáltatás és a kapcsolódó válaszok együttes használatával kapcsolatos gyakori kérdésekre.

### <a name="q-what-is-azure-database-migration-service"></a>Q. Mi az Azure-adatbázis áttelepítése szolgáltatás?
Az Azure-adatbázis áttelepítési szolgáltatás egy olyan teljes körűen felügyelt szolgáltatás lehetővé minimális állásidővel adatok Azure platformon több adatbázis forrásból zökkenőmentes áttelepítés. A szolgáltatás a fejlesztéshez arra irányul, hogy a jelenleg a Public Preview:
- Megbízhatóságát és teljesítményét.
- Forrás-cél párok iteratív hozzáadását.
- Folyamatos beruházást súrlódás szabad áttelepítéseket.

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>Q. Milyen forrás-cél párok az Azure-adatbázis áttelepítési szolgáltatás jelenleg támogatja?
A szolgáltatás a nyilvános előzetes verzió jelenleg áttelepítése az SQL Serverről az Azure SQL Database, és az Azure-portálon most ehhez a forgatókönyvhöz az Azure-adatbázis áttelepítési szolgáltatás használatának megkezdéséhez lépjen. Más, a forrás-cél párok, például az Azure SQL adatbázis felügyelt példány SQL Server és Oracle az Azure SQL Database egy korlátozott Private Preview verziójára keresztül érhetők el. Ezekkel az esetekkel korlátozott Private Preview verziójára részt lehetőség, iratkozzon fel [Itt](https://sqldatabase-migrationpreview.azurewebsites.net/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>Q. Hogyan hasonlítsa össze az Azure-adatbázis áttelepítési szolgáltatás más Microsoft adatbázis áttelepítési eszközök például az adatbázis áttelepítési Segéd (DMA-) vagy az SQL Server áttelepítés Segéd (SSMA)?
Az Azure-adatbázis áttelepítési szolgáltatás legyen az előnyben részesített módszere léptékű adatbázis a Microsoft Azure áttelepítés. További részleteket az Azure-adatbázis áttelepítési szolgáltatás hogyan összehasonlítja más Microsoft-adatbázis-áttelepítési eszközök, és több, különböző esetekre szolgáltatással kapcsolatos javaslatok, lásd: a blog könyvelés [lehetővé tevő Microsoft adatbázis Áttelepítési eszközök és szolgáltatások](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>Q. Hogyan hasonlítsa össze az Azure-adatbázis áttelepítési szolgáltatás az Azure áttelepítése elérhető?
Az Azure áttelepítése szolgáltatás segítséget nyújt a helyszíni Azure IaaS virtuális gépek áttelepítéséhez. A szolgáltatás értékeli az áttelepítési megfelelőség és a teljesítmény-alapú méretezési, és a becsült költség biztosít a helyszíni virtuális gépek Azure-ban futó. Az Azure áttelepítése akkor hasznos, a növekedési és shift helyszíni Virtuálisgép-alapú munkaterhelések Azure IaaS virtuális gépek áttelepítéséhez. Azonban eltérően az Azure adatbázis áttelepítési szolgáltatás Azure áttelepítése nem egy speciális adatbázis áttelepítési szolgáltatás Azure PaaS relációs adatbázis platformokon, például az Azure SQL Database vagy az SQL Azure vagy Azure SQL adatbázis felügyelt példány számára.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>Q. Mi az, hogy egy adatbázis áttelepítése az Azure-adatbázis áttelepítés szolgáltatás használatához szükséges lépések összefoglalása?
Egy tipikus, egyszerű adatbázis az áttelepítés során meg:
1.  Hozzon létre egy cél adatbázisra.
2.  Telepítse át az adatbázis-séma segítségével a [adatbázis áttelepítési Segéd](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3.  Az Azure-adatbázis áttelepítési szolgáltatás egy példányának létrehozásakor.
4.  A forrás adatbázisának vagy adatbázisainak, cél adatbázis (oka) és egyéb táblák áttelepítése áttelepítési projekt létrehozása.
5.  Indítsa el a teljes terhelést.
6.  Válassza ki a következő érvényesítési.
7.  Hajtsa végre a manuális Váltás az éles környezet az új felhőalapú adatbázishoz. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>Q. Mik az Azure-adatbázis áttelepítési szolgáltatás használatára vonatkozó Előfeltételek?
Győződjön meg arról, hogy az Azure-adatbázis áttelepítési szolgáltatás zökkenőmentesen adatbázis áttelepítések végrehajtása során több előfeltételei van. Összes forgatókönyv (a forrás-cél párok) szolgáltatás által támogatott, amíg más Előfeltételek egyediek-e egy adott forgatókönyvhöz néhányat az Előfeltételek vonatkoznak.
Azure-adatbázis áttelepítése szolgáltatás Előfeltételek az összes támogatott áttelepítési forgatókönyvek által közösen használt tartalmaznia kell:
- Az Azure Resource Manager telepítési modell, amely webhelyek kapcsolatot biztosít annak a helyszíni adatforrás-kiszolgálók használatával vagy használatával hozhat létre egy VNETET az Azure-adatbázis áttelepítése szolgáltatás [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Győződjön meg arról, hogy az Azure virtuális hálózatot (VNET) hálózati biztonsági csoport szabályok tegye letiltása a következő kommunikációs portok 443-as, 53-as és 9354-es, 445-ös, 12000. További részletek az Azure VNET NSG forgalomszűrést végez, olvassa el a [hálózati forgalmat hálózati biztonsági csoportokkal](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Egy tűzfal készülék elé a forrás-adatbázis használata esetén szükség lehet ahhoz, hogy az Azure adatbázis áttelepítése az áttelepítés forrás adatbázis(ok) eléréséhez Tűzfalszabályok hozzáadása.
 
Minden a előfeltételei "versenyeznek" az Azure-adatbázis áttelepítése szolgáltatással adott áttelepítési forgatókönyvek listáját lásd: a kapcsolódó oktatóanyagok az Azure áttelepítési szolgáltatásban [dokumentáció](https://docs.microsoft.com/en-us/azure/dms/dms-overview) a a docs.microsoft.com webhelyen.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>Q. Hogyan találhatom az IP-cím az Azure-adatbázis áttelepítése szolgáltatás, hogy létre tudok hozni egy engedélyezési listája az áttelepítés forrás adatbázis eléréséhez használt tűzfalszabályok?
Szükség lehet hozzáadni a tűzfalszabályok engedélyezése a forráshely adatbázisára vonatkozó áttelepítési eléréséhez Azure adatbázis áttelepítési szolgáltatás. A szolgáltatás az IP-cím dinamikus, de az Express Route használ, ha a cím közvetlenül hozzá van rendelve a vállalati hálózaton. A legegyszerűbben úgy, hogy azonosítsa a megfelelő IP-cím ugyanabban az erőforráscsoportban a kiépített Azure adatbázis áttelepítési szolgáltatás erőforrásként a társított felülettel kereséséhez keresse meg a legyen. A hálózati illesztő erőforrás neve általában a hálózati adapter előtaggal kezdődik, és egy egyedi karaktert és számsorozatot, például NIC-jj6tnztnmarpsskr82rbndyp követ. A hálózati illesztő erőforrás kiválasztásával megtekintheti az IP-cím, amely szerepel az engedélyezési listán az erőforrás-áttekintés az Azure portálon a szerepelnie kell.

Is szükség lehet, hogy az SQL Server nem az engedélyezési listán port forrását tartalmazza. Alapértelmezés szerint 1433-as port, de az adatforrás SQL Server konfigurálhatók úgy, hogy más porton figyeljen. Ebben az esetben meg kell adnia azokat a portokat, valamint az engedélyezési listán szereplő. Meghatározhatja, hogy a portot, amelyet az SQL Server dinamikus Kezelőnézetet lekérdezéssel figyel:

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
Azt is meghatározhatja, hogy a port, amelyet az SQL Server az SQL Server hibanaplójában lekérdezésével figyel:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>Q. Vannak-e az Azure-adatbázis áttelepítési szolgáltatás teljesítményének növelése javaslatokkal?
Az adatbázis áttelepítést, a szolgáltatás használatával felgyorsítása érdekében néhány műveleteket végezheti el:
- Használja a többszörös CPU általános célú árképzési szintjében, ahhoz, hogy a előnyeit több Vcpu gyorsabb adatátvitelt és párhuzamos folyamatkezelést biztosítja a szolgáltatáspéldány létrehozásakor.
- Az Azure SQL Database célpéldány prémium tarifacsomagra SKU szabályozás, az Azure SQL Database minimalizálása érdekében az áttelepítési művelet során, felskálázott ideiglenesen befolyásolhatja adatok fájlátviteli tevékenységeket, alacsonyabb szintű termékváltozatok használatakor.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>Q. Hogyan állíthatom be egy Azure virtuális hálózatot?
Több Microsoft-oktatóanyagokat is ismerteti, hogyan kell beállítani egy Azure virtuális Hálózatot a folyamatot, miközben a dokumentációs jelenik meg a cikk [Azure Virtual Network](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview).

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>Q. Az Azure-adatbázis áttelepítési szolgáltatás miért van, nem érhető el vagy leállított?
Ha a felhasználónak explicit módon leállítja az Azure adatbázis áttelepítési szolgáltatás (DMS), vagy ha egy 24 órás időszakban a szolgáltatás inaktív, a szolgáltatás lennie egy leállított vagy szüneteltetett állapot automatikus. Minden esetben a szolgáltatás nem érhető el, és leállított állapotban lesz.  Aktív áttelepítést folytatásához indítsa újra a szolgáltatást.

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>Q. Ha hagyhatja az Azure-adatbázis áttelepítési szolgáltatás visszajelzést?
Azt szeretnénk, a véleményét. Küldjön egy olyan visszajelzést és ötleteket rendelkezik a Azure-adatbázis áttelepítési keresztül User Voice szolgáltatással kapcsolatos / [Itt](https://feedback.azure.com/forums/906100-azure-database-migration-service).

## <a name="next-steps"></a>További lépések
Az Azure-adatbázis áttelepítése szolgáltatás és a nyilvános előzetes regionális rendelkezésre állási áttekintését lásd: a cikk [Mi az az Azure adatbázis áttelepítési szolgáltatás előzetes](dms-overview.md). 