---
title: 'Oktatóanyag: Át egy Azure SQL Database felügyelt példány DMS használatával |} A Microsoft Docs'
description: Megtanulhatja, hogyan telepítse át a helyszíni SQL Server egy Azure SQL Database felügyelt példány az Azure Database Migration Service segítségével.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: cf285c18d2204da625c970a367177f86474149ab
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880984"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Oktatóanyag: SQL Server migrálása az Azure SQL Database felügyelt példány offline a DMS használatával

Az Azure Database Migration Service segítségével az adatbázisokat át egy helyszíni SQL Server-példánynak egy [Azure SQL Database felügyelt példány](../sql-database/sql-database-managed-instance.md). A további módszereket is néhány manuális beavatkozást igénylő, tekintse meg a cikket [SQL Server-példány migrálása az Azure SQL Database felügyelt példány](../sql-database/sql-database-managed-instance-migrate.md).

Ebben az oktatóanyagban áttelepítése a **Adventureworks2012** adatbázist az SQL Server helyi példányát egy Azure SQL Database felügyelt példány az Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Egy Azure Database Migration Service-példány létrehozása.
> - Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> - A migrálás futtatása.
> - A migrálás monitorozása.
> - Migrálási jelentés letöltése.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk bemutatja az SQL Serverből egy Azure SQL Database felügyelt példány az offline áttelepítés. Lásd az online migrálás [SQL Server Migrálása az Azure SQL Database felügyelt példány DMS használatával online](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Hozzon létre egy Azure Virtual Network (VNET) használatával az Azure Resource Manager üzembe helyezési modell, amely lehetővé teszi a helyek közötti kapcsolatot a helyszíni adatforrás-kiszolgálók használatával vagy az Azure Database Migration Service [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Ismerje meg a hálózati topológiák az Azure SQL Database felügyelt példány segítségével végzi az áttelepítést az Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

    > [!NOTE]
    > Virtuális hálózathoz a telepítés során, ha az ExpressRoute hálózati a Microsoft társviszony-létesítés használja, hozzá a következő szolgáltatás [végpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) az alhálózathoz, amelyben üzembe fogja helyezni a szolgáltatást:
    > - Cél adatbázis végpont (például SQL-végpont, Cosmos-DB végpont, és így tovább)
    > - Storage-végpont
    > - Service bus-végpont
    >
    > Ez a konfiguráció szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.

- Győződjön meg arról, hogy a virtuális hálózatok közötti hálózati biztonsági csoport szabályai nem blokkolják a következő bejövő kommunikációs portokat, Azure Database Migration Service: 443, 53, 9354, 445, 12000. További részletek az Azure VNET NSG-forgalom szűréséről: [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurálja a [Windows tűzfalat a forrásadatbázis-motorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a forrásul szolgáló SQL Servert, amely alapértelmezés szerint az 1433-as TCP-porton található.
- Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
- Ha tűzfalberendezést használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz, valamint a fájlokhoz a 445-ös SMB-porton keresztül.
- Hozzon létre egy Azure SQL Database felügyelt példány az a cikk részletesen [egy Azure SQL Database felügyelt példány létrehozása az Azure Portalon](https://aka.ms/sqldbmi).
- Győződjön meg arról, hogy a bejelentkezések, használja a forrás SQL-kiszolgáló és a felügyelt példány célként a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
- Hozzon létre egy hálózati megosztást, amelyet az Azure Database Migration Service használhat a forrásként szolgáló adatbázis biztonsági mentésére.
- Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példányt futtató szolgáltatásfiók írási, a forrásként szolgáló kiszolgáló számítógépes fiókja pedig olvasási és írási jogosultságokkal rendelkezik az Ön által létrehozott hálózati megosztáson.
- Jegyezzen fel egy olyan Windows-felhasználót (és jelszót), amely teljes körű jogosultságokkal rendelkezik az Ön által korábban létrehozott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy fel tudja tölteni a biztonsági mentési fájlokat az Azure Storage-tárolóba a visszaállítási művelethez.
- Hozzon létre egy blobtárolót, és kérje le annak SAS URI-ját az [Azure Blob Storage-erőforrások Storage Explorerrel történő kezelésével](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) foglalkozó cikkben leírt lépések szerint. Az SAS URI létrehozásakor ügyeljen arra, hogy minden jogosultságot (írási, olvasási, törlési, listázási) kiválasszon a szabályzat ablakában. Ezt az adatot az Azure Database Migration Service az áttelepítéshez használt biztonsági mentési fájlok feltöltése a storage-fiók tároló hozzáférést biztosít az adatbázisok az Azure SQL Database felügyelt példánya.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

    ![Portál-előfizetések megtekintése](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)        

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keressen ár az **Azure Database Migration Service** kifejezésre, és a legördülő menüben válassza ki az **Azure Database Migration szolgáltatás** elemet.

     ![Azure Piactér](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, ahol a DMS példányát létre szeretné hozni.

5. Válasszon ki egy meglévő virtuális hálózatot (VNET), vagy hozzon létre egy újat.

    A virtuális hálózat az Azure Database Migration Service a forrás SQL-kiszolgáló és a cél Azure SQL Database felügyelt példány hozzáférést biztosít.

    További információk a virtuális hálózatok Azure Portalon történő létrehozásáról [Virtuális hálózat létrehozása az Azure Portallal](https://aka.ms/DMSVnet).

    További részletekért tekintse meg a cikket [hálózati topológiák az Azure SQL DB felügyelt példányainak migrálásához az Azure Database Migration Service segítségével](https://aka.ms/dmsnetworkformi).

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![DMS-szolgáltatás létrehozása](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

Keresse meg a létrehozott szolgáltatáspéldányt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Az **Azure Database Migration Service** képernyőjén keresse meg a létrehozott példány nevét, és válassza ki a példányt.

3. Válassza a + **Új migrálási projekt** lehetőséget.

4. Az **Új migrálási projekt** képernyőn nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza ki az **SQL Servert**, a **Célkiszolgáló típus** szövegbeviteli mezőben pedig a **felügyelt Azure SQL Database-példányt**. A **Válassza ki a tevékenység típusát** szövegbeviteli mezőben válassza az **Offline adatok migrálása** lehetőséget.

   ![DMS-projekt létrehozása](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server kapcsolati adatait.

2. Ha nem telepített megbízható tanúsítványt a kiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítványokkal titkosított SSL-kapcsolatok nem biztosítanak erős védelmet. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Éles környezetben vagy az internethez csatlakozó kiszolgálók esetén az önaláírt tanúsítványokkal működő SSL nem megbízható.

   ![Forrás részletei](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Kattintson a **Mentés** gombra.

4. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki az **Adventureworks2012** adatbázist a migráláshoz.

   ![Forrásadatbázisok kiválasztása](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Ha az SQL Server Integration Services (SSIS) használ, a DMS jelenleg nem támogatja a katalógus-adatbázis, az SSIS-projektek/csomagok (SSISDB) a migrálás SQL Serverről az Azure SQL Database felügyelt példányában. Azonban üzembe helyezése az Azure Data Factory (ADF) az SSIS és ismételt üzembe helyezése az SSIS-projektek/csomagok a cél Azure SQL Database felügyelt példány által üzemeltetett SSISDB. SSIS-csomagok áttelepítése kapcsolatos további információkért tekintse meg a cikket [áttelepítése az SQL Server Integration Services-csomagok az Azure-bA](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Az a **Migrálási cél részletei** képernyőn, adja meg a cél, amely, amelyhez migráláshoz előre kiépített Azure SQL Database felügyelt példány kapcsolati adatait a **AdventureWorks2012**adatbázis.

    Ha még nem építette az Azure SQL Database felügyelt példány, válassza ki a [hivatkozás](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) segítséget nyújtanak a példány üzembe helyezése. A projekt létrehozása továbbra is, és ezt követően készen áll, az Azure SQL Database felügyelt példány esetén térjen vissza az adott projekthez az áttelepítés végrehajtásához.

     ![Cél kiválasztása](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Kattintson a **Mentés** gombra.

## <a name="select-source-databases"></a>Forrásadatbázisok kiválasztása

1. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki a migrálni kívánt forrásadatbázist.

    ![Forrásadatbázisok kiválasztása](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Kattintson a **Mentés** gombra.

## <a name="select-logins"></a>Bejelentkezési adatok kiválasztása

1. A **Bejelentkezések kiválasztása** képernyőn válassza ki a migrálni kívánt bejelentkezési adatokat.

    >[!NOTE]
    >Ez a kiadás kizárólag az SQL-bejelentkezések migrálását támogatja.

    ![Bejelentkezési adatok kiválasztása](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Kattintson a **Mentés** gombra.

## <a name="configure-migration-settings"></a>Migrálási beállítások konfigurálása

1. A **Migrálási beállítások konfigurálása** képernyőn adja meg a következő adatokat:

    | | |
    |--------|---------|
    |**Válassza ki a forrás biztonsági másolatának beállítását** | Válassza **a legfrissebb biztonsági mentési fájlok megadására** szolgáló lehetőséget, ha már az összes biztonsági mentési fájllal rendelkezik, amelyet a DMS az adatbázis migrálásához használni fog. Válassza azt a lehetőséget, amely szerint **az Azure Database Migration Service fogja létrehozni a biztonsági mentési fájlokat**, ha azt szeretné, hogy a DMS először létrehozza az adatbázis teljes biztonsági másolatát, és ezt használja a migráláshoz. |
    |**Megosztott hálózati helyen** | Hozzon létre egy helyszíni SMB hálózati megosztást, amelyre az Azure Database Migration Service átviheti a forrásadatbázis biztonsági mentéseit. A forrásként szolgáló SQL Server-példányt futtató szolgáltatásfióknak írási jogosultságokkal kell rendelkeznie ehhez a hálózati megosztáshoz. Adja meg a hálózati megosztáson található kiszolgáló FQDN- vagy IP-címét, például \\\servername.domainname.com\backupfolder vagy \\\IP address\backupfolder.|
    |**Felhasználónév** | Győződjön meg arról, hogy a Windows-felhasználó teljes körű jogosultságokkal rendelkezik a fent megadott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználó hitelesítő adatait, hogy fel tudja tölteni a biztonsági mentési fájlokat az Azure Storage-tárolóba a visszaállítási művelethez. Ha TDE-kompatibilis adatbázisok vannak migrálásra kijelölve, a fenti Windows-felhasználónak a beépített rendszergazdai fióknak kell lennie, és a [felhasználói fiókok felügyeletét](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) le kell tiltani, hogy az Azure Database Migration Service feltölthesse és törölhesse a tanúsítványfájlokat. |
    |**Jelszó** | A felhasználó jelszava. |
    |**Tárfiók-beállítások** | Az SAS URI-t biztosít az Azure Database Migration Service, a storage-fiókot, amelyhez a szolgáltatás feltölti a biztonsági mentési fájlokat, és hogy tároló-hozzáféréssel rendelkező használatos áttelepítése adatbázisok az Azure SQL Database felügyelt példányában. [Itt találja az arra vonatkozó tudnivalókat, hogyan kérheti le a blobtároló SAS URI-ját](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**TDE-beállítások** | Ha a forrásadatbázisok migráláshoz a transzparens adattitkosítás (TDE) engedélyezve van, akkor írási jogosultságokkal rendelkezik a cél Azure SQL Database felügyelt példányában.  Válassza ki az előfizetést, amelyben az Azure SQL Database felügyelt példányt a legördülő menüből.  A legördülő menüben válassza ki a célul szolgáló **felügyelt Azure SQL Database-példányt**. |

    ![Migrálási beállítások konfigurálása](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Kattintson a **Mentés** gombra.

## <a name="review-the-migration-summary"></a>A migrálás összefoglalásának áttekintése

1. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

2. Bontsa ki az **Érvényesítési lehetőség** szakaszt a **Válasszon egy ellenőrzési lehetőséget** képernyő megjelenítéséhez. Adja meg, hogy a rendszer ellenőrizze-e a migrált adatbázist a lekérdezés helyessége alapján, majd válassza a **Mentés** lehetőséget.

3. Tekintse át és hagyja jóvá a migrálási projekttel kapcsolatos részleteket.

    ![Migrálási projekt áttekintése](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Kattintson a **Mentés** gombra.

## <a name="run-the-migration"></a>A migrálás futtatása

- Válassza a **Migrálás futtatása** lehetőséget.

  Megjelenik a migrálási tevékenység ablaka. A tevékenység állapota: **Függőben**.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálási tevékenység ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez.
 
   ![A migrálási tevékenység folyamatban van](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Az adatbázisok és a bejelentkezések kategóriáit is kibonthatja a kapcsolódó kiszolgálói objektumok migrálási állapotának nyomon követéséhez.

   ![A migrálási tevékenység folyamatban van](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. A migrálás befejezése után válassza a **Jelentés letöltése** lehetőséget. A megjelenő jelentés a migrálás folyamatával kapcsolatos részleteket listázza.

3. Győződjön meg arról, hogy a céladatbázis a cél Azure SQL Database felügyelt példány környezetben.

## <a name="next-steps"></a>További lépések

- Adatbázis áttelepítése a T-SQL RESTORE parancsot a felügyelt példány hogyan oktatóanyagért lásd: [biztonsági másolatának visszaállítása egy felügyelt példányra a restore parancs használatával](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Felügyelt példánnyal kapcsolatos további információkért lásd: [Mi az a felügyelt példány](../sql-database/sql-database-managed-instance.md).
- Felügyelt példány az alkalmazások csatlakoztatásával kapcsolatos információkért lásd: [alkalmazások csatlakoztatása](../sql-database/sql-database-managed-instance-connect-app.md).
