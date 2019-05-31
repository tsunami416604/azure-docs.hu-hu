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
ms.date: 05/29/2019
ms.openlocfilehash: 4e21014f7b4ed86846a100ed9a2b1cd4b0400974
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304258"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Az Azure Database Migration Service használatára vonatkozó előfeltételek áttekintése

Nincsenek több előfeltételt is szükséges, hogy az Azure Database Migration Service problémamentesen fut, adatbázis-migrálások végrehajtása során. Egyes Előfeltételek a alkalmazni közben egyéb előfeltételek egyediek egy adott forgatókönyv a szolgáltatás által támogatott összes forgatókönyv (forrás-cél párok) között.

Az Azure Database Migration Service segítségével kapcsolódó előfeltételeket az alábbi szakaszokban található.

## <a name="prerequisites-common-across-migration-scenarios"></a>Gyakori áttelepítési forgatókönyvek között Előfeltételek

Az Azure Database Migration Service előfeltételei, amelyek közösek az összes támogatott áttelepítési forgatókönyvek között kell tartalmaznia:

* Hozzon létre egy Azure virtuális hálózaton (VNet) az Azure Resource Manager üzembe helyezési modell, amely lehetővé teszi a helyek közötti kapcsolatot a helyszíni adatforrás-kiszolgálók használatával vagy az Azure Database Migration Service [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Győződjön meg arról, hogy a virtuális hálózatok közötti hálózati biztonsági csoport (NSG) szabályai nem blokkolják a következő kommunikációs portokat a 443-as, 53-as és 9354-es, 445-ös, 12000. Az Azure VNet NSG-forgalom szűrése további részletekért tekintse meg a cikket [hálózati biztonsági csoportokkal a hálózati forgalom szűrése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* A forrásadatbázis (ok) elé egy tűzfalkészülék használata esetén előfordulhat, hogy hozzá kell tűzfalszabályokban, Azure Database Migration Service az áttelepítéshez a forrásadatbázis (ok) eléréséhez.
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Engedélyezze a TCP/IP protokollt, amely az SQL Server Express telepítése során alapértelmezés szerint le van tiltva – kövesse a [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) cikk utasításait.

    > [!IMPORTANT]
    > Hozzon létre egy Azure Database Migration Service-példányt a virtuális hálózat beállításait, amelyet normális esetben nem ugyanabba az erőforráscsoportba való hozzáférés szükséges. Ennek eredményeképpen a DMS egy példányát a felhasználói előfizetés szintjén engedélyre van szüksége. Hozzon létre a szükséges szerepkörök, amelyek igény szerint rendelhet, futtassa a következő parancsfájlt:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.DataMigration/services/*/read", `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Az Azure SQL Database áttelepítése az SQL Server vonatkozó Előfeltételek

Azure Database Migration Service Előfeltételek, amelyek közösek az összes áttelepítési forgatókönyvek kívül is kifejezetten egy forgatókönyvet vagy egy másik érvényes előfeltételekkel.

Azure SQL Database áttelepítések, amelyek közösek az összes áttelepítési forgatókönyvek esetén is telepítheti az SQL Server az Azure Database Migration Service használata esetén ügyeljen arra, hogy oldja meg a következő további Előfeltételek vonatkoznak:

* Hozzon létre egy példányt az Azure SQL Database-példány, amelyeket a következő a részletek a következő cikkben: C[egy Azure SQL database létrehozása az Azure Portalon](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* Töltse le a [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3-as vagy újabb verzióját.
* Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a forrásul szolgáló SQL Servert, amely alapértelmezés szerint az 1433-as TCP-porton található.
* Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
* Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure SQL-adatbáziskiszolgáló számára, hogy az Azure SQL Database Migration Service hozzáférhessen a céladatbázisokhoz. Adja meg az Azure Database Migration Service-hez használt virtuális hálózat alhálózati tartományát.
* Gondoskodjon róla, hogy a forrásként szolgáló SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedélyekkel.
* Gondoskodjon róla, hogy a forrásként szolgáló Azure SQL Database-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek CONTROL DATABASE engedéllyel a célként szolgáló Azure SQL adatbázisokban.

   > [!NOTE]
   > Az áttelepítések végrehajtásához az SQL Serverről az Azure SQL Database az Azure Database Migration Service használatához szükséges előfeltételeket teljes listáját lásd a következő oktatóanyagot [SQL Server Migrálása az Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>Egy Azure SQL Database felügyelt példány az SQL-kiszolgáló áttelepítése előfeltételei

* Hozzon létre egy Azure SQL Database felügyelt példány az a cikk részletesen [egy Azure SQL Database felügyelt példány létrehozása az Azure Portalon](https://aka.ms/sqldbmi).
* Nyissa meg a tűzfal az Azure adatbázis Migrálási szolgáltatás IP-cím vagy alhálózat címtartománya a 445-ös port az SMB-forgalom engedélyezéséhez.
* Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a forrásul szolgáló SQL Servert, amely alapértelmezés szerint az 1433-as TCP-porton található.
* Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
* Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példány és a célként szolgáló felügyelt példány összekapcsolásához használt bejelentkezési adatok mind a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
* Hozzon létre egy hálózati megosztást, amelyet az Azure Database Migration Service használhat a forrásként szolgáló adatbázis biztonsági mentésére.
* Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példányt futtató szolgáltatásfiók írási, a forrásként szolgáló kiszolgáló számítógépes fiókja pedig olvasási és írási jogosultságokkal rendelkezik az Ön által létrehozott hálózati megosztáson.
* Jegyezzen fel egy olyan Windows-felhasználót (és jelszót), amely teljes körű jogosultságokkal rendelkezik az Ön által korábban létrehozott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy fel tudja tölteni a biztonsági mentési fájlokat az Azure Storage-tárolóba a visszaállítási művelethez.
* Hozzon létre egy blobtárolót és az SAS URI lekérni a cikkben leírt lépések végrehajtásával [a Storage Explorer használatával kezelheti az Azure Blob Storage-erőforrások](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Ügyeljen arra, hogy minden engedéllyel (olvasás, írás, törlés, List) házirend ablakban válassza a SAS URI létrehozása során.

   > [!NOTE]
   > Az Azure Database Migration Service segítségével áttelepítések hajtsa végre az SQL Serverről Azure SQL Database felügyelt példányába történő szükséges előfeltételek teljes listáját lásd a következő oktatóanyagot [SQL Server Migrálása az Azure SQL Database felügyelt példánya ](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>További lépések

Az Azure Database Migration Service és a régiónkénti rendelkezésre állás áttekintését lásd: a cikk [Mi az az Azure Database Migration Service](dms-overview.md).
