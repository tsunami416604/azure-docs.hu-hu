---
title: 'Oktatóanyag: SQL Server offline migrálása egyetlen SQL-adatbázisba'
titleSuffix: Azure Database Migration Service
description: Ismerkedjen meg az SQL Serverról Azure SQL Database offline állapotba Azure Database Migration Service használatával.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: a0d2353364767dfb1fd73d517926d0fa63c11a1d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087713"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-offline-using-dms"></a>Oktatóanyag: SQL Server migrálása Azure SQL Database-példányra kapcsolat nélküli üzemmódban, a DMS használatával

A Azure Database Migration Service segítségével áttelepítheti az adatbázisokat egy SQL Server-példányból a [Azure SQL Databaseba](https://docs.microsoft.com/azure/sql-database/). Ebben az oktatóanyagban a (z) SQL Server 2016 (vagy újabb) helyszíni példányára visszaállította a **Adventureworks2012** -adatbázist egy önálló adatbázisba vagy készletezett adatbázisba Azure SQL Database a Azure Database Migration Service használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> - A helyszíni adatbázis felmérése a Data Migration Assistant szolgáltatás használatával.
> - A mintaséma migrálása a Data Migration Assistant szolgáltatás használatával.
> - Hozzon létre egy Azure Database Migration Service-példányt.
> - Hozzon létre egy áttelepítési projektet Azure Database Migration Service használatával.
> - A migrálás futtatása.
> - Az áttelepítés monitorozása.
> - Migrálási jelentés letöltése.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk a SQL Serverról egy Azure SQL Database-adatbázisba történő offline áttelepítést ismerteti. Az online migrálással kapcsolatban tekintse meg az [SQL Server online migrálása az Azure SQL Database-be a DMS használatával](tutorial-sql-server-azure-sql-online.md) című cikket.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Töltse le és telepítse a [SQL Server 2016-es vagy újabb verzióját](https://www.microsoft.com/sql-server/sql-server-downloads).
- Engedélyezze a TCP/IP protokollt, amely az SQL Server Express telepítése során alapértelmezés szerint le van tiltva – kövesse a [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) cikk utasításait.
- Hozzon létre egy adatbázist a Azure SQL Databaseban, amelyet a következő cikkben talál: az [adatbázis létrehozása a Azure SQL Database a Azure Portal használatával](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Ha SQL Server Integration Servicest (SSIS) használ, és a katalógus-adatbázist át szeretné telepíteni a SSIS-projektekhez/csomagokhoz (SSISDB) SQL Serverról Azure SQL Databasere, akkor a rendszer automatikusan létrehozza és felügyeli a célként megadott SSISDB, amikor a SSIS-t Azure Data Factory (ADF) kiépíti. A SSIS-csomagok áttelepítésével kapcsolatos további információkért tekintse [meg SQL Server Integration Services csomagok migrálása az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című cikket.
  
- Töltse le a [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3-as vagy újabb verzióját.
- Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Service számára a Azure Resource Manager üzemi modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával. A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](https://docs.microsoft.com/azure/virtual-network/), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.

    > [!NOTE]
    > Ha a virtuális hálózat beállítása során ExpressRoute használ a Microsoft számára, adja hozzá a következő szolgáltatási [végpontokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
    >
    > - Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont stb.)
    > - Tárolási végpont
    > - Service Bus-végpont
    >
    > Erre a konfigurációra azért van szükség, mert Azure Database Migration Service nem rendelkezik internetkapcsolattal.
    >
    >Ha nem rendelkezik helyek közötti kapcsolattal a helyszíni hálózat és az Azure között, vagy ha a helyek közötti kapcsolat sávszélessége korlátozott, érdemes lehet Azure Database Migration Service hibrid módban (előzetes verzió) használni. A hibrid üzemmód egy helyszíni áttelepítési feldolgozót használ a felhőben futó Azure Database Migration Service egy példányával együtt. Azure Database Migration Service hibrid módban való létrehozásához tekintse meg a [Azure Database Migration Service-példány létrehozása hibrid módban a Azure Portal használatával](https://aka.ms/dms-hybrid-create)című cikket.

- Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport szabályai nem gátolják meg a következő bejövő kommunikációs portok Azure Database Migration Service: 443, 53, 9354, 445, 12000. Az Azure Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)című cikket.
- Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat, hogy a Azure Database Migration Service hozzáférhessen a forrás SQL Serverhoz, amely alapértelmezés szerint a 1433-as TCP-port.
- Ha több elnevezett SQL Server példányt futtat dinamikus portok használatával, akkor előfordulhat, hogy engedélyezni szeretné a SQL Browser szolgáltatást, és engedélyezni szeretné a 1434-as UDP-port elérését a tűzfalakon keresztül, így Azure Database Migration Service csatlakozhat a forráskiszolgálón megnevezett példányhoz.
- Ha a forrásadatbázis (ok) előtt tűzfal-berendezést használ, előfordulhat, hogy olyan tűzfalszabályok hozzáadására van szükség, amelyek lehetővé teszik a Azure Database Migration Service számára a forrás-adatbázis (ok) elérését az áttelepítéshez.
- Hozzon létre egy kiszolgálói szintű IP- [Tűzfalszabály](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a Azure SQL Database számára, hogy lehetővé tegye Azure Database Migration Service hozzáférést a célként megadott adatbázisokhoz. Adja meg a Azure Database Migration Service használt virtuális hálózat alhálózati tartományát.
- Gondoskodjon róla, hogy a forrásként szolgáló SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedélyekkel.
- Győződjön meg arról, hogy a TARGET Azure SQL Database-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkeznek-e az adatbázis VEZÉRLÉSére szolgáló engedéllyel a célként megadott adatbázisokon.

## <a name="assess-your-on-premises-database"></a>A helyszíni adatbázis felmérése

Mielőtt áttelepít egy SQL Server-példány adatait egy adatbázisba vagy egy Azure SQL Database készletezett adatbázisba, fel kell mérnie az SQL Server-adatbázist az áttelepítést megakadályozó esetleges blokkolási problémákra. A Data Migration Assistant 3.3-as vagy újabb verzióját használva kövesse az [SQL Server migrálási felmérés végzése](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) cikkben leírt lépéseket, és végezze el a helyszíni adatbázis felmérését. A szükséges lépések összefoglalva a következők:

1. A Data Migration Assistant eszközben válassza az Új (+) ikont, majd a **Felmérés** projekttípust.
2. Nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza az **SQL Servert**, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database** lehetőséget. Ezután a **Létrehozás** lehetőséggel hozza létre a projektet.

    Ha kiértékeli a forrás SQL Server-adatbázist, és a Azure SQL Database egy különálló adatbázisba vagy készletezett adatbázisba telepíti át, akkor a következő értékelési jelentések közül választhat:

   - Adatbázis-kompatibilitás ellenőrzése
   - Szolgáltatásparitás ellenőrzése

    Alapértelmezés szerint mindkét jelentéstípus ki van választva.

3. A Data Migration Assistant programon belül a **Beállítások** ablakban válassza a **Tovább** lehetőséget.
4. A **Források kiválasztása** képernyőn, a **Kapcsolódás kiszolgálóhoz** párbeszédablakban adja meg az SQL-kiszolgálója adatait, majd válassza a **Csatlakozás** lehetőséget.
5. A **Források hozzáadása** párbeszédablakban válassza az **AdventureWorks2012** elemet, majd a **Hozzáadás**, végül a **Felmérés indítása** lehetőséget.

    > [!NOTE]
    > Ha a SSIS-t használja, a DMA jelenleg nem támogatja a forrás-SSISDB értékelését. A SSIS-projekteket/csomagokat azonban értékeli/érvényesíti a rendszer, mivel azokat újra üzembe helyezi a Azure SQL Database által üzemeltetett cél-SSISDB. A SSIS-csomagok áttelepítésével kapcsolatos további információkért tekintse [meg SQL Server Integration Services csomagok migrálása az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című cikket.

    A felmérés befejeztével az eredmények a következőképpen jelennek meg:

    ![Adatmigrálás felmérése](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Azure SQL Database adatbázisai esetében az értékelések azonosítják a szolgáltatás paritásával kapcsolatos problémákat és az áttelepítés blokkolásával kapcsolatos problémákat egyetlen adatbázis vagy készletezett adatbázis telepítésekor.

    - Az **SQL Server szolgáltatásparitás** kategória átfogó javaslatokat ad, az Azure-ban elérhető alternatív megközelítéseket javasol, valamint kockázatcsökkentő lépéseket ajánl fel a migrálási projektek megtervezéséhez.
    - A **kompatibilitási problémák** kategóriája olyan részlegesen támogatott vagy nem támogatott funkciókat azonosít, amelyek a kompatibilitási problémákra utalnak, amelyek letiltják SQL Server adatbázis (ok) áttelepítését Azure SQL Databasere. A felmerülő problémák megoldására a program javaslatokat is tesz.

6. Tekintse át a felmérés eredményeit, és az egyes lehetőségek kiválasztásával ellenőrizze, fennáll-e bármilyen akadályozó, illetve paritási probléma.

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása

Miután elégedett az értékeléssel, és meggyőződött arról, hogy a kiválasztott adatbázis életképes jelölt az áttelepítéshez egy önálló adatbázisba vagy készletezett adatbázisba Azure SQL Databaseban, a DMA használatával telepítse át a sémát Azure SQL Databasere.

> [!NOTE]
> Mielőtt a Data Migration Assistant áttelepítési projektet hozna létre, győződjön meg róla, hogy már létrehozott egy adatbázist az Azure-ban, ahogy azt az előfeltételek között említettük. Ebben az oktatóanyagban az Azure SQL Database neve **AdventureWorksAzure**, de megadhat bármilyen másik nevet is.

> [!IMPORTANT]
> Ha a SSIS-t használja, a DMA jelenleg nem támogatja a forrás-SSISDB áttelepítését, de a SSIS-projekteket/csomagokat újból üzembe helyezheti Azure SQL Database által üzemeltetett cél-SSISDB. A SSIS-csomagok áttelepítésével kapcsolatos további információkért tekintse [meg SQL Server Integration Services csomagok migrálása az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című cikket.

A **AdventureWorks2012** -séma egyetlen adatbázisba vagy készletezett adatbázisba Azure SQL Database való áttelepítéséhez hajtsa végre a következő lépéseket:

1. A Data Migration Assistant programban válassza az Új (+) ikont, majd a **Projekt típusa** alatt válassza a **Migrálás** lehetőséget.
2. Nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza az **SQL Server** elemet, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database** lehetőséget.
3. A **Migrálási hatókör** alatt válassza a **Csak a séma** lehetőséget.

    A fent leírt lépések elvégzése után megjelenik a Data Migration Assistant felhasználói felülete, ahogy az a következő képen látható:

    ![Data Migration Assistant-projekt létrehozása](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.
5. A Data Migration Assistant programban, adja meg az SQL Server forráskapcsolati adatait, válassza a **Csatlakozás** lehetőséget, majd válassza ki az **AdventureWorks2012** adatbázist.

    ![Data Migration Assistant forráskapcsolati adatok](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Kattintson a **tovább**gombra, a **Csatlakozás a célkiszolgálóra**lehetőségnél adja meg a Azure SQL Database cél kapcsolati adatait, válassza a **Csatlakozás**lehetőséget, majd válassza ki azt a **AdventureWorksAzure** -adatbázist, amelyet a Azure SQL Database előre kiépített.

    ![Data Migration Assistant célkapcsolati adatok](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. A **Tovább** lehetőséggel lépjen tovább az **Objektumok kiválasztása** képernyőre, ahol megadhatja azokat a sémaobjektumokat az **AdventureWorks2012** adatbázisban, amelyeket üzembe kell helyezni az Azure SQL Database-ben.

    Alapértelmezés szerint az összes objektum ki van jelölve.

    ![SQL-szkriptek létrehozása](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Az **SQL-szkript létrehozása** lehetőséggel hozza létre az SQL-szkripteket, majd tekintse át azokat, hogy észrevehesse az esetleges hibákat.

    ![Sémaszkript](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Válassza a **Séma üzembe helyezése** lehetőséget a séma üzembe helyezéséhez az Azure SQL Database-ben. Az üzembe helyezés után ellenőrizze a célt az esetleges hibákért.

    ![Séma üzembe helyezése](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra. Keresse meg és válassza ki az **előfizetéseket**.

   ![Portál-előfizetések megtekintése](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók**lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd válassza a **regisztráció** a **Microsoft. DataMigration**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Példány létrehozása

1. A Azure Portal menüben vagy a **kezdőlapon** válassza az **erőforrás létrehozása**lehetőséget. Keresse meg és válassza ki a **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, amelyben létre szeretné hozni a Azure Database Migration Service példányát.

5. Válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás-SQL Server és a célként megadott Azure SQL Database példányhoz.

    Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](https://aka.ms/DMSVnet)című cikket.

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. A Azure Portal menüben válassza a **minden szolgáltatás**lehetőséget. Keresse meg és válassza ki az **Azure Database Migration Services**elemet.

     ![Azure Database Migration Service összes példányának megkeresése](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn válassza ki a létrehozott Azure Database Migration Service példányt.

3. Válassza az **új áttelepítési projekt**lehetőséget.

     ![Azure Database Migration Service példányának megkeresése](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Az **Új migrálási projekt** képernyőn nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza ki az **SQL Servert**, a **Célkiszolgáló típus** szövegbeviteli mezőben pedig az **Azure SQL Database-t**. A **Válassza ki a tevékenység típusát** szövegbeviteli mezőben válassza az **Offline adatok migrálása** lehetőséget.

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server-példány kapcsolati adatait.

    Gondoskodjon róla, hogy az SQL Server-példány neveként teljes tartománynevet (FQDN) használjon. Amikor a DNS-névfeloldás nem lehetséges, az IP-címet is használhatja.

2. Ha nem telepített megbízható tanúsítványt a forráskiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítvánnyal titkosított TLS-kapcsolatok nem biztosítanak erős biztonságot. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Az önaláírt tanúsítványokat nem szabad a TLS-t használni éles környezetben vagy az internethez csatlakozó kiszolgálókon.

   ![Forrás részletei](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > Ha a SSIS-t használja, a DMS jelenleg nem támogatja a forrás-SSISDB áttelepítését, de a SSIS-projekteket/csomagokat újra üzembe helyezheti Azure SQL Database által üzemeltetett cél-SSISDB. A SSIS-csomagok áttelepítésével kapcsolatos további információkért tekintse [meg SQL Server Integration Services csomagok migrálása az Azure-ba](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)című cikket.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés**lehetőséget, majd az **áttelepítési cél részletei** képernyőn adja meg a cél Azure SQL Database kapcsolati adatait, amely a Data Migration Assistant használatával a **AdventureWorks2012** -séma üzembe helyezésének előre kiépített Azure SQL Database.

    ![Cél kiválasztása](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrás-adatbázis, akkor a Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázis-adatbázist.

    ![Leképezés céladatbázisokra](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Válassza a **Mentés**, lehetőséget, majd a **Táblák kiválasztása** képernyőn bontsa ki a táblák listáját, és tekintse át az érintett mezőket.

    Azure Database Migration Service automatikusan kijelöli az összes üres forrástábla, amely létezik a cél Azure SQL Database példányon. Ha újra kíván migrálni olyan táblákat, amelyek már tartalmaznak adatokat, azokat ezen a panelen külön kikell választania.

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

3. Ellenőrizze a cél adatbázis (oka) t a cél Azure SQL Databaseon.

### <a name="additional-resources"></a>További források

- [SQL-áttelepítés az Azure adatáttelepítési szolgáltatásának](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) gyakorlati laborján keresztül.
- További információ a Azure SQL Database való online áttelepítéssel kapcsolatos ismert problémákról és korlátozásokról: [Azure SQL Database online áttelepítéssel kapcsolatos ismert problémák és megkerülő megoldások](known-issues-azure-sql-online.md).
- További információ a Azure Database Migration Serviceről: mi a [Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- További információ a Azure SQL Databaseről: [Mi a Azure SQL Database szolgáltatás?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
