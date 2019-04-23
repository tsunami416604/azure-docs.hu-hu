---
title: 'Oktatóanyag: Azure Database Migration Service segítségével offline áttelepítése az SQL Serverről Azure SQL Database-ben egyetlen vagy készletezett adatbázis |} A Microsoft Docs'
description: Megismerheti, hogyan kell áttelepíteni a helyszíni SQL Server egy önálló adatbázis vagy az Azure SQL Database offline készletezett adatbázis az Azure Database Migration Service használatával.
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
ms.openlocfilehash: 363377dde6d8f4d5f2e51a223c6315fcb93fe237
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797244"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-offline-using-dms"></a>Oktatóanyag: SQL Server migrálása az önálló adatbázis vagy készletezett Azure SQL Database-adatbázis offline a DMS használatával

Az Azure Database Migration Service használatával migrálhatja egy helyszíni SQL Server-példány adatbázisait az [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/)-be. Ebben az oktatóanyagban áttelepítése a **Adventureworks2012** adatbázis helyreállítása az SQL Server 2016 (vagy újabb) helyszíni példányát egy önálló adatbázis vagy készletezett adatbázis az Azure SQL Database az Azure Database Migration használatával A szolgáltatás.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - A helyszíni adatbázis felmérése a Data Migration Assistant szolgáltatás használatával.
> - A mintaséma migrálása a Data Migration Assistant szolgáltatás használatával.
> - Egy Azure Database Migration Service-példány létrehozása.
> - Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> - A migrálás futtatása.
> - A migrálás monitorozása.
> - Migrálási jelentés letöltése.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk bemutatja az SQL Server-adatbázishoz egyetlen vagy készletezett Azure SQL Database-adatbázis offline áttelepítés. Az online migrálással kapcsolatban tekintse meg az [SQL Server online migrálása az Azure SQL Database-be a DMS használatával](tutorial-sql-server-azure-sql-online.md) című cikket.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Töltse le és telepítse az [SQL Server 2016-os vagy újabb verzióját](https://www.microsoft.com/sql-server/sql-server-downloads) (bármely kiadást).
- Engedélyezze a TCP/IP protokollt, amely az SQL Server Express telepítése során alapértelmezés szerint le van tiltva – kövesse a [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) cikk utasításait.
- Egyetlen (vagy készletezett) adatbázis létrehozása az Azure SQL Database, amelyeket a következő a részletek a cikkben [egy önálló adatbázis létrehozása az Azure SQL Database az Azure portal használatával](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Ha az SQL Server Integration Services (SSIS) használ, és szeretné áttelepíteni a katalógus-adatbázis, az SSIS-projektek/csomagok (SSISDB) az SQL Serverről az Azure SQL Database, a cél SSISDB létrehozott és kezelt automatikusan az Ön nevében amikor, SSIS az Azure Data Factory (ADF) üzembe helyezése. SSIS-csomagok áttelepítése kapcsolatos további információkért tekintse meg a cikket [áttelepítése az SQL Server Integration Services-csomagok az Azure-bA](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).
  
- Töltse le a [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3-as vagy újabb verzióját.
- Hozzon létre egy Azure Virtual Network (VNET) használatával az Azure Resource Manager üzembe helyezési modell, amely lehetővé teszi a helyek közötti kapcsolatot a helyszíni adatforrás-kiszolgálók használatával vagy az Azure Database Migration Service [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Virtuális hálózathoz a telepítés során, ha az ExpressRoute hálózati a Microsoft társviszony-létesítés használja, hozzá a következő szolgáltatás [végpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) az alhálózathoz, amelyben üzembe fogja helyezni a szolgáltatást:
    > - Cél adatbázis végpont (például SQL-végpont, Cosmos-DB végpont, és így tovább)
    > - Storage-végpont
    > - Service bus-végpont
    >
    > Ez a konfiguráció szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.

- Győződjön meg arról, hogy a virtuális hálózatok közötti hálózati biztonsági csoport szabályai nem blokkolják a következő bejövő kommunikációs portokat, Azure Database Migration Service: 443, 53, 9354, 445, 12000. További részletek az Azure VNET NSG-forgalom szűréséről: [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurálja a [Windows tűzfalat az adatbázismotorhoz való hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat, és engedélyezze, hogy az Azure Database Migration Service elérhesse az SQL-kiszolgáló forrását, amely alapértelmezés szerint az 1433-as TCP-port.
- Ha több megnevezett SQL Server-példányt futtat dinamikus portokkal, előnyös lehet engedélyezni az SQL Browser Service-t, és engedélyezni a tűzfalakon keresztül az 1434-es UDP-porthoz való hozzáférést. Így az Azure Database Migration Service a forráskiszolgálón található megnevezett példányhoz férhet hozzá.
- Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.
- Hozzon létre egy kiszolgálószintű IP-cím [tűzfalszabály](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a céladatbázisok Azure Database Migration Service elérésének engedélyezése az Azure SQL Database-kiszolgáló számára. Adja meg az Azure Database Migration Service-hez használt virtuális hálózat alhálózati tartományát.
- Gondoskodjon róla, hogy a forrásként szolgáló SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedélyekkel.
- Gondoskodjon róla, hogy a forrásként szolgáló Azure SQL Database-példányhoz való kapcsolódáshoz használt hitelesítő adatok rendelkezzenek CONTROL DATABASE engedéllyel a célként szolgáló Azure SQL adatbázisokban.

## <a name="assess-your-on-premises-database"></a>A helyszíni adatbázis felmérése

Áttelepítheti az adatokat a helyszíni SQL Server-példány egyetlen adatbázishoz vagy készletezett Azure SQL Database-adatbázishoz, mielőtt kell az SQL Server-adatbázis az olyan problémák elhárítását, amelyek megakadályozhatják a migrálás értékeléséhez. A Data Migration Assistant 3.3-as vagy újabb verzióját használva kövesse az [SQL Server migrálási felmérés végzése](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) cikkben leírt lépéseket, és végezze el a helyszíni adatbázis felmérését. A szükséges lépések összefoglalva a következők:

1. A Data Migration Assistant eszközben válassza az Új (+) ikont, majd a **Felmérés** projekttípust.
2. Nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza az **SQL Servert**, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database** lehetőséget. Ezután a **Létrehozás** lehetőséggel hozza létre a projektet.

    Ha az értékelni kívánt a forrás SQL Server adatbázis migrálása az önálló adatbázis vagy készletezett Azure SQL Database-adatbázishoz, választhat egyikét vagy mindkettőt a következő értékelési jelentéstípusok:

   - Adatbázis-kompatibilitás ellenőrzése
   - Szolgáltatásparitás ellenőrzése

     Alapértelmezés szerint mindkét jelentéstípus ki van választva.

3. A Data Migration Assistant programon belül a **Beállítások** ablakban válassza a **Tovább** lehetőséget.
4. A **Források kiválasztása** képernyőn, a **Kapcsolódás kiszolgálóhoz** párbeszédablakban adja meg az SQL-kiszolgálója adatait, majd válassza a **Csatlakozás** lehetőséget.
5. A **Források hozzáadása** párbeszédablakban válassza az **AdventureWorks2012** elemet, majd a **Hozzáadás**, végül a **Felmérés indítása** lehetőséget.

    > [!NOTE]
    > Az SSIS használata, ha a DMA jelenleg nem támogatja a forrás SSISDB értékelését. Azonban SSIS-projektek/csomagok mérni/érvényesíti, azokat a cél Azure SQL Database által üzemeltetett SSISDB rendszer újratelepítése. SSIS-csomagok áttelepítése kapcsolatos további információkért tekintse meg a cikket [áttelepítése az SQL Server Integration Services-csomagok az Azure-bA](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

    A felmérés befejeztével az eredmények a következőképpen jelennek meg:

    ![Adatmigrálás felmérése](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    A különálló adatbázisokat vagy készletezett adatbázisok az Azure SQL Database az értékelések határozza meg kapcsolatos funkcióparitási problémák és a egy önálló adatbázis üzembe helyezése és készletezett adatbázisok áttelepítése hátráltató.

    - Az **SQL Server szolgáltatásparitás** kategória átfogó javaslatokat ad, az Azure-ban elérhető alternatív megközelítéseket javasol, valamint kockázatcsökkentő lépéseket ajánl fel a migrálási projektek megtervezéséhez.
    - A **Kompatibilitási problémák** kategória azonosítja a csak részben támogatott vagy nem támogatott funkciókat, amelyek olyan kompatibilitási problémákat okozhatnak, amelyek akadályozhatják az SQL Server-adatbázis(ok) migrálását az Azure SQL Database szolgáltatásba. A felmerülő problémák megoldására a program javaslatokat is tesz.

6. Tekintse át a felmérés eredményeit, és az egyes lehetőségek kiválasztásával ellenőrizze, fennáll-e bármilyen akadályozó, illetve paritási probléma.

## <a name="migrate-the-sample-schema"></a>A mintaséma migrálása

Miután nem okoz gondot az értékelés az és DMA meggyőződött arról, hogy a kijelölt adatbázis egy önálló adatbázis vagy készletezett Azure SQL Database-adatbázishoz való migrálásának egy működőképes jelölt, használja a séma migrálása az Azure SQL Database.

> [!NOTE]
> Mielőtt létrehoz egy migrálási projektet a Data Migration Assistant programban, győződjön meg arról, hogy az előkövetelményekben említettek alapján már létrehozott egy Azure SQL-adatbázist. Ebben az oktatóanyagban az Azure SQL Database neve **AdventureWorksAzure**, de megadhat bármilyen másik nevet is.
> [!IMPORTANT]
> Az SSIS használata, ha a DMA jelenleg nem támogatja az áttelepítés forrás SSISDB, de, ugyanakkor az SSIS-projektek/csomagok a cél Azure SQL Database által üzemeltetett SSISDB. SSIS-csomagok áttelepítése kapcsolatos további információkért tekintse meg a cikket [áttelepítése az SQL Server Integration Services-csomagok az Azure-bA](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Áttelepíteni a **AdventureWorks2012** séma egy önálló adatbázis vagy készletezett Azure SQL Database-adatbázishoz, hajtsa végre az alábbi lépéseket:

1. A Data Migration Assistant programban válassza az Új (+) ikont, majd a **Projekt típusa** alatt válassza a **Migrálás** lehetőséget.
2. Nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza az **SQL Server** elemet, a **Célkiszolgáló típusa** szövegbeviteli mezőben pedig az **Azure SQL Database** lehetőséget.
3. A **Migrálási hatókör** alatt válassza a **Csak a séma** lehetőséget.

    A fent leírt lépések elvégzése után megjelenik a Data Migration Assistant felhasználói felülete, ahogy az a következő képen látható:

    ![Data Migration Assistant-projekt létrehozása](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.
5. A Data Migration Assistant programban, adja meg az SQL Server forráskapcsolati adatait, válassza a **Csatlakozás** lehetőséget, majd válassza ki az **AdventureWorks2012** adatbázist.

    ![Data Migration Assistant forráskapcsolati adatok](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Válassza a **Tovább**, lehetőséget. A **Csatlakozás célkiszolgálóhoz** területen alatt adja meg az Azure SQL Database célkapcsolati adatait, válassza a **Csatlakozás** lehetőséget, majd válassza ki azt az **AdventureWorksAzure** adatbázist, amelyek az Azure SQL Database-ben előzetesen kiépített.

    ![Data Migration Assistant célkapcsolati adatok](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. A **Tovább** lehetőséggel lépjen tovább az **Objektumok kiválasztása** képernyőre, ahol megadhatja azokat a sémaobjektumokat az **AdventureWorks2012** adatbázisban, amelyeket üzembe kell helyezni az Azure SQL Database-ben.

    Alapértelmezés szerint az összes objektum ki van jelölve.

    ![SQL-szkriptek létrehozása](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Az **SQL-szkript létrehozása** lehetőséggel hozza létre az SQL-szkripteket, majd tekintse át azokat, hogy észrevehesse az esetleges hibákat.

    ![Sémaszkript](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Válassza a **Séma üzembe helyezése** lehetőséget a séma üzembe helyezéséhez az Azure SQL Database-ben. Az üzembe helyezés után ellenőrizze a célt az esetleges hibákért.

    ![Séma üzembe helyezése](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, amelyen az Azure Database Migration Service példányát létre szeretné hozni.

5. Válasszon ki egy meglévő virtuális hálózatot (VNET), vagy hozzon létre egy újat.

    A VNET biztosítja az Azure Database Migration Service számára a forrásként szolgáló SQL Serverhez és az Azure SQL Database-célpéldányhoz való hozzáférést.

    További információk a virtuális hálózatok létrehozásáról az Azure Portallal: [Virtuális hálózat létrehozása az Azure Portallal](https://aka.ms/DMSVnet).

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    Ha a megfelelő Azure Database Migration Service-csomag kiválasztása segítségre van szüksége, tekintse meg a javaslatok, az a könyvelési [Itt](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

     ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.

    ![Az Azure Database Migration Service személyes példányának megkeresése](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.
4. Az **Új migrálási projekt** képernyőn nevezze el a projektet, majd a **Forráskiszolgáló típusa** szövegbeviteli mezőben válassza ki az **SQL Servert**, a **Célkiszolgáló típus** szövegbeviteli mezőben pedig az **Azure SQL Database-t**. A **Válassza ki a tevékenység típusát** szövegbeviteli mezőben válassza az **Offline adatok migrálása** lehetőséget. 

    ![Azure Database Migration Service-projekt létrehozása](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Migrálási forrás adatai** képernyőn adja meg a forrásként szolgáló SQL Server-példány kapcsolati adatait.

    Gondoskodjon róla, hogy az SQL Server-példány neveként teljes tartománynevet (FQDN) használjon. Amikor a DNS-névfeloldás nem lehetséges, az IP-címet is használhatja.

2. Ha nem telepített megbízható tanúsítványt a forráskiszolgálón, jelölje be a **Kiszolgálótanúsítvány megbízhatósága** jelölőnégyzetet.

    Ha nincs telepítve egy megbízható tanúsítvány, az SQL Server a példány indításakor előállít egy önaláírt tanúsítványt. A rendszer ezt a tanúsítványt használja az ügyfélkapcsolatok hitelesítő adatainak titkosítására.

    > [!CAUTION]
    > Az önaláírt tanúsítványokkal titkosított SSL-kapcsolatok nem biztosítanak erős védelmet. Az ilyen tanúsítványok közbeékelődéses támadásoknak vannak kitéve. Éles környezetben vagy az internethez csatlakozó kiszolgálók esetén az önaláírt tanúsítványokkal működő SSL nem megbízható.

   ![Forrás részletei](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > Az SSIS használata, ha a DMS jelenleg nem támogatja az áttelepítés forrás SSISDB, de az SSIS-projektek/csomagok a cél Azure SQL Database által üzemeltetett SSISDB, ugyanakkor. SSIS-csomagok áttelepítése kapcsolatos további információkért tekintse meg a cikket [áttelepítése az SQL Server Integration Services-csomagok az Azure-bA](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Válassza a **Mentés** lehetőséget, majd a **Migrálási cél részletei** képernyőn adja meg a célul szolgáló Azure SQL Database Server kapcsolati adatait. Ez a cél az az Azure SQL Database, amelyen üzembe helyezte az **AdventureWorks2012** sémát a Data Migration Assistant szolgáltatással.

    ![Cél kiválasztása](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Válassza a **Mentés** lehetőséget, majd a **Leképezés céladatbázisokra** képernyőn képezze le a forrás- és a céladatbázist a migráláshoz.

    Ha a céladatbázis neve megegyezik a forrásadatbázis nevével, az Azure Database Migration Service alapértelmezés szerint a céladatbázist választja ki.

    ![Leképezés céladatbázisokra](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Válassza a **Mentés**, lehetőséget, majd a **Táblák kiválasztása** képernyőn bontsa ki a táblák listáját, és tekintse át az érintett mezőket.

    Vegye figyelembe, hogy az Azure Database Migration Service automatikusan kiválasztja az összes üres forrástáblát, amely a célként szolgáló Azure SQL Database-példányon megtalálható. Ha újra kíván migrálni olyan táblákat, amelyek már tartalmaznak adatokat, azokat ezen a panelen külön kikell választania.

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

3. Ellenőrizze a céladatbázisokat a célként szolgáló Azure SQL Database-kiszolgálón.

### <a name="additional-resources"></a>További források

- [SQL-migrálás az Azure Database Migration Service (DMS) használatával](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) – gyakorlati labor.
- Ismert problémák és korlátozások online migrálást az Azure SQL Database végrehajtásakor kapcsolatos információkért lásd: a cikk [ismert problémák és megoldások az Azure SQL Database online áttelepítések](known-issues-azure-sql-online.md).
- Az Azure Database Migration Service szolgáltatással kapcsolatos tudnivalók: [Mi az Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Azure SQL Database kapcsolatos információkért tekintse meg a cikket [Mi az az Azure SQL Database szolgáltatás?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
