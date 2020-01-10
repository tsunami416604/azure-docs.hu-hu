---
title: 'Oktatóanyag: SQL Server migrálása SQL felügyelt példányra'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan telepítheti át a SQL Server a helyszínről egy Azure SQL Database felügyelt példányra Azure Database Migration Service használatával.
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
ms.openlocfilehash: d8e5b531684e175e5b9423bbc302bbe0b3d36058
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745283"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Oktatóanyag: SQL Server migrálása Azure SQL Database felügyelt példányra a DMS használatával

A Azure Database Migration Service segítségével áttelepítheti az adatbázisokat egy helyszíni SQL Server-példányról egy [Azure SQL Database felügyelt példányra](../sql-database/sql-database-managed-instance.md). A manuális beavatkozást igénylő további módszerekhez tekintse meg a [SQL Server példány áttelepítése Azure SQL Database felügyelt példányra](../sql-database/sql-database-managed-instance-migrate.md)című cikket.

Ebben az oktatóanyagban áttelepíti a **Adventureworks2012** -adatbázist a SQL Server helyszíni példányáról egy SQL Database felügyelt példányra Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> - Hozzon létre egy Azure Database Migration Service-példányt.
> - Hozzon létre egy áttelepítési projektet Azure Database Migration Service használatával.
> - A migrálás futtatása.
> - A migrálás monitorozása.
> - Migrálási jelentés letöltése.

> [!IMPORTANT]
> Az SQL Serverról SQL Database felügyelt példányra történő offline áttelepítéshez Azure Database Migration Service a biztonsági másolat fájljait is létrehozhatja. Másik lehetőségként megadhatja a legújabb teljes adatbázis biztonsági mentését abban az SMB-hálózati megosztásban, amelyet a szolgáltatás az adatbázisok átköltöztetéséhez használ majd. Ne fűzze hozzá több biztonsági mentést egyetlen biztonsági mentési adathordozóhoz; készítsen biztonsági mentést egy külön biztonságimásolat-fájlon. Vegye figyelembe, hogy a tömörített biztonsági mentéseket is használhatja, hogy csökkentse a nagy méretű biztonsági mentések áttelepítésére vonatkozó lehetséges problémák előfordulásának valószínűségét.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk a SQL Serverról SQL Database felügyelt példányra történő offline áttelepítést ismerteti. Online áttelepítéssel kapcsolatban lásd: [SQL Server áttelepítése Azure SQL Database felügyelt példányra a DMS használatával](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Service számára a Azure Resource Manager üzemi modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával. [Ismerkedjen meg Azure SQL Database felügyelt példányok áttelepítése Azure Database Migration Service használatával hálózati topológiákkal](https://aka.ms/dmsnetworkformi). A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](https://docs.microsoft.com/azure/virtual-network/), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.

    > [!NOTE]
    > Ha a virtuális hálózat beállítása során ExpressRoute használ a Microsoft számára, adja hozzá a következő szolgáltatási [végpontokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
    > - Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont stb.)
    > - Tárolási végpont
    > - Service Bus-végpont
    >
    > Erre a konfigurációra azért van szükség, mert Azure Database Migration Service nem rendelkezik internetkapcsolattal.

- Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport szabályai nem gátolják meg a következő bejövő kommunikációs portok Azure Database Migration Service: 443, 53, 9354, 445, 12000. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)című cikket.
- Konfigurálja a [Windows tűzfalat a forrásadatbázis-motorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat, hogy a Azure Database Migration Service hozzáférhessen a forrás SQL Serverhoz, amely alapértelmezés szerint a 1433-as TCP-port.
- Ha több elnevezett SQL Server példányt futtat dinamikus portok használatával, érdemes lehet engedélyezni a SQL Browser szolgáltatást, és engedélyezni a tűzfalon keresztül a 1434-es UDP-port elérését, így Azure Database Migration Service csatlakozhat a forráshoz tartozó nevesített példányhoz Server.
- Ha tűzfalat használ a forrásadatbázis előtt, akkor előfordulhat, hogy a tűzfalszabályok hozzáadásával engedélyezni Azure Database Migration Service a forrás-adatbázis (ok) hoz való hozzáférést az áttelepítéshez, valamint a fájlokat a 445-es SMB-porton keresztül.
- Hozzon létre egy Azure SQL Database felügyelt példányt a [Azure Portal Azure SQL Database felügyelt példány létrehozása](https://aka.ms/sqldbmi)című cikkben ismertetett részletességgel.
- Győződjön meg arról, hogy a forrás SQL Server és a célként felügyelt példány összekapcsolásához használt bejelentkezési adatok a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.

    >[!NOTE]
    >Alapértelmezés szerint a Azure Database Migration Service csak az SQL-bejelentkezések áttelepítését támogatja. Engedélyezheti azonban a Windows-bejelentkezések átmásolásának lehetőségét a következővel:
    >
    >- Annak biztosítása, hogy a célként SQL Database felügyelt példány HRE olvasási hozzáféréssel rendelkezik, amely a Azure Portal által a **vállalati rendszergazdával**vagy **globális rendszergazdai**szerepkörrel rendelkező felhasználó által is konfigurálható.
    >- A Azure Database Migration Service-példány konfigurálása a Windows felhasználói/csoportos bejelentkezési áttelepítésének engedélyezéséhez, amely a Azure Portal használatával állítható be a konfiguráció lapon. A beállítás engedélyezése után indítsa újra a szolgáltatást, hogy a módosítások érvénybe lépnek.
    >
    > A szolgáltatás újraindítása után a Windows felhasználói/csoportos bejelentkezések megjelennek az áttelepítéshez elérhető bejelentkezések listájában. Az áttelepített Windows felhasználói/csoportos bejelentkezések esetében a rendszer felszólítja a társított tartománynév megadására. A szolgáltatás felhasználói fiókjai (tartománynév: NT AUTHORITY) és virtuális felhasználói fiókok (a tartománynév NT SZOLGÁLTATÁSsal) nem támogatottak.

- Hozzon létre egy hálózati megosztást, amelyet a Azure Database Migration Service a forrás-adatbázis biztonsági mentésére használhat.
- Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példányt futtató szolgáltatásfiók írási, a forrásként szolgáló kiszolgáló számítógépes fiókja pedig olvasási és írási jogosultságokkal rendelkezik az Ön által létrehozott hálózati megosztáson.
- Jegyezzen fel egy olyan Windows-felhasználót (és jelszót), amely teljes körű jogosultságokkal rendelkezik az Ön által korábban létrehozott hálózati megosztáson. Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy a biztonsági mentési fájlokat feltöltse az Azure Storage-tárolóba a visszaállítási művelethez.
- Hozzon létre egy blobtárolót, és kérje le annak SAS URI-ját az [Azure Blob Storage-erőforrások Storage Explorerrel történő kezelésével](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) foglalkozó cikkben leírt lépések szerint. Az SAS URI létrehozásakor ügyeljen arra, hogy minden jogosultságot (írási, olvasási, törlési, listázási) kiválasszon a szabályzat ablakában. Ez a részletes Azure Database Migration Service hozzáférést biztosít a Storage-fiók tárolóhoz az adatbázisok Azure SQL Database felügyelt példányra való áttelepítéséhez használt biztonságimásolat-fájlok feltöltéséhez.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

    ![Portál-előfizetések megtekintése](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók**lehetőséget.

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

5. Válasszon ki egy meglévő virtuális hálózatot, vagy hozzon létre egyet.

    A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás-SQL Server és a célként megadott Azure SQL Database felügyelt példányhoz.

    A virtuális hálózatok Azure Portalban való létrehozásával kapcsolatos további információkért tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](https://aka.ms/DMSVnet)című cikket.

    További részletekért tekintse meg a [hálózati topológiák az Azure SQL db felügyelt példányainak áttelepítése Azure Database Migration Service használatával](https://aka.ms/dmsnetworkformi)című cikket.

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![DMS-szolgáltatás létrehozása](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

Keresse meg a létrehozott szolgáltatáspéldányt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Azure Database Migration Service összes példányának megkeresése](media/tutorial-sql-server-to-managed-instance/dms-search.png)

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
    > Ha SQL Server Integration Servicest (SSIS) használ, a DMS jelenleg nem támogatja a katalógus-adatbázis áttelepítését a SSIS-projektekhez/-csomagokhoz (SSISDB) SQL Serverról Azure SQL Database felügyelt példányra. A SSIS azonban Azure Data Factory (ADF)-ben is kiépítheti, és újra üzembe helyezheti a SSIS-projekteket/csomagokat a Azure SQL Database felügyelt példány által üzemeltetett cél SSISDB. A SSIS-csomagok áttelepítésével kapcsolatos további információkért tekintse [meg SQL Server Integration Services csomagok migrálása az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című cikket.

5. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Az **áttelepítési cél részletei** képernyőn határozza meg a cél kapcsolati adatait, amely a **AdventureWorks2012** -adatbázis áttelepítésének előre kiépített Azure SQL Database felügyelt példánya.

    Ha még nem telepítette a SQL Database felügyelt példányát, válassza ki a [hivatkozást](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) , amely segít a példány kiépítésében. Továbbra is folytathatja a projekt létrehozását, majd amikor a Azure SQL Database felügyelt példány elkészült, térjen vissza ehhez az adott projekthez az áttelepítés végrehajtásához.

    ![Cél kiválasztása](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Kattintson a **Mentés** gombra.

## <a name="select-source-databases"></a>Forrásadatbázisok kiválasztása

1. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki a migrálni kívánt forrásadatbázist.

    ![Forrásadatbázisok kiválasztása](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Kattintson a **Mentés** gombra.

## <a name="select-logins"></a>Bejelentkezési adatok kiválasztása

1. A **Bejelentkezések kiválasztása** képernyőn válassza ki a migrálni kívánt bejelentkezési adatokat.

    >[!NOTE]
    >Alapértelmezés szerint a Azure Database Migration Service csak az SQL-bejelentkezések áttelepítését támogatja. Ha engedélyezni szeretné a Windows-bejelentkezések áttelepítésének támogatását, tekintse meg a jelen oktatóanyag **Előfeltételek** című szakaszát.

    ![Bejelentkezési adatok kiválasztása](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Kattintson a **Mentés** gombra.

## <a name="configure-migration-settings"></a>Migrálási beállítások konfigurálása

1. A **Migrálási beállítások konfigurálása** képernyőn adja meg a következő adatokat:

    | | |
    |--------|---------|
    |**Válassza ki a forrás biztonsági másolatának beállítását** | Válassza **a legfrissebb biztonsági mentési fájlok megadására** szolgáló lehetőséget, ha már az összes biztonsági mentési fájllal rendelkezik, amelyet a DMS az adatbázis migrálásához használni fog. Válassza azt a lehetőséget, amely szerint **az Azure Database Migration Service fogja létrehozni a biztonsági mentési fájlokat**, ha azt szeretné, hogy a DMS először létrehozza az adatbázis teljes biztonsági másolatát, és ezt használja a migráláshoz. |
    |**Hálózatihely-megosztás** | Az a helyi SMB-hálózati megosztás, amely Azure Database Migration Service a forrásadatbázis biztonsági másolatait elvégezheti. A forrásként szolgáló SQL Server-példányt futtató szolgáltatásfióknak írási jogosultságokkal kell rendelkeznie ehhez a hálózati megosztáshoz. Adja meg a hálózati megosztáson található kiszolgáló FQDN- vagy IP-címét, például \\\servername.domainname.com\backupfolder vagy \\\IP address\backupfolder.|
    |**Felhasználónév** | Győződjön meg arról, hogy a Windows-felhasználó teljes körű jogosultságokkal rendelkezik a fent megadott hálózati megosztáson. A Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy feltöltse a biztonságimásolat-fájlokat az Azure Storage-tárolóba a visszaállítási művelethez. Ha TDE-kompatibilis adatbázisok vannak migrálásra kijelölve, a fenti Windows-felhasználónak a beépített rendszergazdai fióknak kell lennie, és a [felhasználói fiókok felügyeletét](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) le kell tiltani, hogy az Azure Database Migration Service feltölthesse és törölhesse a tanúsítványfájlokat. |
    |**Jelszó** | A felhasználó jelszava. |
    |**Tárfiók beállításai** | Az SAS URI-ja, amely hozzáférést biztosít a Azure Database Migration Service számára a Storage-fiók tárolóhoz, amelyhez a szolgáltatás feltölti a biztonságimásolat-fájlokat, és az adatbázisok áttelepítésére szolgál a felügyelt példányok Azure SQL Database. [Itt találja az arra vonatkozó tudnivalókat, hogyan kérheti le a blobtároló SAS URI-ját](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**TDE-beállítások** | Ha a forrás-adatbázisokat a transzparens adattitkosítás (TDE) engedélyezésével telepíti át, akkor írási jogosultságokkal kell rendelkeznie a célként Azure SQL Database felügyelt példányon.  Válassza ki azt az előfizetést, amelyben a Azure SQL Database felügyelt példánya kiépítve a legördülő menüből.  A legördülő menüben válassza ki a célul szolgáló **felügyelt Azure SQL Database-példányt**. |

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

3. Győződjön meg arról, hogy a célként megadott céladatbázis Azure SQL Database felügyelt példány környezete.

## <a name="next-steps"></a>Következő lépések

- Ha egy oktatóanyag azt mutatja be, hogyan telepíthet át egy adatbázist felügyelt példányra a T-SQL Restore paranccsal, tekintse meg a [biztonsági másolat visszaállítása felügyelt példányra a Restore parancs használatával](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)című témakört.
- A felügyelt példányokkal kapcsolatos információkért lásd: [Mi az a felügyelt példány](../sql-database/sql-database-managed-instance.md).
- Az alkalmazások felügyelt példányhoz való csatlakoztatásával kapcsolatos információkért lásd: [alkalmazások csatlakoztatása](../sql-database/sql-database-managed-instance-connect-app.md).
