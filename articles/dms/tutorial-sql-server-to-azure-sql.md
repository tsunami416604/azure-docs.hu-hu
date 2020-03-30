---
title: 'Oktatóanyag: Az SQL Server áttelepítése offline állapotban egy SQL egyetlen adatbázisba'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan miként migrálhat az SQL Server helyszíni kiszolgálójáról egyetlen adatbázisba vagy az Azure SQL Database-adatbázisban az Azure Database Migration Service használatával offline állapotba helyezett adatbázisba.
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
ms.openlocfilehash: ff47246482bd0712ea4e741d44b12f2c6767380b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298918"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-offline-using-dms"></a>Oktatóanyag: Az SQL Server áttelepítése egyetlen adatbázisba vagy készletezett adatbázisba az Azure SQL Database-ben kapcsolat nélküli módban a DMS használatával

Az Azure Database Migration Service segítségével áttelepítheti az adatbázisokat egy helyszíni SQL Server-példányból az [Azure SQL Database szolgáltatásba.](https://docs.microsoft.com/azure/sql-database/) Ebben az oktatóanyagban az **Adventureworks2012** adatbázist az SQL Server 2016 (vagy újabb) egy helyszíni példányába telepíti át egyetlen adatbázisba vagy az Azure SQL Database-ben egyesített adatbázisba az Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> - A helyszíni adatbázis felmérése a Data Migration Assistant szolgáltatás használatával.
> - A mintaséma migrálása a Data Migration Assistant szolgáltatás használatával.
> - Hozzon létre egy Azure Database Migration Service-példányt.
> - Hozzon létre egy áttelepítési projektet az Azure Database Migration Service használatával.
> - A migrálás futtatása.
> - A migrálás monitorozása.
> - Migrálási jelentés letöltése.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk az SQL Serverről egyetlen adatbázisba vagy az Azure SQL Database-ben készletezett adatbázisba való offline áttelepítést ismerteti. Az online migrálással kapcsolatban tekintse meg az [SQL Server online migrálása az Azure SQL Database-be a DMS használatával](tutorial-sql-server-azure-sql-online.md) című cikket.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Töltse le és telepítse [az SQL Server 2016-os vagy újabb verziót.](https://www.microsoft.com/sql-server/sql-server-downloads)
- Engedélyezze a TCP/IP protokollt, amely az SQL Server Express telepítése során alapértelmezés szerint le van tiltva – kövesse a [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) cikk utasításait.
- Hozzon létre egy (vagy készletbe adott) adatbázist az Azure SQL Database-ben, amelyet az [Azure SQL Database használatával egyetlen adatbázis létrehozása az Azure SQL Database-ben című](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started)cikk ben található részletek követésével tehet meg.

    > [!NOTE]
    > Ha sql server integrációs szolgáltatásokat (SSIS) használ, és át szeretné telepíteni az SSIS-projektek/-csomagok (SSISDB) katalógus-adatbázisát az SQL Serverről az Azure SQL Database-be, a cél SSISDB automatikusan létrejön és az Ön nevében lesz kezelve, amikor az SSIS-t az Azure Data Factoryban (ADF) építi ki. Az SSIS-csomagok áttelepítéséről az SQL [Server Integration Services-csomagok áttelepítése az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című témakörben olvashat bővebben.
  
- Töltse le a [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3-as vagy újabb verzióját.
- Hozzon létre egy Microsoft Azure virtuális hálózatot az Azure adatbázis-áttelepítési szolgáltatáshoz az Azure Resource Manager telepítési modelljével, amely az [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával biztosít helyek közötti kapcsolatot a helyszíni forráskiszolgálókhoz. A virtuális hálózat létrehozásáról további információt a [Virtuális hálózati dokumentációban](https://docs.microsoft.com/azure/virtual-network/)és különösen a részletes en című rövid útmutatóban talál.

    > [!NOTE]
    > A virtuális hálózat beállítása során, ha az ExpressRoute szolgáltatást hálózati társviszony-létesítéssel használja a Microsofthoz, adja hozzá a következő [szolgáltatásvégpontokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) ahhoz az alhálózathoz, amelyben a szolgáltatás ki lesz építve:
    >
    > - Cél adatbázis-végpont (például SQL-végpont, Cosmos DB-végpont és így tovább)
    > - Tárolási végpont
    > - Szolgáltatásbusz végpontja
    >
    > Erre a konfigurációra azért van szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.
    >
    >Ha nem rendelkezik a helyszíni hálózat és az Azure közötti, a helyek közötti kapcsolattal, vagy ha a helyek közötti kapcsolat sávszélessége korlátozott, fontolja meg az Azure Database Migration Service hibrid módban (előzetes verzió) használatát. A hibrid mód egy helyszíni áttelepítési dolgozót és a felhőben futó Azure Database Migration Service egy példányát használja ki. Az Azure Database Migration Service hibrid módban történő példányának létrehozásához olvassa el az [Azure Database Migration Service példányának létrehozása hibrid módban az Azure Portal használatával](https://aka.ms/dms-hybrid-create)című témakört.

- Győződjön meg arról, hogy a virtuális hálózati biztonsági csoport szabályai nem blokkolják a következő bejövő kommunikációs portokat az Azure Database Migration Service szolgáltatásba: 443, 53, 9354, 445, 12000. Az Azure virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részleteket a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)című témakörben olvashat.
- Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat, hogy az Azure Database Migration Service hozzáférhessen a forrás SQL Server, amely alapértelmezés szerint a TCP-port 1433.
- Ha több elnevezett SQL Server-példányt futtat dinamikus portokkal, engedélyezze az SQL Browser Service szolgáltatást, és engedélyezze az 1434-es UDP-porthoz való hozzáférést a tűzfalakon keresztül, hogy az Azure Database Migration Service a forrásnévvel ellátott példányhoz kapcsolódjon Szerver.
- Ha a forrásadatbázis(ok) előtt tűzfalberendezést használ, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia ahhoz, hogy az Azure Database Migration Service hozzáférhessen a forrásadatbázis(ok)hoz az áttelepítéshez.
- Hozzon létre egy kiszolgálószintű [IP-tűzfal szabályt](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure SQL Database-kiszolgálóhoz, hogy az Azure Database Migration Service hozzáférést biztosíthasson a céladatbázisokhoz. Adja meg az Azure Database Migration Service használt virtuális hálózat alhálózati tartományát.
- Gondoskodjon róla, hogy a forrásként szolgáló SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedélyekkel.
- Gondoskodjon róla, hogy a forrásként szolgáló Azure SQL Database-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek CONTROL DATABASE engedéllyel a célként szolgáló Azure SQL adatbázisokban.

## <a name="assess-your-on-premises-database"></a>A helyszíni adatbázis felmérése

Mielőtt adatokat telepíthet át egy helyszíni SQL Server-példányból egyetlen adatbázisba vagy az Azure SQL Database-ben lévő készletbe helyezett adatbázisba, fel kell mérnie az SQL Server-adatbázist az áttelepítést megakadályozó blokkolási problémák miatt. A Data Migration Assistant 3.3-as vagy újabb verzióját használva kövesse az [SQL Server migrálási felmérés végzése](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) cikkben leírt lépéseket, és végezze el a helyszíni adatbázis felmérését. A szükséges lépések összefoglalva a következők:

1. A Data Migration Assistant eszközben válassza az Új (+) ikont, majd a **Felmérés** projekttípust.
2. Nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza az **SQL Servert**, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database** lehetőséget. Ezután a **Létrehozás** lehetőséggel hozza létre a projektet.

    Amikor az Azure SQL Database-ben egyetlen adatbázisba vagy készletbe adott adatbázisba való átáttelepítést értékeli a forrás SQL Server-adatbázisba, az alábbi értékelési jelentéstípusok közül választhat:

   - Adatbázis-kompatibilitás ellenőrzése
   - Szolgáltatásparitás ellenőrzése

    Alapértelmezés szerint mindkét jelentéstípus ki van választva.

3. A Data Migration Assistant programon belül a **Beállítások** ablakban válassza a **Tovább** lehetőséget.
4. A **Források kiválasztása** képernyőn, a **Kapcsolódás kiszolgálóhoz** párbeszédablakban adja meg az SQL-kiszolgálója adatait, majd válassza a **Csatlakozás** lehetőséget.
5. A **Források hozzáadása** párbeszédablakban válassza az **AdventureWorks2012** elemet, majd a **Hozzáadás**, végül a **Felmérés indítása** lehetőséget.

    > [!NOTE]
    > SSIS használata esetén a DMA jelenleg nem támogatja a forrás SSISDB értékelését. Az SSIS-projektek/-csomagok azonban értékelésre és érvényesítésére kerül, amint az Okat az Azure SQL Database által üzemeltetett cél SSISDB-re helyezik át. Az SSIS-csomagok áttelepítéséről az SQL [Server Integration Services-csomagok áttelepítése az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című témakörben olvashat bővebben.

    A felmérés befejeztével az eredmények a következőképpen jelennek meg:

    ![Adatmigrálás felmérése](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Az Azure SQL Database-ben egyetlen adatbázisok vagy készletezett adatbázisok esetében az értékelések azonosítják a szolgáltatásparitási és az áttelepítési blokkolási problémákat egyetlen adatbázisba vagy készletbe adott adatbázisba való üzembe helyezéshez.

    - Az **SQL Server szolgáltatásparitás** kategória átfogó javaslatokat ad, az Azure-ban elérhető alternatív megközelítéseket javasol, valamint kockázatcsökkentő lépéseket ajánl fel a migrálási projektek megtervezéséhez.
    - A **Kompatibilitási problémák** kategória azonosítja a csak részben támogatott vagy nem támogatott funkciókat, amelyek olyan kompatibilitási problémákat okozhatnak, amelyek akadályozhatják az SQL Server-adatbázis(ok) migrálását az Azure SQL Database szolgáltatásba. A felmerülő problémák megoldására a program javaslatokat is tesz.

6. Tekintse át a felmérés eredményeit, és az egyes lehetőségek kiválasztásával ellenőrizze, fennáll-e bármilyen akadályozó, illetve paritási probléma.

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása

Miután elégedett az értékeléssel, és meggyőződött arról, hogy a kiválasztott adatbázis életképes jelölt az Azure SQL Database egyetlen adatbázisba vagy készletezett adatbázisba való áttelepítéshez, a DMA segítségével telepítse át a sémát az Azure SQL Database-be.

> [!NOTE]
> Mielőtt létrehoz egy migrálási projektet a Data Migration Assistant programban, győződjön meg arról, hogy az előkövetelményekben említettek alapján már létrehozott egy Azure SQL-adatbázist. Ebben az oktatóanyagban az Azure SQL Database neve **AdventureWorksAzure**, de megadhat bármilyen másik nevet is.

> [!IMPORTANT]
> SSIS használata esetén a DMA jelenleg nem támogatja a forrás SSISDB áttelepítését, de újratelepítheti az SSIS-projekteket/csomagokat az Azure SQL Database által üzemeltetett cél SSISDB-re. Az SSIS-csomagok áttelepítéséről az SQL [Server Integration Services-csomagok áttelepítése az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című témakörben olvashat bővebben.

**Az AdventureWorks2012** séma egyetlen adatbázisba vagy azure SQL Database-adatbázisba való áttelepítéséhez hajtsa végre a következő lépéseket:

1. A Data Migration Assistant programban válassza az Új (+) ikont, majd a **Projekt típusa** alatt válassza a **Migrálás** lehetőséget.
2. Nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza az **SQL Server** elemet, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database** lehetőséget.
3. A **Migrálási hatókör** alatt válassza a **Csak a séma** lehetőséget.

    A fent leírt lépések elvégzése után megjelenik a Data Migration Assistant felhasználói felülete, ahogy az a következő képen látható:

    ![Data Migration Assistant-projekt létrehozása](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.
5. A Data Migration Assistant programban, adja meg az SQL Server forráskapcsolati adatait, válassza a **Csatlakozás** lehetőséget, majd válassza ki az **AdventureWorks2012** adatbázist.

    ![Data Migration Assistant forráskapcsolati adatok](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Válassza a **Tovább**lehetőséget, a **Csatlakozás a célkiszolgálóhoz**csoportban adja meg az Azure SQL-adatbázis célkapcsolatának részleteit, válassza a **Csatlakozás**lehetőséget, majd válassza ki **az** Azure-adatbázist, amelyet előre kiépített az Azure SQL Database-ben.

    ![Data Migration Assistant célkapcsolati adatok](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. A **Tovább** lehetőséggel lépjen tovább az **Objektumok kiválasztása** képernyőre, ahol megadhatja azokat a sémaobjektumokat az **AdventureWorks2012** adatbázisban, amelyeket üzembe kell helyezni az Azure SQL Database-ben.

    Alapértelmezés szerint az összes objektum ki van jelölve.

    ![SQL-szkriptek létrehozása](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Az **SQL-szkript létrehozása** lehetőséggel hozza létre az SQL-szkripteket, majd tekintse át azokat, hogy észrevehesse az esetleges hibákat.

    ![Sémaszkript](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Válassza a **Séma üzembe helyezése** lehetőséget a séma üzembe helyezéséhez az Azure SQL Database-ben. Az üzembe helyezés után ellenőrizze a célt az esetleges hibákért.

    ![Séma üzembe helyezése](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure portálra. Keressen rá, és válassza **az Előfizetések**lehetőséget.

   ![Portál-előfizetések megtekintése](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyben létre szeretné hozni az Azure Database Migration Service példányát, majd válassza **az Erőforrás-szolgáltatók**lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd válassza a **Regisztráció** a **Microsoft.DataMigration**programra lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Példány létrehozása

1. Az Azure Portal menüben vagy a **kezdőlapon** válassza az **Erőforrás létrehozása**lehetőséget. Keresse meg és válassza az **Azure Database Migration Service lehetőséget.**

    ![Azure Piactér](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, ahol létre szeretné hozni az Azure Database Migration Service példányát.

5. Jelöljön ki egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat hozzáférést biztosít az Azure Database Migration Service számára a forrás SQL Server és a cél Azure SQL Database-példány eléréséhez.

    A virtuális hálózat Azure Portalon való létrehozásáról további információt a Virtuális hálózat létrehozása az Azure Portal használatával című témakörben [talál.](https://aka.ms/DMSVnet)

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portal menüben válassza a **Minden szolgáltatás**lehetőséget. Keresse meg és válassza az **Azure Database Migration Services lehetőséget.**

     ![Az Azure Database Migration Service összes példányának megkeresése](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn válassza ki az Azure Database Migration Service által létrehozott példányt.

3. Válassza az **Új áttelepítési projekt lehetőséget.**

     ![Az Azure Database Migration Service példányának megkeresése](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Az **Új migrálási projekt** képernyőn nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza ki az **SQL Servert**, a **Célkiszolgáló típus** szövegbeviteli mezőben pedig az **Azure SQL Database-t**. A **Válassza ki a tevékenység típusát** szövegbeviteli mezőben válassza az **Offline adatok migrálása** lehetőséget.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server-példány kapcsolati adatait.

    Gondoskodjon róla, hogy az SQL Server-példány neveként teljes tartománynevet (FQDN) használjon. Amikor a DNS-névfeloldás nem lehetséges, az IP-címet is használhatja.

2. Ha nem telepített megbízható tanúsítványt a forráskiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítvánnyal titkosított TLS-kapcsolatok nem nyújtanak erős biztonságot. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Ne hagyatkozzon a TLS-re, amely önaláírt tanúsítványokat használ éles környezetben vagy az internethez kapcsolódó kiszolgálókon.

   ![Forrás részletei](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > SSIS használata esetén a DMS jelenleg nem támogatja az SSISDB forrás áttelepítését, de újratelepítheti az SSIS-projekteket/-csomagokat az Azure SQL Database által üzemeltetett cél SSISDB-re. Az SSIS-csomagok áttelepítéséről az SQL [Server Integration Services-csomagok áttelepítése az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című témakörben olvashat bővebben.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés** lehetőséget, majd a **Migrálási cél részletei** képernyőn adja meg a célul szolgáló Azure SQL-adatbáziskiszolgáló kapcsolati adatait. Ez a cél az az Azure SQL Database, amelyen üzembe helyezte az **AdventureWorks2012** sémát a Data Migration Assistant szolgáltatással.

    ![Cél kiválasztása](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrásadatbázis, az Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázist.

    ![Leképezés céladatbázisokra](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Válassza a **Mentés**, lehetőséget, majd a **Táblák kiválasztása** képernyőn bontsa ki a táblák listáját, és tekintse át az érintett mezőket.

    Az Azure Database Migration Service automatikusan kiválasztja az összes üres forrástáblát, amely létezik a cél Azure SQL Database-példányban. Ha újra kíván migrálni olyan táblákat, amelyek már tartalmaznak adatokat, azokat ezen a panelen külön kikell választania.

    ![Select tables (Táblák kiválasztása)](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. Válassza a **Mentés** lehetőséget. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

5. Bontsa ki az **Érvényesítési lehetőség** szakaszt a **Válasszon egy ellenőrzési lehetőséget** képernyő megjelenítéséhez, és itt adja meg, hogy a migrált adatbázisok ellenőrzése **Séma-összehasonlítás**, **Adatkonzisztencia** vagy **Lekérdezés helyessége** szerint történjen.

    ![Ellenőrzési lehetőség kiválasztása](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6. Válassza a **Mentés** lehetőséget, és tekintse át az összefoglalást, hogy meggyőződhessen arról, hogy a forrás és cél adatai megegyeznek a korábban megadottakkal.

    ![A migrálás összegzése](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>A migrálás futtatása

- Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik a migrálás műveletének ablaka. A tevékenység **Állapota**: **Függőben**.

    ![Tevékenység állapota](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>A migrálás monitorozása

1. A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Befejezve** értékre.

    ![Tevékenység állapota: Befejezve](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. A migrálás befejezése után válassza a **Jelentés letöltése** lehetőséget. A megjelenő jelentés a migrálás folyamatával kapcsolatos részleteket listázza.

3. Ellenőrizze a céladatbázisokat a célként szolgáló Azure SQL-adatbáziskiszolgálón.

### <a name="additional-resources"></a>További források

- [SQL-áttelepítés az Azure Data Migration Service](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) gyakorlati labor használatával.
- Az Azure SQL Database-be való online áttelepítések során fellépő ismert problémákról és korlátozásokról az Ismert problémák és megoldások az [Azure SQL Database online áttelepítéseivel című témakörben](known-issues-azure-sql-online.md)olvashat.
- Az Azure Database Migration Service szolgáltatásról a [Mi az Azure-adatbázis-áttelepítési szolgáltatás?](https://docs.microsoft.com/azure/dms/dms-overview)cikkben olvashat.
- Az Azure SQL Database szolgáltatásról a [Mi az Azure SQL Database szolgáltatás?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)cikkben olvashat.
