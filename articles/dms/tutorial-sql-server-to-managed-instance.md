---
title: A DMS használatával Azure SQL Database felügyelt példányába történő áttelepítése |} A Microsoft Docs
description: Ismerkedjen meg az Azure SQL Database felügyelt példányába történő a helyszíni SQL Server migrálása az Azure Database Migration Service használatával.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/12/2018
ms.openlocfilehash: c911b096af6662e11afb4c4262b92c239d252c36
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990227"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>SQL Server migrálása az Azure SQL Database felügyelt példány DMS használatával
Az Azure Database Migration Service segítségével az adatbázisokat át egy helyszíni SQL Server-példánynak egy [Azure SQL Database felügyelt példányába](../sql-database/sql-database-managed-instance.md). A további módszereket is néhány manuális beavatkozást igénylő, tekintse meg a cikket [SQL Server-példány migrálása az Azure SQL Database felügyelt példányába történő](../sql-database/sql-database-managed-instance-migrate.md).

> [!IMPORTANT]
> Migrálási projektek SQL Serverről Azure SQL Database felügyelt példányába történő vannak előzetes verzióban érhető el, és szabályozza az [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ebben az oktatóanyagban áttelepítése a **Adventureworks2012** adatbázist az SQL Server helyi példányát egy Azure SQL Database felügyelt példánya az Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Hozza létre az Azure Database Migration Service egy példányát.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * Végezze el az áttelepítést.
> * A migrálás figyelésére.
> * Töltse le a migrálási jelentést.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag kell tennie:

- Virtuális hálózat létrehozása az Azure Database Migration Service segítségével az Azure Resource Manager üzembe helyezési modell, amely biztosítja, hogy a helyek közötti kapcsolatot a helyszíni adatforrás-kiszolgálók használatával [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Ismerje meg a hálózati topológiák az Azure SQL DB felügyelt példányainak áttelepítése az Azure Database Migration Service segítségével](https://aka.ms/dmsnetworkformi).
- Győződjön meg arról, hogy az Azure virtuális hálózat (VNET) hálózati biztonsági csoport szabályai do tiltja le a következő kommunikációs portokat 443-as, 53-as és 9354-es, 445-ös, 12000. Az Azure VNET NSG-forgalom szűrése további részletekért tekintse meg a cikket [hálózati biztonsági csoportokkal a hálózati forgalom szűrése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurálja a [forrás hozzáféréshez a Windows tűzfal](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat az Azure Database Migration Service eléréséhez a forrás SQL-kiszolgáló, amely alapértelmezés szerint az 1433-as TCP-porton.
- Ha több megnevezett az SQL Server példányai dinamikus portokat használó futtatja, érdemes az SQL Browser szolgáltatás engedélyezése és az 1434-es UDP-portot a tűzfalon keresztül való hozzáférés engedélyezése, hogy az Azure Database Migration Service csatlakozhat a forrás elnevezett példányán a kiszolgáló.
- Ha a forrásadatbázisok elé készülékként egy tűzfalat használ, előfordulhat, hogy hozzá kell tűzfalszabályokat az Azure Database Migration Service el áttelepítési, valamint fájlok SMB 445-ös porton keresztül a forrásadatbázis (ok).
- Hozzon létre egy Azure SQL Database felügyelt példánya a következő a részletek a cikkben [egy Azure SQL Database felügyelt példány létrehozása az Azure Portalon](https://aka.ms/sqldbmi).
- Győződjön meg arról, hogy a bejelentkezések, használja a forrás SQL-kiszolgáló és a cél felügyelt példány a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
- Hozzon létre egy hálózati megosztásra, az Azure Database Migration Service segítségével a forrás-adatbázis biztonsági mentése.
- Győződjön meg arról, hogy a szolgáltatásfiók fut a forrás SQL Server-példány írási jogosultsággal az Ön által létrehozott hálózati megosztáson, hogy a forráskiszolgáló számítógépfiókja rendelkezik-e olvasási/írási hozzáférést a megosztáshoz.
- Jegyezze fel a Windows-felhasználó (és jelszó), amely teljes körű jogosultságokkal rendelkezik a korábban létrehozott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználó hitelesítő adatait, a biztonsági mentési fájlok feltöltése az Azure storage-tárolóba a visszaállítási művelethez.
- Hozzon létre egy blobtárolót és az SAS URI lekérni a cikkben leírt lépések végrehajtásával [a Storage Explorer használatával kezelheti az Azure Blob Storage-erőforrások](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), ügyeljen arra, hogy közben házirend ablakban (olvasás, írás, törlés, List) minden engedélyt kiválasszon a SAS URI létrehozása. Ezt az adatot biztosít hozzáférést a tárfiók tárolója az áttelepítéshez használt biztonsági mentési fájlok feltöltése az Azure Database Migration Service-adatbázisok Azure SQL Database felügyelt példánya

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalon, válassza **minden szolgáltatás**, majd válassza ki **előfizetések**.

    ![Portál előfizetések megjelenítése](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megjelenítése](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. Az áttelepítéshez, majd a jobbra található Keresés **Microsoft.DataMigration**válassza **regisztrálása**.

    ![Erőforrás-szolgáltató regisztrálása](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Hozzon létre egy Azure Database Migration Service-példányt

1. Az Azure Portalon válassza ki a + **erőforrás létrehozása**, keressen **Azure Database Migration Service**, majd válassza ki **Azure Database Migration Service** a legördülő listából lista.

     ![Azure Piactér](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. Az a **Azure Database Migration Service** képernyőn válassza ki **létrehozás**.

    ![Azure Database Migration Service-példány létrehozása](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. Az a **Migrálási szolgáltatás létrehozása** lapon nevezze el a szolgáltatást, az előfizetést és a egy új vagy meglévő erőforráscsoportot.

4. Válasszon ki egy meglévő virtuális hálózatot (VNET), vagy hozzon létre egyet.
 
    A virtuális hálózat az Azure Database Migration Service a forrás SQL-kiszolgáló és a cél Azure SQL Database felügyelt példányába való hozzáférést biztosít.

    További információ a virtuális hálózat létrehozása az Azure Portalon, tekintse meg a cikket [hozzon létre egy virtuális hálózatot az Azure portal használatával](https://aka.ms/DMSVnet).

    További részletekért tekintse meg a cikket [hálózati topológiák az Azure SQL DB felügyelt példányainak áttelepítése az Azure Database Migration Service segítségével](https://aka.ms/dmsnetworkformi).

5. Válasszon tarifacsomagot.

    A költségek és a tarifacsomagok további információkért lásd: a [díjszabását ismertető lapon](https://aka.ms/dms-pricing).
   
    ![A DMS-szolgáltatás létrehozása](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  Válassza ki **létrehozás** szolgáltatás létrehozásához.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrehozása után keresse meg azt az Azure Portalon nyissa meg és majd hozzon létre egy új migrálási projekt.

1. Az Azure Portalon válassza ki a **minden szolgáltatás**, Azure Database Migration Service keressen, és válassza **Azure Database Migration Service**.

    ![Keresse meg az Azure Database Migration Service összes példánya](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. Az a **Azure Database Migration Service képernyő**, keresse meg a példány létrehozott, és válassza ki a példány nevét.
 
3. Válassza ki + **új Migrálási projekt**.

4. Az a **új migrálási projekt** lapon adjon meg egy nevet a projektnek a **forráskiszolgáló típusa** szövegbeviteli mezőben válasszon ki **SQL Server**, majd a **cél kiszolgáló típusa** szövegbeviteli mezőben válasszon ki **Azure SQL Database felügyelt példányába**.

   ![A DMS-projekt létrehozása](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. Válassza ki **létrehozás** a projekt létrehozásához.

## <a name="specify-source-details"></a>Adja meg az adatforrás részletei

1. Az a **forrás részletei** képernyőn, adja meg a forrás SQL-kiszolgáló kapcsolati adatait.

2. Ha nem telepítette egy megbízható tanúsítványt a kiszolgálón, válassza ki a **megbízható kiszolgálói tanúsítvány** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítványt, a SQL Server egy önaláírt tanúsítványt állít elő a példány indításakor. Ezt a tanúsítványt a hitelesítő adatokat, az ügyfélkapcsolatok titkosítására szolgál.

    > [!CAUTION]
    > SSL-kapcsolatok önaláírt tanúsítványok használata titkosított nem nyújt erős biztonságot. Man-in-the-middle támadásokra legyenek. Éles környezetben az önaláírt tanúsítványok használatát SSL-lel vagy az internethez csatlakozó kiszolgálókon nem támaszkodhat.

   ![Adatforrás részletei](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Kattintson a **Mentés** gombra.

4. Az a **válassza ki a forrásadatbázisokat** képernyőn válassza ki a **Adventureworks2012** adatbázis az áttelepítéshez.

   ![Válassza ki a Forrásadatbázisokat](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Adja meg a cél részleteit

1.  Az a **cél részletei** képernyőn, adja meg a kapcsolódási adatait a cél, amely az előre kiépített Azure SQL Database felügyelt példányába, amelyhez a **AdventureWorks2012** adatbázis legyen át.

    Ha még nem létesített az Azure SQL Database felügyelt példánya, válassza ki a **nem** segítséget nyújtanak a példány üzembe helyezése egy hivatkozáshoz. Továbbra is a projekt létrehozásának folytatásához, és ezt követően térjen vissza a az Azure SQL Database felügyelt példánya készen áll, ha az adott projekthez az áttelepítés végrehajtásához.   
 
       ![Cél kiválasztása](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  Kattintson a **Mentés** gombra.

3.  Az a **projekt összegzése** képernyőn tekintse át, ellenőrizze a részleteket a migrálási projekt társított.
 
    ![Migrálási projekt összegzése](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  Kattintson a **Mentés** gombra.   

## <a name="run-the-migration"></a>A migrálás futtatása

1.  Válassza ki a nemrég mentett projektet, jelölje be + **új tevékenység**, majd válassza ki **áttelepítés futtatása**.

    ![Új tevékenység létrehozása](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  Amikor a rendszer kéri, adja meg a forrás- és a célkiszolgálók hitelesítő adatait, és válassza ki **mentése**.

3.  Az a **válassza ki a forrásadatbázisokat** képernyőn válassza a forrásadatbázis (ok), amely az áttelepíteni kívánt ki.

    ![Válassza ki a Forrásadatbázisokat](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases2.png)

4.  Válassza ki **mentése**, majd a a **bejelentkezések kiválasztása** képernyőn válassza a migrálni kívánt felhasználói azonosítók ki.

    A jelenlegi kiadása csak támogatja áttelepítése SQL-bejelentkezésekben.

    ![Bejelentkezések kiválasztása](media\tutorial-sql-server-to-managed-instance\dms-select-logins.png)

5. Válassza ki **mentése**, majd a a **migrálási beállítások konfigurálása** képernyőn, adja meg a következő részleteket:

    | | |
    |--------|---------|
    |**Megosztott hálózati helyen** | A helyi hálózati megosztást, hogy az Azure Database Migration Service is igénybe vehet a forrás adatbázisok biztonsági másolatait. A forrás SQL Server-példányt futtató szolgáltatásfiók írási jogosultságokkal kell rendelkeznie a hálózati megosztást. Adja meg például a hálózati megosztást, a kiszolgáló teljes Tartományneve vagy IP-címét "\\\servername.domainname.com\backupfolder" vagy "\\\IP address\backupfolder".|
    |**Felhasználónév** | A windows-felhasználónév, hogy az Azure Database Migration Service megszemélyesíthet-e, és töltse fel a biztonságimásolat-fájlokat az Azure storage-tárolóba a visszaállítási művelethez. |
    |**Jelszó** | A felhasználó jelszava. |
    |**Tárfiók-beállítások** | Az SAS URI-t biztosít az Azure Database Migration Service, a storage-fiókot, amelyhez a szolgáltatás feltölti a biztonsági mentési fájlokat, és hogy tároló-hozzáféréssel rendelkező használatos áttelepítése adatbázisok az Azure SQL Database felügyelt példányain. [Ismerje meg a SAS URI blob-tároló beszerzése](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Migrálási beállítások konfigurálása](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings2.png)

5.  Válassza ki **mentése**, majd a a **Objektumáttelepítés összegzése** képernyő a **tevékenységnév** szöveget adja meg a migrálási tevékenység nevét.

    ![Objektumáttelepítés összegzése](media\tutorial-sql-server-to-managed-instance\dms-migration-summary2.png)

6. Bontsa ki a **érvényesítési lehetőség** megjelenítéséhez a szakasz a **válasszon egy ellenőrzési lehetőséget** képernyőn, adja meg, hogy a migrált adatbázis, a lekérdezés helyességének ellenőrzése, és válassza ki **mentése** .  

7. Válassza ki **áttelepítés futtatása**.

    A migrálási tevékenység ablak jelenik meg, és a tevékenység állapota **függőben lévő**.

## <a name="monitor-the-migration"></a>A migrálás figyelésére

1. A migrálási tevékenység képernyőn válassza ki a **frissítése** megjelenítheti a képernyőn.
 
   ![Az áttelepítési tevékenység folyamatban](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-in-progress.png)

2. Tovább bővítheti az adatbázisok és bejelentkezések kategóriák a megfelelő kiszolgálóhoz objektumok áttelepítési állapotának figyelése.

   ![Az áttelepítési tevékenység folyamatban](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-monitor.png)

3. Az áttelepítés befejezése után jelölje ki a **jelentés letöltése** beolvasni a részleteket az áttelepítési folyamat társított jelentést.
 
4. Ellenőrizze, hogy a céladatbázis a cél Azure SQL Database felügyelt példányába környezetre.

## <a name="next-steps"></a>További lépések

- Bemutatja, hogyan adatbázis áttelepítése egy felügyelt példányra, a T-SQL RESTORE parancs használatával foglalkozó oktatóanyagért lásd: [biztonsági másolatának visszaállítása egy felügyelt példányra, használja a restore parancs](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Felügyelt példánnyal kapcsolatos további információkért lásd: [mit jelent a felügyelt példány](../sql-database/sql-database-managed-instance.md).
- Alkalmazások a felügyelt példányokra való csatlakoztatásával kapcsolatos információkért lásd: [alkalmazások csatlakoztatása](../sql-database/sql-database-managed-instance-connect-app.md).
