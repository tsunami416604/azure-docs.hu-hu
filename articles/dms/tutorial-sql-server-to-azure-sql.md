---
title: SQL-kiszolgáló áttelepítése az Azure SQL Database az Azure-adatbázis áttelepítési szolgáltatás segítségével |} Microsoft Docs
description: Megismerheti, hogyan kell áttelepíteni a helyszíni SQL Server az Azure SQL Database az Azure-adatbázis áttelepítési szolgáltatás használatával.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 06/06/2018
ms.openlocfilehash: 823f785bf33fd4d227fbdbb0f3c6b5eec914e08c
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808009"
---
# <a name="migrate-sql-server-to-azure-sql-database-using-dms"></a>Azure SQL Database szolgáltatáshoz DMS SQL-kiszolgáló áttelepítése
Az Azure-adatbázis áttelepítési szolgáltatás segítségével az adatbázisok át egy helyi SQL Server-példány, [Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/). Ebben az oktatóanyagban az áttelepítést a **Adventureworks2012** adatbázis egy helyszíni példányát az SQL Server 2016 (vagy újabb) visszaállítani egy Azure SQL Database az Azure-adatbázis áttelepítése szolgáltatás használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az adatok áttelepítési Segéd segítségével mérje fel a helyi adatbázis.
> * A minta segítségével az adatok áttelepítési Segéd áttelepítéséhez.
> * Az Azure-adatbázis áttelepítési szolgáltatás egy példányának létrehozásakor.
> * Áttelepítési projekt létrehozása az Azure-adatbázis áttelepítése szolgáltatás használatával.
> * Az áttelepítéshez.
> * Áttelepítésének figyelése.
> * Áttelepítési jelentés letöltése.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez kell:

- Töltse le és telepítse [SQL Server 2016 vagy újabb](https://www.microsoft.com/sql-server/sql-server-downloads) (minden kiadás).
- Engedélyezze a TCP/IP protokollt, amely le van tiltva alapértelmezés szerint az SQL Server Express telepítése során az a cikk utasításait követve [engedélyezheti vagy tilthatja le a hálózati protokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Az Azure SQL Database-példányt, amely a következő cikkben található részletes úgy teheti meg egy példányának létrehozása [Azure SQL-adatbázis létrehozása az Azure portálon](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Töltse le és telepítse a [adatok áttelepítési Segéd](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 vagy újabb.
- Az Azure Resource Manager telepítési modell, amely webhelyek kapcsolatot biztosít annak a helyszíni adatforrás-kiszolgálók használatával vagy használatával hozhat létre egy VNETET az Azure-adatbázis áttelepítése szolgáltatás [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Győződjön meg arról, hogy az Azure virtuális hálózatot (VNET) hálózati biztonsági csoport szabályok tegye letiltása a következő kommunikációs portok 443-as, 53-as és 9354-es, 445-ös, 12000. További részletek az Azure VNET NSG forgalomszűrést végez, olvassa el a [hálózati forgalmat hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurálja a [Windows tűzfalat a hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Nyissa meg a Windows tűzfalat ahhoz, hogy az Azure adatbázis áttelepítése az SQL-kiszolgálón, amely alapértelmezés szerint 1433-as TCP-port forrás eléréséhez.
- Ha több elnevezett SQL Server-példány dinamikus portok használatával futtatja, előfordulhat, hogy kívánja az SQL Browser szolgáltatás engedélyezése és 1434 UDP-portot a tűzfalon keresztüli hozzáférés engedélyezése, hogy az Azure-adatbázis áttelepítési szolgáltatás csatlakozhat a forrás nevesített példány a kiszolgáló.
- Egy tűzfal készülék elé a forrás-adatbázis használata esetén szükség lehet ahhoz, hogy az Azure adatbázis áttelepítése az áttelepítés forrás adatbázis(ok) eléréséhez Tűzfalszabályok hozzáadása.
- Hozzon létre egy kiszolgálószintű [tűzfalszabály](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) az Azure SQL Database-kiszolgáló eléréséhez az Azure-adatbázis áttelepítése szolgáltatás a céladatbázisokhoz. Adja meg a virtuális hálózat az Azure-adatbázis áttelepítése szolgáltatáshoz használt alhálózati tartományán.
- Győződjön meg arról, hogy rendelkezik-e az adatforrás SQL Server-példányhoz való csatlakozásnál használt hitelesítő adatok [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedélyek.
- Győződjön meg arról, hogy rendelkezik-e a cél Azure SQL Database-példányt való kapcsolódáshoz használt hitelesítő adatok FELADATVEZÉRLŐ ADATBÁZISHOZ engedéllyel a cél Azure SQL-adatbázisok.

## <a name="assess-your-on-premises-database"></a>A helyi adatbázis felmérése
Mielőtt telepítene át adatokat a helyszíni SQL Server-példány az Azure SQL Database, ki kell értékelnie az SQL Server-adatbázis az olyan problémák elhárítását, amelyek megakadályozhatják az áttelepítés. Az adatok áttelepítési Segéd v3.3 használatával, vagy később, kövesse a cikkben ismertetett [hajt végre egy SQL-kiszolgáló áttelepítési assessment](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) végrehajtásához a helyi adatbázis-értékelés. A szükséges lépések összefoglalása a következőképpen:
1.  Az adatok áttelepítési segédjében, válassza ki az új (+) ikonra, majd a **Assessment** projekttípus.
2.  Adja meg a projekt nevét a **server adatforrástípust** szövegmezőben, jelölje be **SQL Server**, a a **server Céltípust** szövegmezőben, jelölje be **Azure SQL Database**, majd válassza ki **létrehozása** a projekt létrehozásához.

    A forrás SQL Server-adatbázis áttelepítése az Azure SQL Database értékelésekor beállíthatja a következő értékelési jelentés típusok közül:
    - Adatbázis-kompatibilitás ellenőrzése
    - Funkcióparitás ellenőrzése

    Mindkét jelentéstípusra alapértelmezés szerint ki van jelölve.

3.  Az adatok áttelepítési segédjében a a **beállítások** képernyőn válassza ki **következő**.
4.  Az a **válassza ki az adatforrások** képernyő a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a kapcsolat adatait az SQL-kiszolgálóhoz, és válassza **Connect**.
5.  A a **források hozzáadását** párbeszédpanelen jelölje ki **AdventureWorks2012**, jelölje be **hozzáadása**, majd válassza ki **Start Assessment**.

    Ha az értékelés befejeződött, az eredményekben az alábbi ábrán látható módon:

    ![Adatok áttelepítése felmérése](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    Azure SQL Database, a az értékelések határozza meg a szolgáltatás paritásos problémákat és áttelepítési problémák elhárítását.

    - A **SQL Server szolgáltatásparitást** kategória ajánlások, Azure-ban érhető el alternatív módszerek széles választékát nyújtja, és orvoslása lépések történő áttelepítés projektjeikbe részéről az erőfeszítés tervezéséhez nyújtanak segítséget.
    - A **kompatibilitási problémák** azonosító részben támogatott vagy nem támogatott funkciókat, hogy tükrözze a kompatibilitási problémák, amelyek blokkolhatják a áttelepítése a helyszíni SQL Server adatbázis (oka) Azure SQL Database. Javaslatok segítségével hárítsa el ezeket a problémákat is biztosítja.

6.  Tekintse át az értékelési eredmények áttelepítési problémák elhárítását, és a szolgáltatás paritásos problémák az adott beállítások bejelölésével.

## <a name="migrate-the-sample-schema"></a>A minta áttelepítéséhez
Miután részeként irányul, és meggyőződött arról, hogy a kijelölt adatbázis áttelepítése az Azure SQL Database életképes jelöltségét ellenőrző, használja az adatok áttelepítési Segéd áttelepítéséhez a séma az Azure SQL Database.

> [!NOTE]
> Az adatok áttelepítési Segéd hoz létre az áttelepítési projektet, előtt mindenképpen, hogy már ellátta Azure SQL-adatbázis az Előfeltételek említetteknek megfelelően. Ez az oktatóanyag céljából, az Azure SQL-adatbázis neve adottnak **AdventureWorksAzure**, de megadhatja a kívánt nevet.

Áttelepíteni a **AdventureWorks2012** séma az Azure SQL Database, a következő lépésekkel:

1.  Az adatok áttelepítési Segéd, válassza ki az új (+) ikonra, majd a **projekttípus**, jelölje be **áttelepítési**.
2.  Adja meg a projekt nevét a **server adatforrástípust** szövegmezőben, jelölje be **SQL Server**, majd a a **server Céltípust** szövegmezőben, jelölje be **Azure SQL Adatbázis**.
3.  A **áttelepítési hatókör**, jelölje be **séma csak**.

    Az előző lépések elvégzése után az adatok áttelepítési Segéd felület megjelenjen-e az alábbi ábrán látható módon:
    
    ![Adatok áttelepítése Segéd projekt létrehozása](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

4.  Válassza ki **létrehozása** a projekt létrehozásához.
5.  Az adatok áttelepítési Segéd, adja meg az adatforrás kapcsolódási adatait az SQL Server, válassza ki a **Connect**, majd válassza ki a **AdventureWorks2012** adatbázis.

    ![Adatok áttelepítése Segéd adatforrás kapcsolódási adatait.](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)

6.  Válassza ki **tovább**a **kapcsolódás a célkiszolgáló**, adja meg a célként megadott kapcsolódási adatait az Azure SQL-adatbázis, válassza ki **Connect**, majd válassza ki a **AdventureWorksAzure** kellett előzetes kiosztása az Azure SQL database adatbázishoz.

    ![Adatok áttelepítése Segéd cél kapcsolódási adatait.](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)

7.  Válassza ki **következő** ahhoz, hogy értékről a **objektumok kijelölése** képernyő, amelyen megadhatja az adatbázisséma objektumaiban a a **AdventureWorks2012** adatbázis, amely kell üzembe helyezni az Azure-bA SQL-adatbázis.

    Alapértelmezés szerint minden objektum ki lesz választva.

    ![SQL-parancsfájlok létrehozása](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)

8.  Válassza ki **létrehozni SQL-parancsfájl** az SQL-parancsfájlok létrehozásához, és tekintse át a hibákat a parancsfájlokat.

    ![Séma parancsfájl](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)

9.  Válassza ki **telepítés séma** a séma telepítéséhez az Azure SQL Database és a séma telepítése után ellenőrizze a célkiszolgálón a bármely rendellenességeket.

    ![Séma telepítése](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása
1. Jelentkezzen be az Azure-portálon válassza **minden szolgáltatás**, majd válassza ki **előfizetések**.
 
   ![Portál előfizetések megjelenítése](media\tutorial-sql-server-to-azure-sql\portal-select-subscription1.png)
       
2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.
 
    ![erőforrás-szolgáltatók megjelenítése](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)
    
3.  Keresés az áttelepítéshez, majd a jobbra **Microsoft.DataMigration**, jelölje be **regisztrálása**.
 
    ![Erőforrás-szolgáltató regisztrálása](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Hozzon létre egy példányt
1.  Válassza ki az Azure-portálon + **hozzon létre egy erőforrást**, keresse meg az Azure-adatbázis áttelepítése szolgáltatás, és válassza **Azure adatbázis áttelepítési szolgáltatás** a legördülő listából.

    ![Azure Piactér](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)

2.  Az a **Azure adatbázis áttelepítési szolgáltatás** képernyőn válassza ki **létrehozása**.
 
    ![Azure adatbázis áttelepítési szolgáltatáspéldány létrehozása](media\tutorial-sql-server-to-azure-sql\dms-create1.png)
  
3.  Az a **hozzon létre áttelepítési szolgáltatás** képernyőn, adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válasszon ki egy meglévő virtuális hálózatot (VNET), vagy hozzon létre egy újat.

    A virtuális hálózat biztosít a forrás SQL Server elérésére az Azure-adatbázis áttelepítése szolgáltatás és a cél Azure SQL Database-példányt.

    Egy VNETET az Azure portálon létrehozásával kapcsolatos további információkért lásd: a cikk [hozzon létre egy virtuális hálózatot az Azure portál használatával](https://aka.ms/DMSVnet).

5. Válasszon tarifacsomagot.

    A költségeket és a tarifacsomagok további információkért lásd: a [árképzést ismertető oldalra](https://aka.ms/dms-pricing).

    Ha segítségre van szüksége a megfelelő Azure adatbázis áttelepítési szolgáltatási rétegben kiválasztása van szüksége, tekintse meg a javaslatok, az a könyvelési [Itt](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Azure adatbázis áttelepítési példány beállításainak megadása](media\tutorial-sql-server-to-azure-sql\dms-settings1.png)

6.  Válassza ki **létrehozása** létrehozni a szolgáltatást.

## <a name="create-a-migration-project"></a>Áttelepítési-projekt létrehozása
A szolgáltatás létrehozása után keresse meg azt az Azure portálon, nyissa meg, és ezután hozzon létre egy új áttelepítési projektet.

1. Válassza ki az Azure-portálon **minden szolgáltatás**, keresse meg az Azure-adatbázis áttelepítése szolgáltatás, és válassza **Azure adatbázis áttelepítési szolgáltatások**.
 
      ![Keresse meg az Azure-adatbázis áttelepítési szolgáltatás összes példánya](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. Az a **Azure adatbázis áttelepítési szolgáltatások** képernyőn, keresse meg az Azure-adatbázis áttelepítési szolgáltatás nevének példány létrehozott, majd válassza ki a példány.
 
     ![Keresse meg az Azure-adatbázis áttelepítési szolgáltatás példányát](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Válassza ki + **új áttelepítési projekt**.
4. A a **új áttelepítési projekt** meg kell adnia egy nevet a projekthez, kattintson a jobb a **server adatforrástípust** szövegmezőben, jelölje be **SQL Server**, majd a a **cél kiszolgáló típusa** szövegmezőben, jelölje be **Azure SQL Database**.

    ![Adatbázis áttelepítési Service-projekt létrehozása](media\tutorial-sql-server-to-azure-sql\dms-create-project1.png)

5.  Válassza ki **létrehozása** a projekt létrehozásához.

## <a name="specify-source-details"></a>Adja meg az adatforrás részletei
1. Az a **részletek forrás** jobb kapcsolati adatainak megadása az adatforrás SQL Server-példány.
 
    Ügyeljen arra, hogy a teljes tartomány neve (FQDN) használ a forrás SQL Server-példány neve. Az IP-cím is használható olyan helyzetekben, mely DNS-név feloldása nem lehetséges.

2. Ha nem telepítette a megbízható tanúsítványt a forráskiszolgálón, válassza ki a **megbízható kiszolgálói tanúsítvány** jelölőnégyzetet.

    Ha nincs telepítve megbízható tanúsítvány, a SQL Server önaláírt tanúsítványt hoz létre a példány indításakor. Ez a tanúsítvány az ügyfél-kommunikációhoz hitelesítő adatok titkosításához használható.

    > [!CAUTION]
    > SSL-kapcsolatokat, amelyek egy önaláírt tanúsítványt használ encyopted nem nyújt erős biztonságot. Azok ki vannak téve a átjárójának. Ne hagyatkozzon kizárólag éles környezetben az önaláírt tanúsítványok használatát az SSL vagy az internethez csatlakozó kiszolgálókon.

   ![Adatforrás részletei](media\tutorial-sql-server-to-azure-sql\dms-source-details1.png)
  
2. Válassza ki **mentése**, majd válassza ki a **AdventureWorks2012** adatbázis az áttelepítéshez.

    ![Válassza ki a forrás DB](media\tutorial-sql-server-to-azure-sql\dms-select-source-db1.png)

## <a name="specify-target-details"></a>Adja meg a cél adatait
1. Válassza ki **mentése**, majd a a **céloz részletek** képernyőn, adja meg a kapcsolódási adatait a cél Azure SQL adatbázis-kiszolgáló, amely a előtti ponton aktívvá vált Azure SQL adatbázis, amelyhez a **AdventureWorks2012** séma használatával az adatok áttelepítési Segéd telepített.

    ![Válassza ki a cél](media\tutorial-sql-server-to-azure-sql\dms-select-target1.png)

2. Válassza ki **mentése** menteni a projektet.

3. Az a **projekt összefoglalása** képernyőn tekintse át, ellenőrizze az áttelepítési projekttel kapcsolatos részleteket.

    ![DMS összegzése](media\tutorial-sql-server-to-azure-sql\dms-summary1.png)

4. Kattintson a **Mentés** gombra.

## <a name="run-the-migration"></a>Az áttelepítés futtatása
1.  Válassza ki a nemrég mentett projektet, jelölje be + **új tevékenység**, majd válassza ki **áttelepítés**.

    ![Új tevékenység](media\tutorial-sql-server-to-azure-sql\dms-new-activity1.png)

2.  Amikor a rendszer kéri, adja meg a hitelesítő adatait a forrás- és célkiszolgálók, és válassza **mentése**.

3.  Az a **céladatbázisokhoz térkép** képernyőn, a forrás és a céladatbázis az áttelepítéshez.

    Ha a céladatbázis adatbázis neve megegyezik a forrásadatbázis, Azure DMS a céladatbázis alapértelmezés szerint választja ki.

    ![Leképezés céladatbázisokra](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity1.png)

4. Válassza ki **mentése**, a **táblák kiválasztása** képernyőn, bontsa ki a felsoroló táblát, és tekintse át az érintett mezők listája.

    Vegye figyelembe, hogy az Azure-adatbázis áttelepítése szolgáltatás automatikus jelöli ki, amely létezik a cél Azure SQL Database-példányt összes üres forrás tábla. Ha szeretné újra frissíteni a táblázatot, amely már tartalmazza az adatokat, explicit módon válassza ki a panel táblákat szeretné.

    ![Select tables (Táblák kiválasztása)](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity1.png)

5.  Válassza ki **mentése**, a a **áttelepítési összegzés** képernyő a **tevékenységnév** szöveg adja meg az áttelepítési tevékenység nevét.

6. Bontsa ki a **érvényesítési lehetőség** rész a **válassza ki az érvényesítési beállítás** adja meg, hogy az áttelepített adatbázisok érvényesítéséhez kattintson a jobb **séma összehasonlító**, **Adatkonzisztenciát**, és **lekérdezési nézetet**.
    
    ![Válasszon egy ellenőrzési lehetőséget](media\tutorial-sql-server-to-azure-sql\dms-configuration1.png)

6.  Válassza ki **mentése**, tekintse át az összefoglalást, annak érdekében, hogy a forrás és cél részletei egyeznek-e a mi korábban megadott.

    ![Áttelepítési összegzése](media\tutorial-sql-server-to-azure-sql\dms-run-migration1.png)

7.  Válassza ki **áttelepítés**.

    Az áttelepítési tevékenység ablak megjelenik, és a **állapot** a tevékenység van **függőben lévő**.

    ![Tevékenységi állapot](media\tutorial-sql-server-to-azure-sql\dms-activity-status1.png)

## <a name="monitor-the-migration"></a>A figyelő az áttelepítés
1. Az áttelepítési tevékenység képernyőn válassza ki a **frissítése** frissíti a képernyőt, amíg a **állapot** az áttelepítés jeleníti meg, mint a **befejezve**.

    ![Befejeződött a tevékenység állapota](media\tutorial-sql-server-to-azure-sql\dms-completed-activity1.png)

2. Az áttelepítés befejezése után válassza ki a **töltse le a jelentés** megszerezni a jelentés az áttelepítési folyamathoz tartozó részletek.

3. Ellenőrizze a cél adatbázis (oka) a cél Azure SQL Database-kiszolgálón.

### <a name="additional-resources"></a>További források

 * [SQL-áttelepítés Azure adatok áttelepítési szolgáltatás (DMS) segítségével](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) gyakorlati labor.