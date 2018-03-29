---
title: Az Azure-adatbázis áttelepítési szolgáltatás használatára vonatkozó előfeltételek áttekintése |} Microsoft Docs
description: Ismerje meg az adatbázis-áttelepítés végrehajtásának az Azure-adatbázis áttelepítési szolgáltatás használatára vonatkozó előfeltételek áttekintése.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/29/2018
ms.openlocfilehash: 536a75118e1005ddc0bb5814e2367e1ced808305
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Az Azure-adatbázis áttelepítési szolgáltatás használatára vonatkozó előfeltételek áttekintése
Győződjön meg arról, hogy az Azure-adatbázis áttelepítési szolgáltatás zökkenőmentesen adatbázis áttelepítések végrehajtása során több előfeltételei van. Összes forgatókönyv (a forrás-cél párok) szolgáltatás által támogatott, amíg más Előfeltételek egyediek-e egy adott forgatókönyvhöz néhányat az Előfeltételek vonatkoznak.

Előfeltételek az Azure-adatbázis áttelepítése szolgáltatással társított a következő szakaszokban találhatók.

## <a name="prerequisites-common-across-migration-scenarios"></a>Áttelepítési forgatókönyvek között közös Előfeltételek
Azure-adatbázis áttelepítése szolgáltatás Előfeltételek az összes támogatott áttelepítési forgatókönyvek által közösen használt tartalmaznia kell:
- Az Azure Resource Manager telepítési modell, amely webhelyek kapcsolatot biztosít annak a helyszíni adatforrás-kiszolgálók használatával vagy használatával hozhat létre egy VNETET az Azure-adatbázis áttelepítése szolgáltatás [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Győződjön meg arról, hogy az Azure virtuális hálózatot (VNET) hálózati biztonsági csoport szabályok tegye letiltása a következő kommunikációs portok 443-as, 53-as és 9354-es, 445-ös, 12000. További részletek az Azure VNET NSG forgalomszűrést végez, olvassa el a [hálózati forgalmat hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Egy tűzfal készülék elé a forrás-adatbázis használata esetén szükség lehet ahhoz, hogy az Azure adatbázis áttelepítése az áttelepítés forrás adatbázis(ok) eléréséhez Tűzfalszabályok hozzáadása.
- Konfigurálja a [Windows tűzfalat a hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Engedélyezze a TCP/IP protokollt, amely le van tiltva alapértelmezés szerint az SQL Server Express telepítése során az a cikk utasításait követve [engedélyezheti vagy tilthatja le a hálózati protokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>SQL-kiszolgáló áttelepítése az Azure SQL Database előfeltételei 
Azure-adatbázis áttelepítése szolgáltatás Előfeltételek, amelyek közösek az összes áttelepítési forgatókönyvek kívül is kifejezetten egy forgatókönyvet, vagy egy másik érvényes előfeltételekkel.

SQL Server az Azure SQL Database áttelepítésre, mellett az előfeltételeket, amelyeket megegyeznek az összes áttelepítési forgatókönyv végrehajtásához az Azure-adatbázis áttelepítési szolgáltatás használata esetén ügyeljen arra, hogy a cím a következő további Előfeltételek:

- Hozzon létre egy Azure SQL Database-példányt, amit a C-cikkben található részletes követve példányát[Azure SQL-adatbázis létrehozása az Azure portálon](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Töltse le és telepítse a [adatok áttelepítési Segéd](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 vagy újabb.
- Nyissa meg a Windows tűzfalat ahhoz, hogy az Azure adatbázis áttelepítése az SQL-kiszolgálón, amely alapértelmezés szerint 1433-as TCP-port forrás eléréséhez.
- Ha több elnevezett SQL Server-példány dinamikus portok használatával futtatja, előfordulhat, hogy kívánja az SQL Browser szolgáltatás engedélyezése és 1434 UDP-portot a tűzfalon keresztüli hozzáférés engedélyezése, hogy az Azure-adatbázis áttelepítési szolgáltatás csatlakozhat a forrás nevesített példány a kiszolgáló.
- Hozzon létre egy kiszolgálószintű [tűzfalszabály](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure SQL Database-kiszolgáló eléréséhez az Azure-adatbázis áttelepítése szolgáltatás a céladatbázisokhoz. Adja meg a virtuális hálózat az Azure-adatbázis áttelepítése szolgáltatáshoz használt alhálózati tartományán.
- Győződjön meg arról, hogy rendelkezik-e az adatforrás SQL Server-példányhoz való csatlakozásnál használt hitelesítő adatok [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedélyek.
- Győződjön meg arról, hogy rendelkezik-e a cél Azure SQL Database-példányt való kapcsolódáshoz használt hitelesítő adatok FELADATVEZÉRLŐ ADATBÁZISHOZ engedéllyel a cél Azure SQL-adatbázisok.

   > [!NOTE]
   > Az áttelepítés végrehajtásának az SQL Serverről az Azure SQL Database az Azure-adatbázis áttelepítés szolgáltatás használatához szükséges előfeltételeket teljes listáját lásd az oktatóanyag [SQL Server áttelepítése az Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database-managed-instance"></a>Azure SQL adatbázis felügyelt példányra áttelepítése az SQL Server előfeltételei
- Azure SQL adatbázis felügyelt példány példányt létrehozni a következő cikkben található részletes [hozzon létre egy Azure SQL adatbázis-felügyelt példányt az Azure-portálon](https://aka.ms/sqldbmi).
- Nyissa meg a tűzfalat, hogy az SMB-forgalmat engedélyezi a 445-ös port az Azure adatbázis áttelepítési szolgáltatás IP-címet vagy alhálózatot tartomány.
- Nyissa meg a Windows tűzfalat ahhoz, hogy az Azure adatbázis áttelepítése az SQL-kiszolgálón, amely alapértelmezés szerint 1433-as TCP-port forrás eléréséhez.
- Ha több elnevezett SQL Server-példány dinamikus portok használatával futtatja, előfordulhat, hogy kívánja az SQL Browser szolgáltatás engedélyezése és 1434 UDP-portot a tűzfalon keresztüli hozzáférés engedélyezése, hogy az Azure-adatbázis áttelepítési szolgáltatás csatlakozhat a forrás nevesített példány a kiszolgáló.
- Győződjön meg arról, hogy a használnak az adatforrás SQL Server és felügyelt célpéldányának bejelentkezések a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
- Hozzon létre egy hálózati megosztást, amelyet az Azure-adatbázis áttelepítési szolgáltatás segítségével a forrás-adatbázis biztonsági mentése.
- Győződjön meg arról, hogy a szolgáltatás az adatforrás SQL Server-példányt futtató fióknak írási jogosultságokkal a létrehozott hálózati megosztáson.
- Jegyezze fel a Windows-felhasználó (és jelszó), amely teljes hozzáférési jogosultsággal rendelkezik a fenti létrehozott hálózati megosztáson. Az Azure-adatbázis áttelepítési szolgáltatás a biztonsági mentési fájlok feltöltése az Azure storage-tároló visszaállítási művelet a felhasználó hitelesítő adatainak megszemélyesít.
- A blob-tároló létrehozása és a SAS URI-JÁNAK beolvasása a cikkben szereplő lépések segítségével [kezelése az Azure Blob Storage-erőforrások a Tártallózó (előzetes verzió)](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Győződjön meg arról, jelölje be az összes engedélyt (olvasási, írási, törlés, lista) házirend ablakban SAS URI létrehozása során.

   > [!NOTE]
   > Az áttelepítés végrehajtásának az SQL Serverről az Azure SQL Database-felügyelt példányt az Azure-adatbázis áttelepítés szolgáltatás használatához szükséges előfeltételeket teljes listáját lásd az oktatóanyag [áttelepítése az SQL Server Azure SQL adatbázis felügyelt példányra ](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>További lépések
Az Azure-adatbázis áttelepítése szolgáltatás és a nyilvános előzetes regionális rendelkezésre állási áttekintését lásd: a cikk [Mi az az Azure adatbázis áttelepítési szolgáltatás előzetes](dms-overview.md). 