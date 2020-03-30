---
title: 'Oktatóanyag: Az SQL Server áttelepítése SQL által felügyelt példányra'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan migrálhat az SQL Server helyszíni kiszolgálójáról egy Azure Database Database-felügyelt példányra az Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 416be7de4b3cef4fb6e1bcfd09d934937f8c96d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297729"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Oktatóanyag: Az SQL Server áttelepítése egy Azure SQL Database felügyelt példányba offline állapotban a DMS használatával

Az Azure Database Migration Service segítségével áttelepítheti az adatbázisokat egy helyszíni SQL Server-példányból egy [Azure SQL Database által felügyelt példányba.](../sql-database/sql-database-managed-instance.md) További módszerek, amelyek némi manuális erőfeszítést igényel, olvassa el a cikk [SQL Server példány áttelepítésaz Azure SQL Database felügyelt példány.](../sql-database/sql-database-managed-instance-migrate.md)

Ebben az oktatóanyagban az **Adventureworks2012-adatbázist** az SQL Server egy helyszíni példányából egy SQL Database-példányba telepíti át az Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> - Hozzon létre egy Azure Database Migration Service-példányt.
> - Hozzon létre egy áttelepítési projektet az Azure Database Migration Service használatával.
> - A migrálás futtatása.
> - A migrálás monitorozása.
> - Migrálási jelentés letöltése.

> [!IMPORTANT]
> Az SQL Serverről az SQL Database által felügyelt példányra történő offline áttelepítések esetén az Azure Database Migration Service létrehozhatja a biztonsági mentési fájlokat. Másik lehetőségként az SMB hálózati megosztáslegújabb teljes adatbázis-biztonsági mentését is megadhatja, amelyet a szolgáltatás az adatbázisok áttelepítéséhez használ. Ne fűzz több biztonsági mentést egyetlen biztonsági másolathoz; minden biztonsági mentést külön biztonsági másolattal. Vegye figyelembe, hogy a tömörített biztonsági mentések is, hogy csökkentse annak valószínűségét, hogy a nagy biztonsági mentések áttelepítése lehetséges problémákat tapasztal.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk az SQL Server kiszolgálóról az SQL Database által felügyelt példányba történő offline áttelepítést ismerteti. Az online áttelepítésről az [SQL Server áttelepítése egy Azure SQL Database által felügyelt példányba a DMS használatával](tutorial-sql-server-managed-instance-online.md)című témakörben olvashat.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Hozzon létre egy Microsoft Azure virtuális hálózatot az Azure adatbázis-áttelepítési szolgáltatáshoz az Azure Resource Manager telepítési modelljével, amely az [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával biztosít helyek közötti kapcsolatot a helyszíni forráskiszolgálókhoz. [Ismerje meg az Azure SQL Database által felügyelt példányok áttelepítéséhez használt hálózati topológiákat az Azure Database Migration Service használatával.](https://aka.ms/dmsnetworkformi) A virtuális hálózat létrehozásáról további információt a [Virtuális hálózati dokumentációban](https://docs.microsoft.com/azure/virtual-network/)és különösen a részletes en című rövid útmutatóban talál.

    > [!NOTE]
    > A virtuális hálózat beállítása során, ha az ExpressRoute szolgáltatást hálózati társviszony-létesítéssel használja a Microsofthoz, adja hozzá a következő [szolgáltatásvégpontokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) ahhoz az alhálózathoz, amelyben a szolgáltatás ki lesz építve:
    > - Cél adatbázis-végpont (például SQL-végpont, Cosmos DB-végpont és így tovább)
    > - Tárolási végpont
    > - Szolgáltatásbusz végpontja
    >
    > Erre a konfigurációra azért van szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.

- Győződjön meg arról, hogy a virtuális hálózati biztonsági csoport szabályai nem blokkolják a következő bejövő kommunikációs portokat az Azure Database Migration Service szolgáltatásba: 443, 53, 9354, 445, 12000. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részleteket a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal című témakörben olvashat.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)
- Konfigurálja a [Windows tűzfalat a forrásadatbázis-motorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat, hogy az Azure Database Migration Service hozzáférhessen a forrás SQL Server kiszolgálóhoz, amely alapértelmezés szerint a 1433-as TCP-port.
- Ha több elnevezett SQL Server-példányt futtat dinamikus portokkal, engedélyezze az SQL Browser Service szolgáltatást, és engedélyezze az 1434-es UDP-porthoz való hozzáférést a tűzfalakon keresztül, hogy az Azure Database Migration Service a forrásnévvel ellátott példányhoz kapcsolódjon Szerver.
- Ha a forrásadatbázisok előtt tűzfalberendezést használ, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia ahhoz, hogy az Azure Database Migration Service hozzáférhessen a forrásadatbázis(ok)hoz az áttelepítéshez, valamint a fájlokhoz a 445-ös SMB-porton keresztül.
- Hozzon létre egy Azure SQL Database felügyelt példányt az Azure Portalon felügyelt [Azure Database-példány létrehozása](https://aka.ms/sqldbmi)című cikk részleteinek követésével.
- Győződjön meg arról, hogy a forrás SQL Server és a cél felügyelt példány csatlakoztatásához használt bejelentkezések a sysadmin server szerepkör tagjai.

    >[!NOTE]
    >Alapértelmezés szerint az Azure Database Migration Service csak az SQL-bejelentkezések áttelepítését támogatja. A Windows-bejelentkezések áttelepítése azonban a következő módon lehetséges:
    >
    >- Annak biztosítása, hogy a cél SQL Database felügyelt példány a AAD olvasási hozzáféréssel rendelkezik, amelyet az Azure Portalon keresztül a **vállalati rendszergazdával**vagy egy **globális rendszergazdai**" szerepkörrel rendelkező felhasználó konfigurálhat.
    >- Konfigurálja az Azure Database Migration Service példányát a Windows felhasználói/csoport bejelentkezési áttelepítésének engedélyezéséhez, amely az Azure Portalon keresztül van beállítva a Konfiguráció lapon. A beállítás engedélyezése után indítsa újra a szolgáltatást a módosítások érvénybe léptetéséhez.
    >
    > A szolgáltatás újraindítása után a Windows felhasználói/csoportbejelentkezései megjelennek az áttelepítésre rendelkezésre álló bejelentkezések listájában. Az áttelepített Windows-felhasználók/csoportok bejelentkezések esetén a rendszer kéri a társított tartománynév megadását. A szolgáltatás felhasználói fiókjai (nt authority tartománynévvel rendelkező fiók) és a virtuális felhasználói fiókok (NT SERVICE tartománynévvel rendelkező fióknév) nem támogatottak.

- Hozzon létre egy hálózati megosztást, amelyet az Azure Database Migration Service a forrásadatbázis biztonsági mentén használhat.
- Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példányt futtató szolgáltatásfiók írási, a forrásként szolgáló kiszolgáló számítógépes fiókja pedig olvasási és írási jogosultságokkal rendelkezik az Ön által létrehozott hálózati megosztáson.
- Jegyezzen fel egy olyan Windows-felhasználót (és jelszót), amely teljes körű jogosultságokkal rendelkezik az Ön által korábban létrehozott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy töltse fel a biztonsági mentési fájlokat az Azure Storage-tárolóba visszaállítási művelethez.
- Hozzon létre egy blobtárolót, és kérje le annak SAS URI-ját az [Azure Blob Storage-erőforrások Storage Explorerrel történő kezelésével](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) foglalkozó cikkben leírt lépések szerint. Az SAS URI létrehozásakor ügyeljen arra, hogy minden jogosultságot (írási, olvasási, törlési, listázási) kiválasszon a szabályzat ablakában. Ez a részlet hozzáférést biztosít az Azure Database Migration Service számára a tárfiók tárolójához az adatbázisok Azure SQL Database felügyelt példányba való áttelepítéséhez használt biztonsági mentési fájlok feltöltéséhez.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

    ![Portál-előfizetések megtekintése](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyben létre szeretné hozni az Azure Database Migration Service példányát, majd válassza **az Erőforrás-szolgáltatók**lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd jobbra a **Microsoft.DataMigration**programtól, és válassza a **Regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása**lehetőséget, keresse meg az Azure Database **Migration Service**elemet, majd válassza az Azure Database **Migration Service** lehetőséget a legördülő listából.

    ![Azure Piactér](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, ahol a DMS példányát létre szeretné hozni.

5. Jelöljön ki egy meglévő virtuális hálózatot, vagy hozzon létre egyet.

    A virtuális hálózat hozzáférést biztosít az Azure Database Migration Service számára a forrás SQL Server és a cél Az Azure SQL Database felügyelt példány.

    A virtuális hálózat Azure Portalon való létrehozásáról további információt a Virtuális hálózat létrehozása az Azure Portal használatával című témakörben [talál.](https://aka.ms/DMSVnet)

    További részleteket az [Azure SQL DB által felügyelt példányok áttelepítése](https://aka.ms/dmsnetworkformi)az Azure Database Migration Service használatával című cikkben olvashat.

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![DMS-szolgáltatás létrehozása](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

Keresse meg a létrehozott szolgáltatáspéldányt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Az Azure Database Migration Service összes példányának megkeresése](media/tutorial-sql-server-to-managed-instance/dms-search.png)

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
    > Az önaláírt tanúsítvánnyal titkosított TLS-kapcsolatok nem nyújtanak erős biztonságot. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Ne hagyatkozzon a TLS-re, amely önaláírt tanúsítványokat használ éles környezetben vagy az internethez kapcsolódó kiszolgálókon.

   ![Forrás részletei](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Kattintson a **Mentés** gombra.

4. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki az **Adventureworks2012** adatbázist a migráláshoz.

   ![Forrásadatbázisok kiválasztása](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Ha SQL Server Integration Services (SSIS) szolgáltatást használ, a DMS jelenleg nem támogatja az SSIS-projektek/-csomagok (SSISDB) katalógusadatbázisának áttelepítését az SQL Server ről az Azure SQL Database felügyelt példányára. Az SSIS-t azonban az Azure Data Factoryban (ADF) is kiépítheti, és újratelepítheti az SSIS-projekteket/-csomagokat az Azure SQL Database által felügyelt példány által üzemeltetett cél SSISDB-re. Az SSIS-csomagok áttelepítéséről az SQL [Server Integration Services-csomagok áttelepítése az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című témakörben olvashat bővebben.

5. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Az **áttelepítési cél részletei** képernyőn adja meg a cél kapcsolatának részleteit, amely az előre kiépített Azure SQL Database felügyelt példánya, amelyre az **AdventureWorks2012** adatbázist telepíti át.

    Ha még nem kiépítette az SQL Database felügyelt példányát, válassza ki a [példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) kiépítéséhez. Továbbra is folytathatja a projekt létrehozását, majd amikor az Azure SQL Database felügyelt példánya készen áll, térjen vissza az adott projekthez az áttelepítés végrehajtásához.

    ![Cél kiválasztása](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Kattintson a **Mentés** gombra.

## <a name="select-source-databases"></a>Forrásadatbázisok kiválasztása

1. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki a migrálni kívánt forrásadatbázist.

    ![Forrásadatbázisok kiválasztása](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Kattintson a **Mentés** gombra.

## <a name="select-logins"></a>Bejelentkezési adatok kiválasztása

1. A **Bejelentkezések kiválasztása** képernyőn válassza ki a migrálni kívánt bejelentkezési adatokat.

    >[!NOTE]
    >Alapértelmezés szerint az Azure Database Migration Service csak az SQL-bejelentkezések áttelepítését támogatja. A Windows-bejelentkezések áttelepítésének engedélyezéséhez tekintse meg az oktatóanyag **Előfeltételek** című részét.

    ![Bejelentkezési adatok kiválasztása](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Kattintson a **Mentés** gombra.

## <a name="configure-migration-settings"></a>Migrálási beállítások konfigurálása

1. A **Migrálási beállítások konfigurálása** képernyőn adja meg a következő adatokat:

    | | |
    |--------|---------|
    |**Válassza ki a forrás biztonsági másolatának beállítását** | Válassza **a legfrissebb biztonsági mentési fájlok megadására** szolgáló lehetőséget, ha már az összes biztonsági mentési fájllal rendelkezik, amelyet a DMS az adatbázis migrálásához használni fog. Válassza azt a lehetőséget, amely szerint **az Azure Database Migration Service fogja létrehozni a biztonsági mentési fájlokat**, ha azt szeretné, hogy a DMS először létrehozza az adatbázis teljes biztonsági másolatát, és ezt használja a migráláshoz. |
    |**Hálózatihely-megosztás** | A helyi SMB hálózati megosztás, amely az Azure Database Migration Service vehet a forrásadatbázis biztonsági mentések. A forrásként szolgáló SQL Server-példányt futtató szolgáltatásfióknak írási jogosultságokkal kell rendelkeznie ehhez a hálózati megosztáshoz. Adja meg a hálózati megosztáson található kiszolgáló FQDN- vagy IP-címét, például \\\servername.domainname.com\backupfolder vagy \\\IP address\backupfolder.|
    |**Felhasználónév** | Győződjön meg arról, hogy a Windows-felhasználó teljes körű jogosultságokkal rendelkezik a fent megadott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy töltse fel a biztonsági mentési fájlokat az Azure Storage-tárolóba visszaállítási művelethez. Ha TDE-kompatibilis adatbázisok vannak migrálásra kijelölve, a fenti Windows-felhasználónak a beépített rendszergazdai fióknak kell lennie, és a [felhasználói fiókok felügyeletét](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) le kell tiltani, hogy az Azure Database Migration Service feltölthesse és törölhesse a tanúsítványfájlokat. |
    |**Jelszó** | A felhasználó jelszava. |
    |**Tárfiók beállításai** | A SAS-URI, amely az Azure Database Migration Service hozzáférést biztosít a tárfiók tárolójához, amelyre a szolgáltatás feltölti a biztonsági mentési fájlokat, és amely az adatbázisok Áttelepítésére szolgál az Azure SQL Database felügyelt példányba. [Itt találja az arra vonatkozó tudnivalókat, hogyan kérheti le a blobtároló SAS URI-ját](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**TDE-beállítások** | Ha a forrásadatbázisok átgrése átlátszó adattitkosítás (TDE) engedélyezve van, írási jogosultságokkal kell rendelkeznie a cél Azure SQL Database felügyelt példány.  Válassza ki azt az előfizetést, amelyben az Azure SQL Database felügyelt példány a legördülő menüből kiépített.  A legördülő menüben válassza ki a célul szolgáló **felügyelt Azure SQL Database-példányt**. |

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

  Megjelenik az áttelepítési tevékenység ablak, és a tevékenység állapota **Függőben**van.

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálási tevékenység ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez.

   ![A migrálási tevékenység folyamatban van](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Az adatbázisok és a bejelentkezések kategóriáit is kibonthatja a kapcsolódó kiszolgálói objektumok migrálási állapotának nyomon követéséhez.

   ![A migrálási tevékenység folyamatban van](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. A migrálás befejezése után válassza a **Jelentés letöltése** lehetőséget. A megjelenő jelentés a migrálás folyamatával kapcsolatos részleteket listázza.

3. Ellenőrizze, hogy a cél-adatbázis a cél Az Azure SQL Database felügyelt példány környezetben.

## <a name="next-steps"></a>További lépések

- Az adatbázis T-SQL RESTORE paranccsal történő áttelepítését bemutató oktatóanyagról a [Visszaállítás paranccsal című témakörben található a Biztonsági másolat visszaállítása felügyelt példányra című témakörben.](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)
- A felügyelt példányról a Mi a felügyelt példány című [témakörben](../sql-database/sql-database-managed-instance.md)talál további információt.
- Az alkalmazások felügyelt példányhoz való csatlakoztatásáról az [Alkalmazások csatlakoztatása című](../sql-database/sql-database-managed-instance-connect-app.md)témakörben talál további információt.
