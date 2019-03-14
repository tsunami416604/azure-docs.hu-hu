---
title: 'Oktatóanyag: Az Azure Database Migration Service használata egy Azure SQL Database felügyelt példány az SQL Server online áttelepítése |} A Microsoft Docs'
description: Megtanulhatja, hogyan hajtható végre egy online migrálás a helyszíni SQL Server egy Azure SQL Database felügyelt példány az Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 790501aff42bec483862b735a0c5de7f8107ed46
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792310"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Oktatóanyag: SQL Server migrálása az Azure SQL Database felügyelt példány DMS online használatával

Az Azure Database Migration Service segítségével az adatbázisokat át egy helyszíni SQL Server-példánynak egy [Azure SQL Database felügyelt példány](../sql-database/sql-database-managed-instance.md) minimális állásidővel. A további módszereket is néhány manuális beavatkozást igénylő, tekintse meg a cikket [SQL Server-példány migrálása az Azure SQL Database felügyelt példány](../sql-database/sql-database-managed-instance-migrate.md).

Ebben az oktatóanyagban áttelepítése a **Adventureworks2012** egy SQL Server helyi példányát az Azure SQL Database felügyelt példány az Azure Database Migration Service használatával minimális állásideje adatbázisról.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Egy Azure Database Migration Service-példány létrehozása.
> - Migrálási projekt létrehozása és az online migrálás indítása az Azure Database Migration Service használatával.
> - A migrálás monitorozása.
> - Átállás a migrálás elkészültekor.

> [!NOTE]
> Az Azure Database Migration Service online áttelepítése használatához hozzon létre egy példányt prémium tarifacsomagban alapján.
> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a migrálási folyamat optimalizálása érdekében ugyanabban az Azure-régióban hozza létre az Azure Database Migration Service-példányt, mint amelyikben a céladatbázis is található. Az adatok különböző régiók és földrajzi helyek közötti áthelyezése lelassíthatja a migrálási folyamatot, és hibákat eredményezhet.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk ismerteti egy online migrálás SQL Serverről az Azure SQL Database felügyelt példánya. Offline áttelepítés, lásd: [SQL Server Migrálása az Azure SQL Database felügyelt példány DMS offline használatával](tutorial-sql-server-to-managed-instance.md).

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

- Győződjön meg arról, hogy a virtuális hálózatok közötti hálózati biztonsági csoport szabályai nem blokkolják a következő kommunikációs portokat a 443-as, 53-as és 9354-es, 445-ös, 12000. További részletek az Azure VNET NSG-forgalom szűréséről: [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurálja a [Windows tűzfalat a forrásadatbázis-motorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse a forrásul szolgáló SQL Servert, amely alapértelmezés szerint az 1433-as TCP-porton található.
- Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
- Ha tűzfalberendezést használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz, valamint a fájlokhoz a 445-ös SMB-porton keresztül.
- Hozzon létre egy Azure SQL Database felügyelt példány az a cikk részletesen [egy Azure SQL Database felügyelt példány létrehozása az Azure Portalon](https://aka.ms/sqldbmi).
- Győződjön meg arról, hogy a bejelentkezési azonosítók kapcsolódhat a forrás SQL-kiszolgáló az a cél felügyelt példány a sysadmin (rendszergazda) kiszolgálói szerepkör tagjai.
- Adjon meg egy helyszíni SMB hálózati megosztást, amely tartalmazza a teljes adatbázis biztonsági mentési fájljait és az azt követő tranzakciós naplók biztonsági mentési fájljait, amelyeket az Azure Database Migration Service használhat az adatbázis migrálásakor.
- Győződjön meg arról, hogy a forrásként szolgáló SQL Server-példányt futtató szolgáltatásfiók írási, a forrásként szolgáló kiszolgáló számítógépes fiókja pedig olvasási és írási jogosultságokkal rendelkezik az Ön által létrehozott hálózati megosztáson.
- Jegyezzen fel egy olyan Windows-felhasználót (és jelszót), amely teljes körű jogosultságokkal rendelkezik az Ön által korábban létrehozott hálózati megosztáson. Az Azure Database Migration Service megszemélyesíti a felhasználói hitelesítő adatokat, hogy fel tudja tölteni a biztonsági mentési fájlokat az Azure Storage-tárolóba a visszaállítási művelethez.
- Hozzon létre egy Azure Active Directory-Alkalmazásazonosító, amely létrehozza az Alkalmazásazonosító kulcs, amely a DMS-szolgáltatás használatával kapcsolódni a cél Azure Database felügyelt példány és az Azure Storage-tárolóba. További információ: [Azure Active Directory-alkalmazás és -szolgáltatásnév létrehozása a portálon erőforrások eléréséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)
- Hozzon létre vagy jegyezzen fel egy **standard teljesítményszintű** Azure Storage-fiókot, amelybe a DMS-szolgáltatás feltöltheti az adatbázis biztonsági mentési fájljait, majd felhasználhatja azokat az adatbázisok migrálásakor.  Ügyeljen arra, hogy az Azure Storage-tárfiók ugyanabban a régióban legyen, mint a létrehozott DMS-szolgáltatás.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

    ![Portál-előfizetések megtekintése](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)        
2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service-példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keressen ár az **Azure Database Migration Service** kifejezésre, és a legördülő menüben válassza ki az **Azure Database Migration szolgáltatás** elemet.

     ![Azure Piactér](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, ahol a DMS példányát létre szeretné hozni.

5. Válasszon ki egy meglévő virtuális hálózatot (VNET), vagy hozzon létre egy újat.

    A virtuális hálózat az Azure Database Migration Service a forrás SQL-kiszolgáló és a cél Azure SQL Database felügyelt példány hozzáférést biztosít.

    További információk a virtuális hálózatok Azure Portalon történő létrehozásáról [Virtuális hálózat létrehozása az Azure Portallal](https://aka.ms/DMSVnet).

    További részletekért tekintse meg a cikket [hálózati topológiák az Azure SQL Database felügyelt példányainak migrálásához az Azure Database Migration Service segítségével](https://aka.ms/dmsnetworkformi).

6. Válassza ki a Termékváltozat a prémium tarifacsomag.

    > [!NOTE]
    > Csak a prémium szint használatakor online áttelepítéseket támogatja. 

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![DMS-szolgáltatás létrehozása](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

Keresse meg a létrehozott szolgáltatáspéldányt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Az **Azure Database Migration Service** képernyőjén keresse meg a létrehozott példány nevét, és válassza ki a példányt.

3. Válassza a + **Új migrálási projekt** lehetőséget.

4. Az a **új migrálási projekt** lapon adjon meg egy nevet a projektnek a **forráskiszolgáló típusa** szövegbeviteli mezőben válasszon ki **SQL Server**, a a **célkiszolgáló típus** szövegbeviteli mezőben válasszon ki **Azure SQL Database felügyelt példányába**, majd **válassza ki a tevékenység típusát**, jelölje be **Online adatáttelepítés** .

   ![DMS-projekt létrehozása](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server kapcsolati adatait.

2. Ha nem telepített megbízható tanúsítványt a kiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítványokkal titkosított SSL-kapcsolatok nem biztosítanak erős védelmet. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Éles környezetben vagy az internethez csatlakozó kiszolgálók esetén az önaláírt tanúsítványokkal működő SSL nem megbízható.

   ![Forrás részletei](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Kattintson a **Mentés** gombra.

4. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki az **Adventureworks2012** adatbázist a migráláshoz.

   ![Forrásadatbázisok kiválasztása](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Ha az SQL Server Integration Services (SSIS) használ, a DMS jelenleg nem támogatja a katalógus-adatbázis, az SSIS-projektek/csomagok (SSISDB) a migrálás SQL Serverről az Azure SQL Database felügyelt példányában. Azonban üzembe helyezése az Azure Data Factory (ADF) az SSIS és ismételt üzembe helyezése az SSIS-projektek/csomagok a cél Azure SQL Database felügyelt példány által üzemeltetett SSISDB. SSIS-csomagok áttelepítése kapcsolatos további információkért tekintse meg a cikket [áttelepítése az SQL Server Integration Services-csomagok az Azure-bA](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Az a **Migrálási cél részletei** képernyőn, adja meg a **Alkalmazásazonosító** és **kulcs** , amely a DMS-példány használatával csatlakozzon a cél Azure SQL Database-példányra felügyelt példány és az Azure Storage-fiókot.

    További információ: [Azure Active Directory-alkalmazás és -szolgáltatásnév létrehozása a portálon erőforrások eléréséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

2. Válassza ki a **előfizetés** tartalmazó a célpéldány az Azure SQL Database felügyelt példány, és válassza a célpéldány.

    Ha még nem építette az Azure SQL Database felügyelt példány, válassza ki a [hivatkozás](https://aka.ms/SQLDBMI) segítséget nyújtanak a példány üzembe helyezése. Amikor készen áll az Azure SQL Database felügyelt példány, térjen vissza a projektben az áttelepítés végrehajtásához.

3. Adja meg **SQL-felhasználó** és **jelszó** csatlakozni az Azure SQL Database felügyelt példánya.

    ![Cél kiválasztása](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Kattintson a **Mentés** gombra.

## <a name="select-source-databases"></a>Forrásadatbázisok kiválasztása

1. A **Forrásadatbázisok kiválasztása** képernyőn válassza ki a migrálni kívánt forrásadatbázist.

    ![Forrásadatbázisok kiválasztása](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

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

    ![Migrálási beállítások konfigurálása](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

2. Kattintson a **Mentés** gombra.

## <a name="review-the-migration-summary"></a>A migrálás összefoglalásának áttekintése

1. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

2. Tekintse át és hagyja jóvá a migrálási projekttel kapcsolatos részleteket.

    ![Migrálási projekt áttekintése](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>A migrálás futtatása és monitorozása

1. Válassza a **Migrálás futtatása** lehetőséget.

2. A migrálási tevékenység ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez.

   ![A migrálási tevékenység folyamatban van](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    Az adatbázisok és a bejelentkezések kategóriáit is kibonthatja a kapcsolódó kiszolgálói objektumok migrálási állapotának nyomon követéséhez.

   ![A migrálási tevékenység folyamatban van](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Átállásos migrálás végrehajtása

A teljes adatbázis biztonsági másolatának visszaállítása az Azure SQL Database felügyelt példány célpéldányát után átállás áttelepítés az adatbázis érhető el.

1. Ha készen áll az adatbázis online migrálásának befejezésére, kattintson az **Átállás indítása** gombra.

2. Állítsa le a forrásadatbázisok összes bejövő forgalmát.

3. Vegye a [naplóvég biztonsági mentését], tegye elérhetővé a biztonságimásolat-fájlt az SMB hálózati megosztáson, majd várjon, amíg a rendszer helyreállítja ennek a végső tranzakciónaplónak a biztonsági másolatát.

    Ekkor a **Függőben lévő módosítások** száma 0-ra vált.

4. Kattintson a **Megerősítés**, majd az **Alkalmaz** gombra.

    ![Teljes átállás előkészítése](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Ha az adatbázisok migrálási állapotát jeleníti meg **befejezve**, összekötheti saját alkalmazásait az Azure SQL Database felügyelt példány új célpéldányát.

    ![Az átállás befejeződött](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>További lépések

- Adatbázis áttelepítése a T-SQL RESTORE parancsot a felügyelt példány hogyan oktatóanyagért lásd: [biztonsági másolatának visszaállítása egy felügyelt példányra a restore parancs használatával](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Felügyelt példánnyal kapcsolatos további információkért lásd: [Mi az a felügyelt példány](../sql-database/sql-database-managed-instance.md).
- Felügyelt példány az alkalmazások csatlakoztatásával kapcsolatos információkért lásd: [alkalmazások csatlakoztatása](../sql-database/sql-database-managed-instance-connect-app.md).
