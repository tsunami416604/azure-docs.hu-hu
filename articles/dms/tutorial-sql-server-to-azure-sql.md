---
title: "Használata Azure adatbázis áttelepítési szolgáltatás áttelepítése SQL-kiszolgáló áttelepítése az Azure SQL Database |} Microsoft Docs"
description: "Tudnivalók Azure SQL Azure Database áttelepítési szolgáltatással a helyszíni SQL Server telepíthetők át."
services: dms
author: HJToland3
ms.author: jtoland
manager: jhubbard
ms.reviewer: 
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 70127b09e64ea4f19de437297498bdf78d415b99
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="migrate-sql-server-to-azure-sql-database"></a>Az Azure SQL-adatbázis SQL-kiszolgáló áttelepítése
Az Azure-adatbázis áttelepítési szolgáltatás segítségével a helyszíni SQL Server-példány az Azure SQL Database-adatbázisok át. Ebben az oktatóanyagban az áttelepítést a **Adventureworks2012** adatbázis visszaállítása egy helyszíni példányát az SQL Server 2016 (vagy újabb) egy Azure SQL Database az Azure-adatbázis áttelepítése szolgáltatás használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az adatok áttelepítési Segéd segítségével mérje fel a helyi adatbázis.
> * A minta segítségével az adatok áttelepítési Segéd áttelepítéséhez.
> * Az Azure-adatbázis áttelepítési szolgáltatás egy példányának létrehozásakor.
> * Hozzon létre egy Azure adatbázis áttelepítési szolgáltatás áttelepítési projektet.
> * Az áttelepítéshez.
> * Áttelepítésének figyelése.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- [SQL Server 2016 vagy újabb](https://www.microsoft.com/sql-server/sql-server-downloads) (minden kiadás)
- SQL Server Express telepítése alapértelmezés szerint a TCP/IP-protokoll le van tiltva. Engedélyezze az következő a [jelen cikkben lévő utasítások](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Konfigurálhatja a [Windows tűzfalat a hozzáféréshez](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Egy Azure SQL Database-példányt. Egy Azure SQL Database-példányt hozhat létre a következő cikkben található részletes [Azure SQL-adatbázis létrehozása az Azure portálon](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Adatok áttelepítése Segéd](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 vagy újabb.
- Az Azure-adatbázis áttelepítési szolgáltatás szükséges egy VNETET az Azure Resource Manager telepítési modell, amely webhelyek kapcsolatot biztosít annak a helyszíni adatforrás-kiszolgálók használatával vagy használatával létrehozott [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Az adatforrás SQL Server-példányhoz való kapcsolódáshoz használt hitelesítő adatokat kell [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) engedélyek.
- Azure SQL-adatbázis a célpéldány való kapcsolódáshoz használt hitelesítő adatok a cél Azure SQL Database adatbázisok FELADATVEZÉRLŐ ADATBÁZISHOZ engedéllyel kell rendelkeznie.
- A Windows tűzfal kell megnyitni ahhoz, hogy az Azure adatbázis áttelepítése az SQL Server forrás eléréséhez.

## <a name="assess-your-on-premises-database"></a>A helyi adatbázis felmérése
Mielőtt telepítene át adatokat a helyszíni SQL Server-példány az Azure SQL Database, ki kell értékelnie az SQL Server-adatbázis az olyan problémák elhárítását, amelyek megakadályozhatják az áttelepítés. Töltse le és telepítse az adatok áttelepítési Segéd v3.3, után kövesse a lépéseket, a cikkben ismertetett [hajt végre egy SQL-kiszolgáló áttelepítési assessment](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) végrehajtásához a helyi adatbázis-értékelés. A szükséges lépések összefoglalása a következőképpen:
1.  Az adatok áttelepítési segédjében, válassza ki az új (+) ikonra, majd a **Assessment** projekttípus.
2.  Adja meg a projekt nevét a **server adatforrástípust** szövegmezőben, jelölje be **SQL Server**, majd a a **server Céltípust** szövegmezőben, jelölje be **Azure SQL Adatbázis**.
3.  Válassza ki **létrehozása** a projekt létrehozásához.
    A forrás SQL Server-adatbázis áttelepítése az Azure SQL Database értékelésekor beállíthatja a következő értékelési jelentés típusok közül:
    - Ellenőrizze az adatbázis kompatibilitási
    - Ellenőrizze a szolgáltatás paritás

    Mindkét jelentéstípusra alapértelmezés szerint ki van jelölve.
4.  Az adatok áttelepítési segédjében a a **beállítások** képernyőn válassza ki **következő**.
5.  Az a **válassza ki az adatforrások** képernyő a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a kapcsolat adatait az SQL-kiszolgálóhoz, és válassza **Connect**.
6.  Válassza ki **AdventureWorks2012**, jelölje be **Hozzáadás**, majd válassza ki **Start Assessment**.

    Ha az értékelés befejeződött, az eredményekben az alábbi ábrán látható módon:

    ![Adatok áttelepítése felmérése](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    Az Azure SQL Database a értékelések áttelepítési problémák elhárítását és a szolgáltatás paritásos problémák azonosításához.

7.  Tekintse át az értékelési eredmények áttelepítési problémák elhárítását, és a szolgáltatás paritásos problémák az adott beállítások bejelölésével.
    - Az SQL Server szolgáltatás paritásos kategória ajánlások, Azure-ban, és enyhítő lépéseket érhető el alternatív módszerek segítségével megtervezheti a részéről az erőfeszítés történő áttelepítés projektjeikbe széles választékát nyújtja.
    - A kompatibilitási problémák kategória részben biztosítja vagy nem támogatott funkciókat, amelyek blokkolhatják a Azure SQL-adatbázis áttelepítése a helyszíni SQL Server adatbázis-kompatibilitási problémák tükrözik. Javaslatok segítségével hárítsa el ezeket a problémákat is biztosítja.


## <a name="migrate-the-sample-schema"></a>A minta áttelepítéséhez
Miután részeként irányul, és meggyőződött arról, hogy a kijelölt adatbázis áttelepítése az Azure SQL Database-érdemes deduplikációra, segítségével adatok áttelepítési Segéd áttelepítéséhez az Azure SQL Database.

> [!NOTE]
> Mielőtt áttelepítési projekt létrehozása az adatok áttelepítési Segéd, győződjön meg, hogy már ellátta Azure SQL-adatbázis a szükséges előfeltételek. Ez az oktatóanyag céljából, az Azure SQL-adatbázis neve adottnak **AdventureWorks2012**, de nevet adhat az másképp Ha.

Áttelepíteni a **AdventureWorks2012** séma az Azure SQL Database, a következő lépésekkel:

1.  Indítsa el az adatok áttelepítési Segéd.
2.  Válassza ki az új (+) ikonra, majd a **projekttípus**, jelölje be **áttelepítési**.
3.  Adja meg a projekt nevét a **server adatforrástípust** szövegmezőben, jelölje be **SQL Server**, majd a a **server Céltípust** szövegmezőben, jelölje be **Azure SQL Adatbázis**.
4.  A **áttelepítési hatókör**, jelölje be **séma csak**.

    Az előző lépések elvégzése után az adatok áttelepítési Segéd felület megjelenjen-e az alábbi ábrán látható módon:
    
    ![Adatok áttelepítése Segéd projekt létrehozása](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

5.  Válassza ki **létrehozása** a projekt létrehozásához.
6.  Az adatok áttelepítési Segéd, adja meg az adatforrás kapcsolódási adatait az SQL Server, válassza ki a **Connect**, majd válassza ki a **AdventureWorks2012** adatbázis.

    ![Adatok áttelepítése Segéd adatforrás kapcsolódási adatait.](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)
7.  Válassza ki **tovább**a **kapcsolódás a célkiszolgáló**, adja meg a célként megadott kapcsolódási adatait az Azure SQL-adatbázis, válassza ki **Connect**, majd válassza ki a **AdventureWorks2012** kellett előzetes kiosztása az Azure SQL database adatbázishoz.

    ![Adatok áttelepítése Segéd cél kapcsolódási adatait.](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)
8.  Válassza ki **következő** ahhoz, hogy értékről a **objektumok kijelölése** képernyő, amelyen megadhatja az adatbázisséma objektumaiban a a **AdventureWorks2012** adatbázis, amely kell üzembe helyezni az Azure-bA SQL-adatbázis.

    Alapértelmezés szerint minden objektum ki lesz választva.

    ![SQL-parancsfájlok létrehozása](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)
9.  Válassza ki **létrehozni SQL-parancsfájl** az SQL-parancsfájlok létrehozásához, és tekintse át a hibákat a parancsfájlokat.

    ![Séma parancsfájl](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)
10. Válassza ki **telepítés séma** a séma telepítéséhez az Azure SQL Database és a séma telepítése után ellenőrizze a célkiszolgálón a bármely rendellenességeket.

    ![Séma telepítése](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="create-an-instance"></a>Hozzon létre egy példányt
1.  Jelentkezzen be az Azure-portálon válassza **+ hozzon létre egy erőforrást**, keresse meg az Azure-adatbázis áttelepítése szolgáltatás, és válassza **Azure adatbázis áttelepítési szolgáltatás** a legördülő listából.

    ![Azure Piactér](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)
2.  Az a **Azure adatbázis áttelepítési szolgáltatás (előzetes verzió)** képernyőn válassza ki **létrehozása**.
 
    ![Azure adatbázis áttelepítési szolgáltatáspéldány létrehozása](media\tutorial-sql-server-to-azure-sql\dms-create.png)
  
3.  Az a **adatbázis áttelepítési szolgáltatás** képernyőn, adja meg a szolgáltatás, az előfizetés, a virtuális hálózat és az árképzési szint nevét.

    További információ a költségek és árképzési szinteket tekintse meg a tarifákat tartalmazó oldalt.

     ![Azure adatbázis áttelepítési példány beállításainak megadása](media\tutorial-sql-server-to-azure-sql\dms-settings.png)

4.  Válassza ki **létrehozása** létrehozni a szolgáltatást.

## <a name="create-a-migration-project"></a>Áttelepítési-projekt létrehozása
A szolgáltatás létrehozása után keresse meg azt az Azure portálon, és ezután hozzon létre egy áttelepítési projektet.
1. Válassza ki az Azure-portálon **minden szolgáltatás**, keresse meg az Azure-adatbázis áttelepítése szolgáltatás, és válassza **Azure adatbázis áttelepítési szolgáltatások**.
 
      ![Keresse meg az Azure-adatbázis áttelepítési szolgáltatás összes példánya](media\tutorial-sql-server-to-azure-sql\dms-search.png)
2. Az a **Azure adatbázis áttelepítési szolgáltatások** képernyőn, keresse meg az Azure DMS neve példány létrehozott, majd válassza ki a példány.
 
     ![Keresse meg az Azure-adatbázis áttelepítési szolgáltatás példányát](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Válassza ki **+ új áttelepítési projekt**.
4. A a **új áttelepítési projekt** meg kell adnia egy nevet a projekthez, kattintson a jobb a **server adatforrástípust** szövegmezőben, jelölje be **SQL Server**, majd a a **cél kiszolgáló típusa** szövegmezőben, jelölje be **Azure SQL Database**.

    ![Adatbázis áttelepítési Service-projekt létrehozása](media\tutorial-sql-server-to-azure-sql\dms-create-project.png)

5.  Válassza ki **létrehozása** a projekt létrehozásához.


## <a name="specify-source-details"></a>Adja meg az adatforrás részletei
1. Az a **részletek forrás** képernyőn, a forrás SQL-kiszolgáló kapcsolati adatainak megadása.

    ![Forrás kiválasztása](media\tutorial-sql-server-to-azure-sql\dms-select-source.png)

2. Válassza ki **mentése**, majd válassza ki a **AdventureWorks2012** adatbázis az áttelepítéshez.

    ![Válassza ki a forrás DB](media\tutorial-sql-server-to-azure-sql\dms-select-source-db.png)

## <a name="specify-target-details"></a>Adja meg a cél adatait
1. Válassza ki **mentése**, majd a a **céloz részletek** képernyőn, adja meg a kapcsolódási adatait a célként, ami a előtti ponton aktívvá vált Azure SQL-adatbázis, amelyhez a **AdventureWorks2012**  séma használatával az adatok áttelepítési Segéd telepített.

    ![Válassza ki a cél](media\tutorial-sql-server-to-azure-sql\dms-select-target.png)

2. Válassza ki **mentése** menteni a projektet.
3. Az a **áttelepítési összegzés** képernyőn tekintse át, ellenőrizze az áttelepítési projekttel kapcsolatos részleteket.

    ![DMS összegzése](media\tutorial-sql-server-to-azure-sql\dms-summary.png)

4. Kattintson a **Mentés** gombra.

## <a name="run-the-migration"></a>Az áttelepítés futtatása
1.  Válassza ki a nemrég mentett projektet, jelölje be **+ új tevékenység**, majd válassza ki **adatáttelepítés futtatása**.

    ![Új tevékenység](media\tutorial-sql-server-to-azure-sql\dms-new-activity.png)

2.  Amikor a rendszer kéri, adja meg a hitelesítő adatait a forrás- és célkiszolgálók, és válassza **mentése**.
3.  Az a **céladatbázisokhoz térkép** képernyőn, a forrás és a céladatbázis az áttelepítéshez.

    Ha a céladatbázis adatbázis neve megegyezik a forrásadatbázis, Azure DMS a céladatbázis alapértelmezés szerint választja ki.

    ![Céladatbázisokhoz leképezése](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity.png)

4. Válassza ki **mentése**, a **táblák kiválasztása** képernyőn, bontsa ki a tábla átjáróra a listában, és tekintse át az érintett mezőket.

    ![Select tables (Táblák kiválasztása)](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity.png)

5.  Válassza ki **mentése**, a a **áttelepítési összegzés** képernyő a **tevékenységnév** szöveg adja meg az áttelepítési tevékenység nevét.

    Ezen a képernyőn is bővítheti a **válassza ki az érvényesítési beállítás** képernyő, amely segítségével adja meg, hogy az áttelepített adatbázis ellenőrzése:
    - Séma
    - Adatkonzisztencia
    - Lekérdezés helyességét és a teljesítmény

    ![Válassza ki az érvényesítési beállítás](media\tutorial-sql-server-to-azure-sql\dms-configuration.png)

6.  Válassza ki **mentése**, tekintse át az összefoglalást, annak érdekében, hogy a forrás és cél részletei egyeznek-e a mi korábban megadott.

    ![Áttelepítési összegzése](media\tutorial-sql-server-to-azure-sql\dms-run-migration.png)

7.  Válassza ki **áttelepítés** indítsa el az áttelepítési tevékenység, és válassza ki a **frissítése** aktuális állapota.

    ![A tevékenység állapota](media\tutorial-sql-server-to-azure-sql\dms-activity-status.png)

## <a name="monitor-the-migration"></a>A figyelő az áttelepítés
1. Válassza ki az áttelepítési tevékenység a tevékenység állapota.
2. Ellenőrizze a cél Azure SQL-adatbázis, az áttelepítés befejezése után.

    ![Befejezve](media\tutorial-sql-server-to-azure-sql\dms-completed-activity.png)
