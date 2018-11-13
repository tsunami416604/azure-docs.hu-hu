---
title: 'Oktatóanyag: Migrálás egy felügyelt Azure SQL Database-példányra a DMS használatával | Microsoft Docs'
description: Megismerheti, hogyan migrálhat a helyszíni SQL Serverről egy felügyelt Azure SQL Database-példányra az Azure Database Migration Service használatával.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: f6b77f3a2b78d037e74bbca9a3624c9fa62c5d8b
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50961874"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-managed-instance-offline-using-dms"></a>Oktatóanyag: SQL Server offline migrálása felügyelt Azure SQL Database-példányra a DMS használatával
Az Azure Database Migration Service használatával migrálhatja egy helyszíni SQL Server-példány adatbázisait egy [felügyelt Azure SQL Database-példányra](../sql-database/sql-database-managed-instance.md). Ha olyan módszerek is érdeklik, amelyek esetenként manuális beavatkozást is igényelhetnek, tekintse át az [SQL Server-példány felügyelt Azure SQL Database-példányra történő migrálásával](../sql-database/sql-database-managed-instance-migrate.md) foglalkozó témakört.

Ebben az oktatóanyagban az **Adventureworks2012** adatbázist fogja migrálni az SQL Server egy helyszíni példányáról egy felügyelt Azure SQL Database-példányra az Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Egy Azure Database Migration Service-példány létrehozása.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.
> * Migrálási jelentés letöltése.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk egy offline migrálást mutat be az SQL Serverről egy felügyelt Azure SQL Database-példányra. Az online migrálással kapcsolatban tekintse meg az [SQL Server online migrálása felügyelt Azure SQL Database-példányra a DMS használatával](tutorial-sql-server-managed-instance-online.md) című cikket.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Hozzon létre egy virtuális hálózatot az Azure Database Migration Service-hez az Azure Resource Manager-alapú üzemi modell használatával, amely a hálózat helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgálóknak [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) használatával. [Meg kell ismerkednie azokkal a hálózati topológiákkal, amelyek a felügyelt Azure SQL DB-példányok Azure Database Migration Service-szel végzett migrálásához kapcsolódnak](https://aka.ms/dmsnetworkformi).
- Győződjön meg arról, hogy az Azure Virtual Network (VNET) hálózati biztonsági szabályok nem blokkolják a következő kommunikációs portokat: 443, 53, 9354, 445, 12000. További részletek az Azure VNET NSG-forgalom szűréséről: [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurálja a [Windows tűzfalat a forrásadatbázis-motorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a forrásul szolgáló SQL Servert, amely alapértelmezés szerint az 1433-as TCP-porton található.
- Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
- Ha tűzfalberendezést használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz, valamint a fájlokhoz a 445-ös SMB-porton keresztül.
- Hozzon létre egy felügyelt Azure SQL Database-példányt a [Felügyelt Azure SQL Database-példány létrehozása az Azure Portalon](https://aka.ms/sqldbmi) cikk lépései alapján.
- Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példány és a célként szolgáló felügyelt példány összekapcsolásához használt bejelentkezési adatok mind a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
- Hozzon létre egy hálózati megosztást, amelyet az Azure Database Migration Service használhat a forrásként szolgáló adatbázis biztonsági mentésére.
- Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példányt futtató szolgáltatásfiók írási, a forrásként szolgáló kiszolgáló számítógépes fiókja pedig olvasási és írási jogosultságokkal rendelkezik az Ön által létrehozott hálózati megosztáson.
- Jegyezzen fel egy olyan Windows-felhasználót (és jelszót), amely teljes körű jogosultságokkal rendelkezik az Ön által korábban létrehozott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy fel tudja tölteni a biztonsági mentési fájlokat az Azure Storage-tárolóba a visszaállítási művelethez.
- Hozzon létre egy blobtárolót, és kérje le annak SAS URI-ját az [Azure Blob Storage-erőforrások Storage Explorerrel történő kezelésével](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) foglalkozó cikkben leírt lépések szerint. Az SAS URI létrehozásakor ügyeljen arra, hogy minden jogosultságot (írási, olvasási, törlési, listázási) kiválasszon a szabályzat ablakában. Így gondoskodhat róla, hogy az Azure Database Migration Service hozzá tudjon férni a tárfióktárolóhoz, és fel tudja tölteni azokat a biztonsági mentési fájlokat, amelyeket a rendszer az adatbázisok felügyelt Azure SQL Database-példányra való migrálására használ.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

    ![Portál-előfizetések megtekintése](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megtekintése](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keressen ár az **Azure Database Migration Service** kifejezésre, és a legördülő menüben válassza ki az **Azure Database Migration szolgáltatás** elemet.

     ![Azure Piactér](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4.  Válassza ki azt a helyet, ahol a DMS példányát létre szeretné hozni.

5. Válasszon ki egy meglévő virtuális hálózatot (VNET), vagy hozzon létre egy újat.
 
    A VNET biztosítja az Azure Database Migration Service számára a forrásként szolgáló SQL Serverhez és a felügyelt Azure SQL Database-célpéldányhoz való hozzáférést.

    További információk a virtuális hálózatok Azure Portalon történő létrehozásáról [Virtuális hálózat létrehozása az Azure Portallal](https://aka.ms/DMSVnet).

    További részletekért lásd [a felügyelt Azure SQL DB-példányok Azure Database Migration Service-szel végzett migrálásához kapcsolódó hálózati topológiákkal](https://aka.ms/dmsnetworkformi) foglalkozó témakört.

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.
   
    ![DMS-szolgáltatás létrehozása](media\tutorial-sql-server-to-managed-instance\dms-create-service2.png)

7.  A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

Keresse meg a létrehozott szolgáltatáspéldányt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Az Azure Database Migration Service minden példányának megkeresése](media\tutorial-sql-server-to-managed-instance\dms-search.png)

2. Az **Azure Database Migration Service** képernyőjén keresse meg a létrehozott példány nevét, és válassza ki a példányt.
 
3. Válassza a + **Új migrálási projekt** lehetőséget.

4. Az **Új migrálási projekt** képernyőn nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza ki az **SQL Servert**, a **Célkiszolgáló típus** szövegbeviteli mezőben pedig a **felügyelt Azure SQL Database-példányt**. A **Válassza ki a tevékenység típusát** szövegbeviteli mezőben válassza az **Offline adatok migrálása** lehetőséget.

   ![DMS-projekt létrehozása](media\tutorial-sql-server-to-managed-instance\dms-create-project2.png)

5. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server kapcsolati adatait.

2. Ha nem telepített megbízható tanúsítványt a kiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítványokkal titkosított SSL-kapcsolatok nem biztosítanak erős védelmet. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Éles környezetben vagy az internethez csatlakozó kiszolgálók esetén az önaláírt tanúsítványokkal működő SSL nem megbízható.

   ![Forrás részletei](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Kattintson a **Mentés** gombra.

4. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki az **Adventureworks2012** adatbázist a migráláshoz.

   ![Forrásadatbázisok kiválasztása](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1.  A **Migrálási cél részletei** képernyőn adja meg a cél kapcsolati adatait. A cél az előzetesen kiépített felügyelt Azure SQL Database-példány, amelyre migrálni kívánja az **AdventureWorks2012** adatbázist.

    Ha még nem építette ki a felügyelt Azure SQL Database-példányt, a **Nem** lehetőséget választva megjelenik egy hivatkozás, amely segít a példány kiépítésében. Így is folytathatja a projekt létrehozását, és amikor a felügyelt Azure SQL Database-példány elkészül, lépjen vissza erre a projektre a migrálás elvégzéséhez.   
 
       ![Cél kiválasztása](media\tutorial-sql-server-to-managed-instance\dms-target-details2.png)

2.  Kattintson a **Mentés** gombra.

## <a name="select-source-databases"></a>Forrásadatbázisok kiválasztása

1. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki a migrálni kívánt forrásadatbázist.

    ![Forrásadatbázisok kiválasztása](media\tutorial-sql-server-to-managed-instance\select-source-databases.png)

2. Kattintson a **Mentés** gombra.

## <a name="select-logins"></a>Bejelentkezési adatok kiválasztása
 
1. A **Bejelentkezések kiválasztása** képernyőn válassza ki a migrálni kívánt bejelentkezési adatokat.

    >[!NOTE]
    >Ez a kiadás kizárólag az SQL-bejelentkezések migrálását támogatja.

    ![Bejelentkezési adatok kiválasztása](media\tutorial-sql-server-to-managed-instance\select-logins.png)

2. Kattintson a **Mentés** gombra.
 
## <a name="configure-migration-settings"></a>Migrálási beállítások konfigurálása
 
1. A **Migrálási beállítások konfigurálása** képernyőn adja meg a következő adatokat:

    | | |
    |--------|---------|
    |**Válassza ki a forrás biztonsági másolatának beállítását** | Válassza **a legfrissebb biztonsági mentési fájlok megadására** szolgáló lehetőséget, ha már az összes biztonsági mentési fájllal rendelkezik, amelyet a DMS az adatbázis migrálásához használni fog. Válassza azt a lehetőséget, amely szerint **az Azure Database Migration Service fogja létrehozni a biztonsági mentési fájlokat**, ha azt szeretné, hogy a DMS először létrehozza az adatbázis teljes biztonsági másolatát, és ezt használja a migráláshoz. |
    |**Hálózatihely-megosztás** | Hozzon létre egy helyszíni SMB hálózati megosztást, amelyre az Azure Database Migration Service átviheti a forrásadatbázis biztonsági mentéseit. A forrásként szolgáló SQL Server-példányt futtató szolgáltatásfióknak írási jogosultságokkal kell rendelkeznie ehhez a hálózati megosztáshoz. Adja meg a hálózati megosztáson található kiszolgáló FQDN- vagy IP-címét, például \\\servername.domainname.com\backupfolder vagy \\\IP address\backupfolder.|
    |**Felhasználónév** | Győződjön meg arról, hogy a Windows-felhasználó teljes körű jogosultságokkal rendelkezik a fent megadott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználó hitelesítő adatait, hogy fel tudja tölteni a biztonsági mentési fájlokat az Azure Storage-tárolóba a visszaállítási művelethez. Ha TDE-kompatibilis adatbázisok vannak migrálásra kijelölve, a fenti Windows-felhasználónak a beépített rendszergazdai fióknak kell lennie, és a [felhasználói fiókok felügyeletét](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) le kell tiltani, hogy az Azure Database Migration Service feltölthesse és törölhesse a tanúsítványfájlokat. |
    |**Jelszó** | A felhasználó jelszava. |
    |**Tárfiók beállításai** | Az az SAS URI, amely biztosítja az Azure Database Migration Service számára az ahhoz a tárfiókhoz való hozzáférést, amelyre a szolgáltatás feltölti azokat a biztonsági mentési fájlokat, amelyeket az adatbázisok felügyelt Azure SQL Database-példányra való migrálására használ a rendszer. [Itt találja az arra vonatkozó tudnivalókat, hogyan kérheti le a blobtároló SAS URI-ját](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**TDE-beállítások** | Ha a forrásadatbázisokat engedélyezett transzparens adattitkosítással (TDE-vel) migrálja, írási jogosultságokkal kell rendelkeznie a célul szolgáló felügyelt Azure SQL Database-példányon.  A legördülő menüben válassza ki azt az előfizetést, amelyben a felügyelt Azure SQL DB-példány üzembe van helyezve.  A legördülő menüben válassza ki a célul szolgáló **felügyelt Azure SQL Database-példányt**. |
    
    ![Migrálási beállítások konfigurálása](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings3.png)

2. Kattintson a **Mentés** gombra.
 
## <a name="review-the-migration-summary"></a>A migrálás összefoglalásának áttekintése

1. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

2. Bontsa ki az **Érvényesítési lehetőség** szakaszt a **Válasszon egy ellenőrzési lehetőséget** képernyő megjelenítéséhez. Adja meg, hogy a rendszer ellenőrizze-e a migrált adatbázist a lekérdezés helyessége alapján, majd válassza a **Mentés** lehetőséget.

3. Tekintse át és hagyja jóvá a migrálási projekttel kapcsolatos részleteket.
 
    ![Migrálási projekt áttekintése](media\tutorial-sql-server-to-managed-instance\dms-project-summary2.png)

4.  Kattintson a **Mentés** gombra.   

## <a name="run-the-migration"></a>A migrálás futtatása

- Válassza a **Migrálás futtatása** lehetőséget.

  Megjelenik a migrálási tevékenység ablaka. A tevékenység állapota: **Függőben**.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálási tevékenység ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez.
 
   ![A migrálási tevékenység folyamatban van](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration1.png)

    Az adatbázisok és a bejelentkezések kategóriáit is kibonthatja a kapcsolódó kiszolgálói objektumok migrálási állapotának nyomon követéséhez.

   ![A migrálási tevékenység folyamatban van](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration-extend.png)

2. A migrálás befejezése után válassza a **Jelentés letöltése** lehetőséget. A megjelenő jelentés a migrálás folyamatával kapcsolatos részleteket listázza.
 
3. Ellenőrizze, hogy a céladatbázis a célul szolgáló felügyelt Azure SQL Database-példány környezetében található-e.

## <a name="next-steps"></a>További lépések

- Arra vonatkozó oktatóanyag, hogyan migrálható egy adatbázis egy felügyelt példányra a T-SQL RESTORE paranccsal: [Biztonsági másolat visszaállítása felügyelt példányra a visszaállítási paranccsal](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- A felügyelt példányokkal kapcsolatos információért lásd: [Mi az a felügyelt példány?](../sql-database/sql-database-managed-instance.md)
- Az alkalmazások felügyelt példányhoz való csatlakoztatásával kapcsolatos információkat az [alkalmazások csatlakoztatását ismertető](../sql-database/sql-database-managed-instance-connect-app.md) cikk tartalmazza.