---
title: Az Azure adatbázis-áttelepítési szolgáltatásának előfeltételei
description: Ismerje meg az Azure Database Migration Service adatbázis-áttelepítések végrehajtásához szükséges előfeltételeinek áttekintését.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 89cb63630e3dbe953ed3f4fd8796d01ba0d36067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651491"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Az Azure Database Migration Service használatára vonatkozó előfeltételek áttekintése

Számos előfeltétele van annak biztosításához, hogy az Azure Database Migration Service zökkenőmentesen futjon az adatbázis-áttelepítések végrehajtásakor. Az előfeltételek egy része a szolgáltatás által támogatott összes forgatókönyvre (forrás-cél párokra) vonatkozik, míg más előfeltételek egyediek egy adott forgatókönyvhöz.

Az Azure database migration service használatával társított előfeltételek a következő szakaszokban találhatók.

## <a name="prerequisites-common-across-migration-scenarios"></a>Az áttelepítési forgatókönyvekben közös előfeltételek

Az Azure Database Migration Service előfeltételei, amelyek az összes támogatott áttelepítési forgatókönyvben gyakoriak, a következőket foglalják magukban:

* Hozzon létre egy Microsoft Azure virtuális hálózatot az Azure adatbázis-áttelepítési szolgáltatáshoz az Azure Resource Manager telepítési modelljével, amely az [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával biztosít helyek közötti kapcsolatot a helyszíni forráskiszolgálókhoz.
* Győződjön meg arról, hogy a virtuális hálózati biztonsági csoport (NSG) szabályai nem tiltják le a következő 443, 53, 9354, 445, 12000 kommunikációs portokat. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részleteket a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal című témakörben olvashat.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)
* Ha a forrásadatbázis(ok) előtt tűzfalberendezést használ, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia ahhoz, hogy az Azure Database Migration Service hozzáférhessen a forrásadatbázis(ok)hoz az áttelepítéshez.
* Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Engedélyezze a TCP/IP protokollt, amely az SQL Server Express telepítése során alapértelmezés szerint le van tiltva – kövesse a [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) cikk utasításait.

    > [!IMPORTANT]
    > Az Azure Database Migration Service egy példányának létrehozása olyan virtuális hálózati beállításokhoz való hozzáférést igényel, amelyek általában nem ugyanabban az erőforráscsoportban vannak. Ennek eredményeképpen a DMS egy példányát létrehozó felhasználónak előfizetési szintű engedélyre van szüksége. A szükséges szerepkörök létrehozásához, amelyeket szükség szerint hozzárendelhet, futtassa a következő parancsfájlt:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
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

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Az SQL Server Azure SQL Database rendszerbe való áttelepítésének előfeltételei

Az Azure Database Migration Service előfeltételei, amelyek közösek az összes áttelepítési forgatókönyvek, vannak olyan előfeltételek is, amelyek kifejezetten az egyik forgatókönyv vagy egy másik.

Ha az Azure Database Migration Service használatával sql server azure SQL Database-áttelepítések végrehajtására, az összes áttelepítési forgatókönyvközös előfeltételein kívül ügyeljen a következő további előfeltételekre:

* Hozzon létre egy Azure SQL Database-példányt az [Azure SQL Database létrehozása az Azure Portalon](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) cikk lépéseit követve.
* Töltse le a [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3-as vagy újabb verzióját.
* Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a forrásul szolgáló SQL Servert, amely alapértelmezés szerint az 1433-as TCP-porton található.
* Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
* Hozzon létre egy kiszolgálószintű [tűzfalszabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure SQL-adatbáziskiszolgáló számára, hogy az Azure SQL Database Migration Service hozzáférhessen a céladatbázisokhoz. Adja meg az Azure database migration service-hez használt virtuális hálózat alhálózati tartományát.
* Gondoskodjon róla, hogy a forrásként szolgáló SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedélyekkel.
* Gondoskodjon róla, hogy a forrásként szolgáló Azure SQL Database-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek CONTROL DATABASE engedéllyel a célként szolgáló Azure SQL adatbázisokban.

   > [!NOTE]
   > Az Azure Database Migration Service sql serverről az Azure SQL Database-adatbázisba történő áttelepítések végrehajtásához szükséges előfeltételek teljes listáját az [SQL Server áttelepítése az Azure SQL Database szolgáltatásba](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)című oktatóanyagban található.
   >

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>Az SQL Server Azure SQL Database felügyelt példányba való áttelepítésének előfeltételei

* Hozzon létre egy Azure SQL Database felügyelt példányt az Azure Portalon az [Azure-adatbázis felügyelt példányának létrehozása](https://aka.ms/sqldbmi)című cikk részleteinek követésével.
* Nyissa meg a tűzfalakat, hogy az SMB-forgalom a 445-ös porton az Azure Database Migration Service IP-címvagy alhálózati tartomány számára.
* Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a forrásul szolgáló SQL Servert, amely alapértelmezés szerint az 1433-as TCP-porton található.
* Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
* Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példány és a célként szolgáló felügyelt példány összekapcsolásához használt bejelentkezési adatok mind a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
* Hozzon létre egy hálózati megosztást, amelyet az Azure Database Migration Service használhat a forrásként szolgáló adatbázis biztonsági mentésére.
* Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példányt futtató szolgáltatásfiók írási, a forrásként szolgáló kiszolgáló számítógépes fiókja pedig olvasási és írási jogosultságokkal rendelkezik az Ön által létrehozott hálózati megosztáson.
* Jegyezzen fel egy olyan Windows-felhasználót (és jelszót), amely teljes körű jogosultságokkal rendelkezik az Ön által korábban létrehozott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy töltse fel a biztonsági mentési fájlokat az Azure Storage-tárolóba visszaállítási művelethez.
* Hozzon létre egy blobtárolót, és olvassa be a SAS-URI-t az [Azure Blob Storage-erőforrások kezelése](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)a Storage Explorer használatával című cikkben leírt lépésekkel. Ügyeljen arra, hogy a SAS URI létrehozásakor a házirendablakban válassza ki az összes engedélyt (Olvasás, írás, törlés, lista).

   > [!NOTE]
   > Az Azure Database Migration Service sql serverről az Azure SQL Database Managed Instance szolgáltatásba történő áttelepítések végrehajtásához szükséges előfeltételek teljes listáját az SQL Server áttelepítése az [Azure SQL Database Managed Instance programba](https://aka.ms/migratetomiusingdms)című oktatóanyagban található.

## <a name="next-steps"></a>További lépések

Az Azure Database Migration Service és a regionális elérhetőség áttekintését a [Mi az Azure-adatbázis-áttelepítési szolgáltatás](dms-overview.md)című cikkben találja.
