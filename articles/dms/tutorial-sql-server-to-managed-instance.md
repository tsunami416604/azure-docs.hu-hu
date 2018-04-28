---
title: Azure SQL Database-felügyelt példányt telepíthet át a DMS segítségével |} Microsoft Docs
description: Ismerje meg, a helyszíni SQL Server át Azure SQL adatbázis felügyelt példányát az Azure-adatbázis áttelepítése szolgáltatás használatával.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/10/2018
ms.openlocfilehash: 6628ea218c4c7a9aacc0c2899c1ea4e5b6169b51
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>Azure SQL adatbázis felügyelt példányhoz DMS használatával SQL-kiszolgáló áttelepítése
Az Azure-adatbázis áttelepítési szolgáltatás segítségével az adatbázisok át egy helyi SQL Server-példány, egy [Azure SQL adatbázis felügyelt példány](../sql-database/sql-database-managed-instance.md) szinte nullára csökkenti állásidővel. Bizonyos időre leállítást igényel további módszereket, tekintse meg [Azure SQL adatbázis felügyelt példány SQL Server példány áttelepítés](../sql-database/sql-database-managed-instance-migrate.md).

Ebben az oktatóanyagban az áttelepítést a **Adventureworks2012** adatbázis az SQL Server helyi példánya egy Azure SQL Database az Azure-adatbázis áttelepítése szolgáltatás használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Hozzon létre egy Azure adatbázis áttelepítési szolgáltatáspéldány.
> * Áttelepítési projekt létrehozása az Azure-adatbázis áttelepítése szolgáltatás használatával.
> * Az áttelepítéshez.
> * Áttelepítésének figyelése.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez kell:

- Az Azure Resource Manager telepítési modell, amely webhelyek kapcsolatot biztosít annak a helyszíni adatforrás-kiszolgálók használatával vagy használatával hozhat létre egy VNETET az Azure-adatbázis áttelepítése szolgáltatás [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [További tudnivalók az Azure SQL DB felügyelt példány áttelepítéshez az Azure-adatbázis áttelepítése szolgáltatással hálózati topológiák](https://aka.ms/dmsnetworkformi).
- Győződjön meg arról, hogy az Azure virtuális hálózatot (VNET) hálózati biztonsági csoport szabályok tegye letiltása a következő kommunikációs portok 443-as, 53-as és 9354-es, 445-ös, 12000. További részletek az Azure VNET NSG forgalomszűrést végez, olvassa el a [hálózati forgalmat hálózati biztonsági csoportokkal](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Konfigurálja a [Windows tűzfalat a forrás hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat ahhoz, hogy az Azure adatbázis áttelepítése az SQL-kiszolgálón, amely alapértelmezés szerint 1433-as TCP-port forrás eléréséhez.
- Ha több elnevezett SQL Server-példány dinamikus portok használatával futtatja, előfordulhat, hogy kívánja az SQL Browser szolgáltatás engedélyezése és 1434 UDP-portot a tűzfalon keresztüli hozzáférés engedélyezése, hogy az Azure-adatbázis áttelepítési szolgáltatás csatlakozhat a forrás nevesített példány a kiszolgáló.
- Ha egy tűzfal készülék használ a forrásadatbázis elé, szükség lehet ahhoz, hogy az Azure adatbázis áttelepítése az áttelepítési, valamint a fájlok SMB 445-ös porton keresztül adatforrás adatbázisának vagy adatbázisainak eléréséhez Tűzfalszabályok hozzáadása.
- Hozzon létre egy Azure SQL adatbázis-felügyelt példányok cikkben található részletes [hozzon létre egy Azure SQL adatbázis-felügyelt példányt az Azure-portálon](https://aka.ms/sqldbmi).
- Győződjön meg arról, hogy a használnak az adatforrás SQL Server és felügyelt célpéldányának bejelentkezések a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
- Hozzon létre egy hálózati megosztást, amelyet az Azure-adatbázis áttelepítési szolgáltatás segítségével a forrás-adatbázis biztonsági mentése.
- Győződjön meg arról, hogy a szolgáltatás az adatforrás SQL Server-példányt futtató fióknak írási jogosultságokkal a létrehozott hálózati megosztáson.
- Jegyezze fel a Windows-felhasználó (és jelszó), amely teljes hozzáférési jogosultsággal rendelkezik a fenti létrehozott hálózati megosztáson. Az Azure-adatbázis áttelepítési szolgáltatás a biztonsági mentési fájlok feltöltése az Azure storage-tároló visszaállítási művelet a felhasználó hitelesítő adatainak megszemélyesít.
- Egy blob-tároló létrehozása és a SAS URI-JÁNAK beolvasása a cikkben szereplő lépések segítségével [kezelése az Azure Blob Storage-erőforrások a Tártallózó alkalmazással](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), ügyeljen arra, hogy válassza ki az összes engedélyt (olvasás, írás, törlés, lista) során házirend ablakban a SAS URI létrehozásához. Ez áttelepítéséhez használja a biztonsági mentési fájlok feltöltése a tároló fiók hozzáférést biztosít az Azure-adatbázis áttelepítési szolgáltatás adatbázisok Azure SQL adatbázis felügyelt példányra

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1.  Jelentkezzen be az Azure-portálon válassza **minden szolgáltatás**, majd válassza ki **előfizetések**.
![Portál előfizetések megjelenítése](media\tutorial-sql-server-to-managed-instance\portal-select-subscription.png)

1.  Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.
![erőforrás-szolgáltatók megjelenítése](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

1.  Keresés az áttelepítéshez, majd a jobbra **Microsoft.DataMigration**, jelölje be **regisztrálása**.
![erőforrás-szolgáltató regisztrálása](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)    

## <a name="create-an-azure-database-migration-service-instance"></a>Egy Azure adatbázis áttelepítési szolgáltatáspéldány létrehozása

1.  Az Azure portálon, válassza ki a **+ hozzon létre egy erőforrást**, keresse meg **Azure adatbázis áttelepítési szolgáltatás**, majd válassza ki **Azure adatbázis áttelepítési szolgáltatás** a legördülő lista.

     ![Azure Piactér](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

1.  Az a **Azure adatbázis áttelepítési szolgáltatás (előzetes verzió)** képernyőn válassza ki **létrehozása**.

    ![Azure adatbázis áttelepítési szolgáltatáspéldány létrehozása](media\tutorial-sql-server-to-managed-instance\dms-create.png)

1.  Az a **adatbázis áttelepítési szolgáltatás** képernyőn, adja meg a szolgáltatás, az előfizetés, erőforrás csoport, egy virtuális hálózatot és az árképzési szint nevét.

    A költségeket és a tarifacsomagok további információkért lásd: a [árképzést ismertető oldalra](https://aka.ms/dms-pricing). *Az Azure-adatbázis áttelepítési szolgáltatás jelenleg előzetes verzióban érhetők, és nem kell fizetnie.*

    **Hálózati:** válasszon egy meglévő, vagy hozzon létre egy új virtuális hálózat, amely a forrás SQL Server elérésére és a cél Azure SQL Database-felügyelt példányt biztosít az Azure-adatbázis áttelepítési szolgáltatás. [További tudnivalók az Azure SQL DB felügyelt példány áttelepítéshez az Azure-adatbázis áttelepítése szolgáltatással hálózati topológiák](https://aka.ms/dmsnetworkformi).

    A virtuális hálózat létrehozása az Azure-portálon további információkért lásd: [virtuális hálózat létrehozása az Azure portál használatával több alhálózattal rendelkező](https://aka.ms/DMSVnet).

    ![Hozzon létre DMS szolgáltatást](media\tutorial-sql-server-to-managed-instance\dms-create-service.png)

1.  Válassza ki **létrehozása** létrehozni a szolgáltatást.


## <a name="create-a-migration-project"></a>Áttelepítési-projekt létrehozása

A szolgáltatás létrehozása után keresse meg azt az Azure portálon, és nyissa meg.

1.  Válassza ki **+ új áttelepítési projekt**.

1.  A a **új áttelepítési projekt** meg kell adnia egy nevet a projekthez, kattintson a jobb a **server adatforrástípust** szövegmezőben, jelölje be **SQL Server**, majd a a **cél kiszolgáló típusa** szövegmezőben, jelölje be **Azure SQL adatbázis felügyelt példány**.

    ![DMS-projekt létrehozása](media\tutorial-sql-server-to-managed-instance\dms-create-project.png)

1.  Válassza ki **létrehozása** a projekt létrehozásához.

## <a name="specify-source-details"></a>Adja meg az adatforrás részletei

1.  Az a **részletek forrás** képernyőn, a forrás SQL-kiszolgáló kapcsolati adatainak megadása.

    ![Adatforrás részletei](media\tutorial-sql-server-to-managed-instance\dms-source-details.png)

1.  Válassza ki **mentése**, majd válassza ki a **Adventureworks2012** adatbázis az áttelepítéshez.

    ![Válassza ki a forrás-adatbázisok](media\tutorial-sql-server-to-managed-instance\dms-source-database.png)

## <a name="specify-target-details"></a>Adja meg a cél adatait

1.  Válassza ki **mentése**, majd a a **céloz részletek** képernyőn, adja meg a kapcsolódási adatait a cél, amely a előtti ponton aktívvá vált Azure SQL adatbázis felügyelt példánya, amelyhez a  **AdventureWorks2012** adatbázis át lesz telepítve.

    ![Válassza ki a cél](media\tutorial-sql-server-to-managed-instance\dms-target-details.png)

1.  Kattintson a **Mentés** gombra.

1.  Az a **projekt összefoglalása** képernyőn tekintse át, ellenőrizze az áttelepítési projekttel kapcsolatos részleteket.

## <a name="run-the-migration"></a>Az áttelepítés futtatása

1.  Válassza ki a nemrég mentett projektet, jelölje be **+ új tevékenység**, majd válassza ki **áttelepítés**.

    ![Új tevékenység létrehozása](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity.png)

1.  Amikor a rendszer kéri, adja meg a hitelesítő adatait a forrás- és célkiszolgálók, és válassza **mentése**.

1.  Az a **céladatbázisokhoz térkép** képernyőn, válassza ki az áttelepíteni kívánt adatforrás adatbázisának vagy adatbázisainak.

    ![Válassza ki a forrás-adatbázisok](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases.png)

1.  Válassza ki **mentése**, a **konfigurálni az áttelepítési beállításokat** képernyőn, a következő részleteket tartalmazza:

    | | |
    |--------|---------|
    |**Biztonsági mentési helye** | A helyi hálózati megosztást, hogy az Azure-adatbázis áttelepítési szolgáltatás is igénybe vehet a source adatbázis biztonsági másolatait. Az adatforrás SQL Server-példányt futtató szolgáltatásfiókot a hálózati megosztás írási jogosultságokkal kell rendelkeznie. |
    |**Felhasználónév** | A windows rendszerbeli felhasználónevet, hogy az Azure-adatbázis áttelepítési szolgáltatás megszemélyesíthet-e, és a biztonsági mentési fájlok feltöltése az Azure storage-tároló visszaállítási művelet. |
    |**Jelszó** | A fenti felhasználó jelszavát. |
    |**Tároló SAS URI-t** | SAS URI-t, amely a fiók a tároló, amelyhez a szolgáltatás feltölti a biztonsági másolat fájljait, és hogy a hozzáférést az Azure-adatbázis áttelepítése szolgáltatás szolgál áttelepítése az Azure SQL adatbázis felügyelt példány adatbázisokat. [A SAS URI blob tároló beszerzéséről](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Áttelepítési beállítások konfigurálása](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings.png)

1.  Válassza ki **mentése**, a összefoglaló az áttelepítés képernyő a **tevékenységnév** szöveg adja meg az áttelepítési tevékenység nevét.

    ![Áttelepítési összegzése](media\tutorial-sql-server-to-managed-instance\dms-migration-summary.png)


## <a name="monitor-the-migration"></a>A figyelő az áttelepítés

1.  Válassza ki az áttelepítési tevékenység a tevékenység állapota.

1.  Az áttelepítés befejezése után ellenőrizze a céladatbázisokhoz a cél Azure SQL Database felügyelt-példányt.

    ![A figyelő az áttelepítés](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration.png)

## <a name="next-steps"></a>További lépések

- Az oktatóanyag bemutatja, hogyan adatbázis áttelepítése egy felügyelt példányhoz a T-SQL RESTORE parancs használatával, lásd: [biztonsági másolat visszaállítása egy felügyelt példányhoz, a restore parancs használatával](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- További információ az adatbázis BACPAC-fájlból való importálása: [egy új Azure SQL-adatbázis BACPAC fájl importálása](../sql-database/sql-database-import.md).
- Felügyelt példány kapcsolatos információkért lásd: [Mi az, hogy a felügyelt példánya](../sql-database/sql-database-managed-instance.md).
- További információ az alkalmazások egy felügyelt példányhoz kapcsolódva: [alkalmazások csatlakozás](../sql-database/sql-database-managed-instance-connect-app.md).
