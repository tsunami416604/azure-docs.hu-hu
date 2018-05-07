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
ms.date: 05/07/2018
ms.openlocfilehash: bb7cc17c36809975e26c8da8beda004a0b0cfd9e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>Azure SQL adatbázis felügyelt példányhoz DMS használatával SQL-kiszolgáló áttelepítése
Az Azure-adatbázis áttelepítési szolgáltatás segítségével az adatbázisok át egy helyi SQL Server-példány, egy [Azure SQL adatbázis felügyelt példány](../sql-database/sql-database-managed-instance.md). Más módszerekkel is szükség lehet néhány manuális adatfrissítésre című cikk [Azure SQL adatbázis felügyelt példány SQL Server példány áttelepítés](../sql-database/sql-database-managed-instance-migrate.md).

> [!IMPORTANT]
> Áttelepítési projektek Azure SQL adatbázis felügyelt példány az SQL Server rendszer Preview és közölt a [kiegészítő használati feltételek Microsoft Azure előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ebben az oktatóanyagban az áttelepítést a **Adventureworks2012** adatbázis az SQL Server helyszíni példányát az Azure SQL adatbázis felügyelt példányhoz az Azure-adatbázis áttelepítése szolgáltatás használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az Azure-adatbázis áttelepítési szolgáltatás egy példányának létrehozásakor.
> * Áttelepítési projekt létrehozása az Azure-adatbázis áttelepítése szolgáltatás használatával.
> * Az áttelepítéshez.
> * Áttelepítésének figyelése.
> * Áttelepítési jelentés letöltése.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez kell:

- Az Azure Resource Manager telepítési modell, amely webhelyek kapcsolatot biztosít annak a helyszíni adatforrás-kiszolgálók használatával vagy használatával hozhat létre egy VNETET az Azure-adatbázis áttelepítése szolgáltatás [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [További tudnivalók az Azure SQL DB felügyelt példány áttelepítéshez az Azure-adatbázis áttelepítése szolgáltatással hálózati topológiák](https://aka.ms/dmsnetworkformi).
- Győződjön meg arról, hogy az Azure virtuális hálózatot (VNET) hálózati biztonsági csoport szabályok tegye letiltása a következő kommunikációs portok 443-as, 53-as és 9354-es, 445-ös, 12000. További részletek az Azure VNET NSG forgalomszűrést végez, olvassa el a [hálózati forgalmat hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurálja a [Windows tűzfalat a forrás hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat ahhoz, hogy az Azure adatbázis áttelepítése az SQL-kiszolgálón, amely alapértelmezés szerint 1433-as TCP-port forrás eléréséhez.
- Ha több elnevezett SQL Server-példány dinamikus portok használatával futtatja, előfordulhat, hogy kívánja az SQL Browser szolgáltatás engedélyezése és 1434 UDP-portot a tűzfalon keresztüli hozzáférés engedélyezése, hogy az Azure-adatbázis áttelepítési szolgáltatás csatlakozhat a forrás nevesített példány a kiszolgáló.
- Ha egy tűzfal készülék használ a forrásadatbázis elé, szükség lehet ahhoz, hogy az Azure adatbázis áttelepítése az áttelepítési, valamint a fájlok SMB 445-ös porton keresztül adatforrás adatbázisának vagy adatbázisainak eléréséhez Tűzfalszabályok hozzáadása.
- Hozzon létre egy Azure SQL adatbázis-felügyelt példányok cikkben található részletes [hozzon létre egy Azure SQL adatbázis-felügyelt példányt az Azure-portálon](https://aka.ms/sqldbmi).
- Győződjön meg arról, hogy a használnak az adatforrás SQL Server és felügyelt célpéldányának bejelentkezések a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
- Hozzon létre egy hálózati megosztást, amelyet az Azure-adatbázis áttelepítési szolgáltatás segítségével a forrás-adatbázis biztonsági mentése.
- Győződjön meg arról, hogy a forrás futtató szolgáltatásfiók SQL Server-példány rendelkezik írási jogosultsággal a létrehozott hálózati megosztáson, hogy a forráskiszolgáló számítógépfiókja rendelkezik-e olvasási/írási hozzáférést a megosztáshoz.
- Jegyezze fel a Windows-felhasználó (és jelszó), amely teljes hozzáférési jogosultsággal rendelkezik a korábban létrehozott hálózati megosztáson. Az Azure-adatbázis áttelepítési szolgáltatás a biztonsági mentési fájlok feltöltése az Azure storage-tároló visszaállítási művelet a felhasználó hitelesítő adatainak megszemélyesít.
- Egy blob-tároló létrehozása és a SAS URI-JÁNAK beolvasása a cikkben szereplő lépések segítségével [kezelése az Azure Blob Storage-erőforrások a Tártallózó alkalmazással](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), ügyeljen arra, hogy válassza ki az összes engedélyt (olvasás, írás, törlés, lista) során házirend ablakban a SAS URI létrehozásához. Ez biztosítja azt a Azure adatbázis áttelepítési szolgáltatást áttelepítéséhez használja a biztonsági mentési fájlok feltöltése a tároló fiók hozzáféréssel rendelkező adatbázisok Azure SQL adatbázis felügyelt példányra

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure-portálon válassza **minden szolgáltatás**, majd válassza ki **előfizetések**.

    ![Portál előfizetések megjelenítése](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![erőforrás-szolgáltatók megjelenítése](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. Keresés az áttelepítéshez, majd a jobbra **Microsoft.DataMigration**, jelölje be **regisztrálása**.

    ![Erőforrás-szolgáltató regisztrálása](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Egy Azure adatbázis áttelepítési szolgáltatáspéldány létrehozása

1. Az Azure portálon, válassza ki a + **hozzon létre egy erőforrást**, keresse meg **Azure adatbázis áttelepítési szolgáltatás**, majd válassza ki **Azure adatbázis áttelepítési szolgáltatás** a legördülő lista.

     ![Azure Piactér](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. Az a **Azure adatbázis áttelepítési szolgáltatás** képernyőn válassza ki **létrehozása**.

    ![Azure adatbázis áttelepítési szolgáltatáspéldány létrehozása](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. Az a **hozzon létre áttelepítési szolgáltatás** képernyőn, adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válasszon ki egy meglévő virtuális hálózatot (VNET), vagy hozzon létre egyet.
 
    A virtuális hálózat az Azure adatbázis áttelepítési szolgáltatás számára a forrás SQL Server és a cél Azure SQL Database-felügyelt példányt biztosít.

    VNET létrehozása az Azure-portálon további információkért lásd: a cikk [hozzon létre egy virtuális hálózatot az Azure portál használatával](https://aka.ms/DMSVnet).

    Részletesebben is tárgyaljuk, tekintse meg a cikket [hálózati topológiák az Azure SQL DB felügyelt példány áttelepítéshez az Azure-adatbázis áttelepítése szolgáltatással](https://aka.ms/dmsnetworkformi).

5. Válasszon tarifacsomagot.

    A költségeket és a tarifacsomagok további információkért lásd: a [árképzést ismertető oldalra](https://aka.ms/dms-pricing).
   
    ![Hozzon létre DMS szolgáltatást](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  Válassza ki **létrehozása** létrehozni a szolgáltatást.

## <a name="create-a-migration-project"></a>Áttelepítési-projekt létrehozása

A szolgáltatás létrehozása után keresse meg azt az Azure portálon, nyissa meg, és ezután hozzon létre egy új áttelepítési projektet.

1. Válassza ki az Azure-portálon **minden szolgáltatás**, keresse meg az Azure-adatbázis áttelepítése szolgáltatás, és válassza **Azure adatbázis áttelepítési szolgáltatások**.

    ![Keresse meg az Azure-adatbázis áttelepítési szolgáltatás összes példánya](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. Az a **Azure adatbázis áttelepítési szolgáltatás képernyő**, keresse meg a példány hozott létre, és válassza ki a példány nevét.
 
3. Válassza ki + **új áttelepítési projekt**.

4. A a **új áttelepítési projekt** meg kell adnia egy nevet a projekthez, kattintson a jobb a **server adatforrástípust** szövegmezőben, jelölje be **SQL Server**, majd a a **cél kiszolgáló típusa** szövegmezőben, jelölje be **Azure SQL adatbázis felügyelt példány**.

   ![DMS-projekt létrehozása](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. Válassza ki **létrehozása** a projekt létrehozásához.

## <a name="specify-source-details"></a>Adja meg az adatforrás részletei

1. Az a **részletek forrás** képernyőn, a forrás SQL-kiszolgáló kapcsolati adatainak megadása.

2. Ha a megbízható tanúsítvány a kiszolgáló nem telepítette, jelölje be a **megbízható kiszolgálói tanúsítvány** jelölőnégyzetet.

    Ha nincs telepítve megbízható tanúsítvány, a SQL Server önaláírt tanúsítványt hoz létre a példány indításakor. Ez a tanúsítvány az ügyfél-kommunikációhoz hitelesítő adatok titkosításához használható.

    > [!CAUTION]
    > SSL-kapcsolatokat, amelyek egy önaláírt tanúsítványt használ encyopted nem nyújt erős biztonságot. Azok ki vannak téve a átjárójának. Ne hagyatkozzon kizárólag éles környezetben az önaláírt tanúsítványok használatát az SSL vagy az internethez csatlakozó kiszolgálókon.

   ![Adatforrás részletei](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Kattintson a **Mentés** gombra.

4. Az a **válassza ki a forrás-adatbázisok** képernyőn, válassza ki a **Adventureworks2012** adatbázis az áttelepítéshez.

   ![Válassza ki a forrás-adatbázisok](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Adja meg a cél adatait

1.  Az a **céloz részletek** képernyőn, adja meg a kapcsolódási adatait a cél, amely a előtti ponton aktívvá vált Azure SQL adatbázis felügyelt példánya, amelyhez a **AdventureWorks2012** adatbázis lesz áttelepítve.

    Ha már nincs telepítve az Azure SQL adatbázis-felügyelt példánya, válassza ki a **nem** segítséget nyújtanak a példány kiépíteni egy hivatkozásra. Továbbra is folytatja a projekt létrehozása, és ezt követően amikor készen áll az Azure SQL adatbázis-felügyelt példány térjen vissza az adott projekthez az áttelepítés végrehajtásához.   
 
       ![Válassza ki a cél](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  Kattintson a **Mentés** gombra.

3.  Az a **projekt összefoglalása** képernyőn tekintse át, ellenőrizze az áttelepítési projekttel kapcsolatos részleteket.
 
    ![Áttelepítési projekt összefoglaló](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  Kattintson a **Mentés** gombra.   

## <a name="run-the-migration"></a>Az áttelepítés futtatása

1.  Válassza ki a nemrég mentett projektet, jelölje be + **új tevékenység**, majd válassza ki **áttelepítés**.

    ![Új tevékenység létrehozása](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  Amikor a rendszer kéri, adja meg a hitelesítő adatait a forrás- és célkiszolgálók, és válassza **mentése**.

3.  Az a **válassza ki a forrás-adatbázisok** képernyőn, válassza ki a forrás-adatbázist, amelyet át szeretne.

    ![Válassza ki a forrás-adatbázisok](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases1.png)

4.  Válassza ki **mentése**, majd a a **konfigurálni az áttelepítési beállításokat** képernyőn, a következő részleteket tartalmazza:

    | | |
    |--------|---------|
    |**Hálózati hely megosztás** | A helyi hálózati megosztást, hogy az Azure-adatbázis áttelepítési szolgáltatás is igénybe vehet a source adatbázis biztonsági másolatait. Az adatforrás SQL Server-példányt futtató szolgáltatásfiókot a hálózati megosztás írási jogosultságokkal kell rendelkeznie. Adja meg például a hálózati megosztáson található, a kiszolgáló teljes Tartománynevét vagy IP-címéről "\\\servername.domainname.com\backupfolder" vagy "\\\IP address\backupfolder".|
    |**Felhasználónév** | A windows rendszerbeli felhasználónevet, hogy az Azure-adatbázis áttelepítési szolgáltatás megszemélyesíthet-e, és a biztonsági mentési fájlok feltöltése az Azure storage-tároló visszaállítási művelet. |
    |**Jelszó** | A felhasználó jelszavát. |
    |**Tárolási fiók beállításait** | A SAS URI-t, amely a fiók a tároló, amelyhez a szolgáltatás feltölti a biztonsági másolat fájljait, és hogy a hozzáférést az Azure-adatbázis áttelepítése szolgáltatás áttelepítéséhez használt adatbázisok Azure SQL adatbázis felügyelt példányra. [A SAS URI blob tároló beszerzéséről](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Áttelepítési beállítások konfigurálása](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings1.png)

5.  Válassza ki **mentése**, majd a a **áttelepítési összegzés** képernyő a **tevékenységnév** szöveg adja meg az áttelepítési tevékenység nevét.

6. Bontsa ki a **érvényesítési lehetőség** rész a **válassza ki az érvényesítési beállítás** adja meg, hogy az áttelepített adatbázis lekérdezés helyességét ellenőrizni, majd válassza ki a jobb **mentése** .  

    ![Áttelepítési összegzése](media\tutorial-sql-server-to-managed-instance\dms-migration-summary1.png)

7. Válassza ki **áttelepítés**.

    Az áttelepítési tevékenység ablakban jelenik meg, és a tevékenység állapota **függőben lévő**.

   ![Az áttelepítési tevékenység függőben](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-pending.png)

## <a name="monitor-the-migration"></a>A figyelő az áttelepítés

1. Az áttelepítési tevékenység képernyőn válassza ki a **frissítése** frissíti a képernyőt, amíg megjelenik, hogy az áttelepítés állapota **befejezve**.
 
   ![Az áttelepítési tevékenység befejeződött](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-finished.png)

2. Az áttelepítés befejezése után válassza ki a **töltse le a jelentés** megszerezni a jelentés az áttelepítési folyamathoz tartozó részletek.
 
3. Ellenőrizze, hogy a céladatbázis a cél Azure SQL adatbázis felügyelt példány környezetre.

## <a name="next-steps"></a>További lépések

- Az oktatóanyag bemutatja, hogyan adatbázis áttelepítése egy felügyelt példányhoz a T-SQL RESTORE parancs használatával, lásd: [biztonsági másolat visszaállítása egy felügyelt példányhoz, a restore parancs használatával](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Felügyelt példány kapcsolatos információkért lásd: [Mi az, hogy a felügyelt példánya](../sql-database/sql-database-managed-instance.md).
- További információ az alkalmazások egy felügyelt példányhoz kapcsolódva: [alkalmazások csatlakozás](../sql-database/sql-database-managed-instance-connect-app.md).
