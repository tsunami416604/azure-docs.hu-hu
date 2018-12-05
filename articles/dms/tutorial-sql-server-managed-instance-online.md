---
title: 'Oktatóanyag: Az SQL Server online migrálása egy felügyelt Azure SQL Database-példányra az Azure Database Migration Service használatával | Microsoft Docs'
description: Ismerje meg, hogyan végezhet online migrálást a helyszíni SQL Serverről egy felügyelt Azure SQL Database-példányra az Azure Database Migration Service használatával.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: ccbfc6e204981a4e14812759715d0482e5b6cb67
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874759"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-managed-instance-online-using-dms"></a>Oktatóanyag: SQL Server online migrálása felügyelt Azure SQL Database-példányra a DMS használatával
Az Azure Database Migration Service használatával minimális állásidővel migrálhatja egy helyszíni SQL Server-példány adatbázisait egy [felügyelt Azure SQL Database-példányra](../sql-database/sql-database-managed-instance.md). Ha olyan módszerek is érdeklik, amelyek esetenként manuális beavatkozást is igényelhetnek, tekintse át az [SQL Server-példány felügyelt Azure SQL Database-példányra történő migrálásával](../sql-database/sql-database-managed-instance-migrate.md) foglalkozó témakört.

>[!IMPORTANT]
>Az SQL Serverről felügyelt Azure SQL Database-példányra végzett online migrálási projektekre érvényesek a [Microsoft Azure előzetes verziójú termékeire vonatkozó kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ebben az oktatóanyagban az **Adventureworks2012** adatbázist fogja migrálni minimális állásidővel az SQL Server egy helyszíni példányáról egy felügyelt Azure SQL Database-példányra az Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Egy Azure Database Migration Service-példány létrehozása.
> * Migrálási projekt létrehozása és az online migrálás indítása az Azure Database Migration Service használatával.
> * A migrálás monitorozása.
> * Átállás a migrálás elkészültekor.

> [!NOTE]
> Az Azure Database Migration Service egy online migrálás végrehajtásához használatához hozzon létre egy példányt (előzetes verzió) prémium tarifacsomag alapján.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk egy online migrálást mutat be az SQL Serverről egy felügyelt Azure SQL Database-példányra. Az offline migrálással kapcsolatban tekintse meg az [SQL Server migrálása felügyelt offline Azure SQL Database-példányra a DMS használatával](tutorial-sql-server-to-managed-instance.md) című cikket.

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
- Adjon meg egy helyszíni SMB hálózati megosztást, amely tartalmazza a teljes adatbázis biztonsági mentési fájljait és az azt követő tranzakciós naplók biztonsági mentési fájljait, amelyeket az Azure Database Migration Service használhat az adatbázis migrálásakor.
- Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példányt futtató szolgáltatásfiók írási, a forrásként szolgáló kiszolgáló számítógépes fiókja pedig olvasási és írási jogosultságokkal rendelkezik az Ön által létrehozott hálózati megosztáson.
- Jegyezzen fel egy olyan Windows-felhasználót (és jelszót), amely teljes körű jogosultságokkal rendelkezik az Ön által korábban létrehozott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy fel tudja tölteni a biztonsági mentési fájlokat az Azure Storage-tárolóba a visszaállítási művelethez.
- Hozzon létre egy Azure Active Directory-alkalmazásazonosítót, amely létrehozza az alkalmazás azonosító kulcsát, amellyel a DMS-szolgáltatás kapcsolódhat a célként szolgáló felügyelt Azure Database-példányhoz és az Azure Storage-tárolóhoz. További információ: [Azure Active Directory-alkalmazás és -szolgáltatásnév létrehozása a portálon erőforrások eléréséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)
- Hozzon létre vagy jegyezzen fel egy **standard teljesítményszintű** Azure Storage-fiókot, amelybe a DMS-szolgáltatás feltöltheti az adatbázis biztonsági mentési fájljait, majd felhasználhatja azokat az adatbázisok migrálásakor.  Ügyeljen arra, hogy az Azure Storage-tárfiók ugyanabban a régióban legyen, mint a létrehozott DMS-szolgáltatás.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

    ![Portál-előfizetések megtekintése](media\tutorial-sql-server-to-managed-instance-online\portal-select-subscriptions.png)        
2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megtekintése](media\tutorial-sql-server-to-managed-instance-online\portal-select-resource-provider.png)

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media\tutorial-sql-server-to-managed-instance-online\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keressen ár az **Azure Database Migration Service** kifejezésre, és a legördülő menüben válassza ki az **Azure Database Migration szolgáltatás** elemet.

     ![Azure Piactér](media\tutorial-sql-server-to-managed-instance-online\portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media\tutorial-sql-server-to-managed-instance-online\dms-create1.png)

3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4.  Válassza ki azt a helyet, ahol a DMS példányát létre szeretné hozni.

5. Válasszon ki egy meglévő virtuális hálózatot (VNET), vagy hozzon létre egy újat.
 
    A VNET biztosítja az Azure Database Migration Service számára a forrásként szolgáló SQL Serverhez és a felügyelt Azure SQL Database-célpéldányhoz való hozzáférést.

    További információk a virtuális hálózatok Azure Portalon történő létrehozásáról [Virtuális hálózat létrehozása az Azure Portallal](https://aka.ms/DMSVnet).

    További részletekért lásd [a felügyelt Azure SQL DB-példányok Azure Database Migration Service-szel végzett migrálásához kapcsolódó hálózati topológiákkal](https://aka.ms/dmsnetworkformi) foglalkozó témakört.

6. Válasszon egy termékváltozatot az „Üzleti szempontból kritikus (Előzetes verzió)” tarifacsomagból.

    > [!NOTE]
    > Az online migrálások végrehajtása csak az „Üzleti szempontból kritikus (Előzetes verzió)” szint használata esetén támogatott. 
   
    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.
   
    ![DMS-szolgáltatás létrehozása](media\tutorial-sql-server-to-managed-instance-online\dms-create-service3.png)

7.  A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

Keresse meg a létrehozott szolgáltatáspéldányt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Az Azure Database Migration Service minden példányának megkeresése](media\tutorial-sql-server-to-managed-instance-online\dms-search.png)

2. Az **Azure Database Migration Service** képernyőjén keresse meg a létrehozott példány nevét, és válassza ki a példányt.
 
3. Válassza a + **Új migrálási projekt** lehetőséget.

4. Az **Új migrálási projekt** képernyőn nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza ki az **SQL Servert**, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig a **felügyelt Azure SQL Database-példányt**. A **Válassza ki a tevékenység típusát** szövegbeviteli mezőben válassza az **Online adatok migrálása (Előzetes verzió)** lehetőséget.

   ![DMS-projekt létrehozása](media\tutorial-sql-server-to-managed-instance-online\dms-create-project3.png)

5. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server kapcsolati adatait.

2. Ha nem telepített megbízható tanúsítványt a kiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítványokkal titkosított SSL-kapcsolatok nem biztosítanak erős védelmet. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Éles környezetben vagy az internethez csatlakozó kiszolgálók esetén az önaláírt tanúsítványokkal működő SSL nem megbízható.

   ![Forrás részletei](media\tutorial-sql-server-to-managed-instance-online\dms-source-details2.png)

3. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1.  A **Migrálási cél adatai** képernyőn adja meg az **alkalmazásazonosítót** és a **kulcsot**, amelyekkel a DMS kapcsolódhat a célként szolgáló felügyelt Azure SQL Database-példányhoz és az Azure Storage-tárfiókhoz.

    További információ: [Azure Active Directory-alkalmazás és -szolgáltatásnév létrehozása a portálon erőforrások eléréséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)
    
2. Válassza ki a célként szolgáló felügyelt Azure SQL Database-példányt tartalmazó **Előfizetést**, majd a célként szolgáló példányt.

    Ha még nem építette ki a felügyelt Azure SQL Database-példányt, kattintson a [hivatkozásra](https://aka.ms/SQLDBMI), amely segít a példány kiépítésében. Ha a felügyelt Azure SQL Database-példány elkészült, lépjen vissza erre a projektre a migrálás végrehajtásához.

3. Adja meg az **SQL-felhasználót** és a **jelszót** a célként szolgáló felügyelt Azure SQL Database-példányhoz való csatlakozáshoz.

    ![Cél kiválasztása](media\tutorial-sql-server-to-managed-instance-online\dms-target-details3.png)

2.  Kattintson a **Mentés** gombra.

## <a name="select-source-databases"></a>Forrásadatbázisok kiválasztása

1. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki a migrálni kívánt forrásadatbázist.

    ![Forrásadatbázisok kiválasztása](media\tutorial-sql-server-to-managed-instance-online\dms-select-source-databases2.png)

2. Kattintson a **Mentés** gombra.

## <a name="configure-migration-settings"></a>Migrálási beállítások konfigurálása
 
1. A **Migrálási beállítások konfigurálása** képernyőn adja meg a következő adatokat:

    | | |
    |--------|---------|
    |**SMB hálózatihely-megosztás** | A helyszíni SMB hálózati megosztást, amely tartalmazza a teljes adatbázis biztonsági mentési fájljait és a tranzakciós naplók biztonsági mentési fájljait, amelyeket az Azure Database Migration Service használhat a migráláskor. A forrásként szolgáló SQL Server-példányt futtató szolgáltatásfióknak olvasási és írási jogosultságokkal kell rendelkeznie ehhez a hálózati megosztáshoz. Adja meg a hálózati megosztáson található kiszolgáló FQDN- vagy IP-címét, például \\\servername.domainname.com\backupfolder vagy \\\IP address\backupfolder.|
    |**Felhasználónév** | Győződjön meg arról, hogy a Windows-felhasználó teljes körű jogosultságokkal rendelkezik a fent megadott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználó hitelesítő adatait, hogy fel tudja tölteni a biztonsági mentési fájlokat az Azure Storage-tárolóba a visszaállítási művelethez. |
    |**Jelszó** | A felhasználó jelszava. |
    |**Az Azure Storage-tárfiók előfizetése** | Válassza ki az Azure Storage-tárfiókot tartalmazó előfizetést. |
    |**Azure Storage-tárfiók** | Válassza ki az Azure Storage-tárfiókot, amelybe a DMS feltöltheti az SMB hálózati megosztásból származó biztonsági mentési fájlokat, majd felhasználhatja azokat a migráláskor.  Az optimális fájlfeltöltési teljesítmény érdekében javasoljuk, hogy a tárfiók ugyanabban a régióban legyen, mint a DMS-szolgáltatás. |
    
    ![Migrálási beállítások konfigurálása](media\tutorial-sql-server-to-managed-instance-online\dms-configure-migration-settings4.png)

2. Kattintson a **Mentés** gombra.
 
## <a name="review-the-migration-summary"></a>A migrálás összefoglalásának áttekintése

1. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

2. Tekintse át és hagyja jóvá a migrálási projekttel kapcsolatos részleteket.
 
    ![Migrálási projekt áttekintése](media\tutorial-sql-server-to-managed-instance-online\dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>A migrálás futtatása és monitorozása

1. Válassza a **Migrálás futtatása** lehetőséget.

2. A migrálási tevékenység ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez.
 
   ![A migrálási tevékenység folyamatban van](media\tutorial-sql-server-to-managed-instance-online\dms-monitor-migration2.png)

    Az adatbázisok és a bejelentkezések kategóriáit is kibonthatja a kapcsolódó kiszolgálói objektumok migrálási állapotának nyomon követéséhez.

   ![A migrálási tevékenység folyamatban van](media\tutorial-sql-server-to-managed-instance-online\dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Átállásos migrálás végrehajtása

Miután a célként szolgáló felügyelt Azure SQL Database-példányon a teljes adatbázis biztonsági mentése helyre lett állítva, az adatbázison elvégezhető az átállásos migrálás.

1.  Ha készen áll az adatbázis online migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

2.  Állítsa le a forrásadatbázisok összes bejövő forgalmát.

3.  Vegye a [naplóvég biztonsági mentését], tegye elérhetővé a biztonságimásolat-fájlt az SMB hálózati megosztáson, majd várjon, amíg a rendszer helyreállítja ennek a végső tranzakciónaplónak a biztonsági másolatát.

    Ekkor a **Függőben lévő módosítások** száma 0-ra vált.

4.  Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.

    ![Teljes átállás előkészítése](media\tutorial-sql-server-to-managed-instance-online\dms-complete-cutover.png)

5.  Ha az adatbázis migrálási állapota **Befejezve** értékre vált, csatlakoztassa alkalmazásait a célként szolgáló új felügyelt Azure SQL Database-példányhoz.

    ![Az átállás befejeződött](media\tutorial-sql-server-to-managed-instance-online\dms-cutover-complete.png)

## <a name="next-steps"></a>További lépések

- Arra vonatkozó oktatóanyag, hogyan migrálható egy adatbázis egy felügyelt példányra a T-SQL RESTORE paranccsal: [Biztonsági másolat visszaállítása felügyelt példányra a visszaállítási paranccsal](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- A felügyelt példányokkal kapcsolatos információért lásd: [Mi az a felügyelt példány?](../sql-database/sql-database-managed-instance.md)
- Az alkalmazások felügyelt példányhoz való csatlakoztatásával kapcsolatos információkat az [alkalmazások csatlakoztatását ismertető](../sql-database/sql-database-managed-instance-connect-app.md) cikk tartalmazza.
