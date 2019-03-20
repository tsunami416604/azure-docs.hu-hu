---
title: Az Azure Database Migration Service használatára vonatkozó előfeltételek áttekintése |} A Microsoft Docs
description: További információ az adatbázis-migrálási parancsait végrehajtásához az Azure Database Migration Service használatára vonatkozó előfeltételek áttekintése.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 6e26ae7dd8637a71b9f4e2f6ea1ed6063cf0ec40
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181197"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Az Azure Database Migration Service használatára vonatkozó előfeltételek áttekintése
Nincsenek győződjön meg arról, hogy az Azure Database Migration Service zökkenőmentesen adatbázis-migrálások végrehajtása során kell néhány előfeltételnek. Egyes Előfeltételek a alkalmazni közben egyéb előfeltételek egyediek egy adott forgatókönyv a szolgáltatás által támogatott összes forgatókönyv (forrás-cél párok) között.

Az Azure Database Migration Service segítségével kapcsolódó előfeltételeket az alábbi szakaszokban található.

## <a name="prerequisites-common-across-migration-scenarios"></a>Gyakori áttelepítési forgatókönyvek között Előfeltételek
Az Azure Database Migration Service előfeltételei, amelyek közösek az összes támogatott áttelepítési forgatókönyvek között kell tartalmaznia:
- Hozzon létre egy virtuális hálózatot az Azure Database Migration Service-hez az Azure Resource Manager-alapú üzemi modell használatával, amely a hálózat helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgálóknak [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) használatával.
- Győződjön meg arról, hogy az Azure Virtual Network (VNET) hálózati biztonsági szabályok nem blokkolják a következő kommunikációs portokat: 443, 53, 9354, 445, 12000. További részletek az Azure VNET NSG-forgalom szűréséről: [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
- Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Engedélyezze a TCP/IP protokollt, amely az SQL Server Express telepítése során alapértelmezés szerint le van tiltva – kövesse a [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) cikk utasításait.

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Az Azure SQL Database áttelepítése az SQL Server vonatkozó Előfeltételek 
Azure Database Migration Service Előfeltételek, amelyek közösek az összes áttelepítési forgatókönyvek kívül is kifejezetten egy forgatókönyvet vagy egy másik érvényes előfeltételekkel.

Azure SQL Database áttelepítések, amelyek közösek az összes áttelepítési forgatókönyvek esetén is telepítheti az SQL Server az Azure Database Migration Service használata esetén ügyeljen arra, hogy oldja meg a következő további Előfeltételek vonatkoznak:

- Hozzon létre egy példányt az Azure SQL Database-példány, amelyeket a következő a részletek a következő cikkben: C[egy Azure SQL database létrehozása az Azure Portalon](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Töltse le a [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3-as vagy újabb verzióját.
- Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a forrásul szolgáló SQL Servert, amely alapértelmezés szerint az 1433-as TCP-porton található.
- Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
- Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure), hogy az Azure SQL Database Migration Service hozzáférhessen a céladatbázisokhoz. Adja meg az Azure Database Migration Service-hez használt virtuális hálózat alhálózati tartományát.
- Gondoskodjon róla, hogy a forrásként szolgáló SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedélyekkel.
- Gondoskodjon róla, hogy a forrásként szolgáló Azure SQL Database-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek CONTROL DATABASE engedéllyel a célként szolgáló Azure SQL adatbázisokban.

   > [!NOTE]
   > Az áttelepítések végrehajtásához az SQL Serverről az Azure SQL Database az Azure Database Migration Service használatához szükséges előfeltételeket teljes listáját lásd a következő oktatóanyagot [SQL Server Migrálása az Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database-managed-instance"></a>Az Azure SQL Database felügyelt példányába történő áttelepítése az SQL Server vonatkozó Előfeltételek
- Hozzon létre egy példányát az Azure SQL Database felügyelt példánya a következő a részletek a cikkben [egy Azure SQL Database felügyelt példány létrehozása az Azure Portalon](https://aka.ms/sqldbmi).
- Nyissa meg a tűzfal az Azure adatbázis Migrálási szolgáltatás IP-cím vagy alhálózat címtartománya a 445-ös port az SMB-forgalom engedélyezéséhez.
- Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a forrásul szolgáló SQL Servert, amely alapértelmezés szerint az 1433-as TCP-porton található.
- Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
- Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példány és a célként szolgáló felügyelt példány összekapcsolásához használt bejelentkezési adatok mind a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
- Hozzon létre egy hálózati megosztást, amelyet az Azure Database Migration Service használhat a forrásként szolgáló adatbázis biztonsági mentésére.
- Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példányt futtató szolgáltatásfiók írási, a forrásként szolgáló kiszolgáló számítógépes fiókja pedig olvasási és írási jogosultságokkal rendelkezik az Ön által létrehozott hálózati megosztáson.
- Jegyezzen fel egy olyan Windows-felhasználót (és jelszót), amely teljes körű jogosultságokkal rendelkezik az Ön által korábban létrehozott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy fel tudja tölteni a biztonsági mentési fájlokat az Azure Storage-tárolóba a visszaállítási művelethez.
- Hozzon létre egy blobtárolót és az SAS URI lekérni a cikkben leírt lépések végrehajtásával [a Storage Explorer használatával kezelheti az Azure Blob Storage-erőforrások](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Ügyeljen arra, hogy minden engedéllyel (olvasás, írás, törlés, List) házirend ablakban válassza a SAS URI létrehozása során.

   > [!NOTE]
   > Az Azure Database Migration Service segítségével áttelepítések hajtsa végre az SQL Serverről Azure SQL Database felügyelt példányába történő szükséges előfeltételek teljes listáját lásd a következő oktatóanyagot [SQL Server Migrálása az Azure SQL Database felügyelt példánya ](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>További lépések
Az Azure Database Migration Service és a régiónkénti rendelkezésre állás áttekintését lásd: a cikk [Mi az az Azure Database Migration Service](dms-overview.md). 