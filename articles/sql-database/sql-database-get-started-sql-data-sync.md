---
title: Adatszinkronizálás beállítása
description: Ez az oktatóanyag bemutatja, hogyan állíthatja be az Azure SQL Data Sync-et
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/14/2019
ms.openlocfilehash: 019ddbac1900856666b958d90b4395f25eb5ee84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268963"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Oktatóanyag: SQL-adatszinkronizálás beállítása az Azure SQL Database és a helyszíni SQL Server között

Ebben az oktatóanyagban megtudhatja, hogyan állíthatja be az Azure SQL Data Sync-et egy olyan szinkronizálási csoport létrehozásával, amely az Azure SQL Database és az SQL Server-példányokat is tartalmazza. A szinkronizálási csoport egyénileg konfigurálva van, és a beállított ütemezés szerint szinkronizál.

Az oktatóanyag feltételezi, hogy legalább néhány korábbi tapasztalattal rendelkezik az SQL Database és az SQL Server szolgáltatással kapcsolatban.

Az SQL Data Sync áttekintését az [Adatok szinkronizálása felhőbeli és helyszíni adatbázisok között](sql-database-sync-data.md)az Azure SQL Data Sync szolgáltatással című témakörben találja.

Az SQL Data Sync konfigurálásával kapcsolatos PowerShell-példákról az [Azure SQL-adatbázisok](scripts/sql-database-sync-data-between-sql-databases.md) vagy [egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis](scripts/sql-database-sync-data-between-azure-onprem.md) közötti szinkronizálás című témakörben talál példákat.

> [!IMPORTANT]
> Az Azure SQL Data Sync jelenleg **nem** támogatja az Azure SQL Database felügyelt példánya.

## <a name="create-sync-group"></a>Szinkronizálási csoport létrehozása

1. Az [SQL-adatbázis](https://portal.azure.com) megkereséséhez keresse meg az Azure Portalt. SQL-adatbázisok keresése és **kijelölése.**

    ![SQL-adatbázisok keresése, Microsoft Azure portál](media/sql-database-get-started-sql-data-sync/search-for-sql-databases.png)

1. Jelölje ki azt az adatbázist, amelyet az adatszinkronizálás központi adatbázisaként kíván használni.

    ![Kiválasztás az SQL adatbázislistából, microsoft Azure portál](media/sql-database-get-started-sql-data-sync/select-sql-database.png)

    > [!NOTE]
    > A központi adatbázis egy szinkronizálási topológia központi végpontja, amelyben egy szinkronizálási csoport több adatbázis-végpontot. Az összes többi tagadatbázis végpontokkal a szinkronizálási csoportban, szinkronizálja a központi adatbázissal.

1. A kijelölt adatbázis **SQL adatbázis** menüjében válassza a Szinkronizálás **más adatbázisokkal parancsot.**

    ![Szinkronizálás más adatbázisokkal, SQL-adatbázissal, Microsoft Azure portállal](media/sql-database-get-started-sql-data-sync/sync-to-other-databases.png)

1. A **Szinkronizálás más adatbázisokkal** lapon válassza az **Új szinkronizálási csoport lehetőséget.** Megnyílik **az Új szinkronizálási csoport** lap, amelyen a Szinkronizálás létrehozása csoport létrehozása **(1. lépés)** ki van emelve.

   ![1. lépés beállításai](media/sql-database-get-started-sql-data-sync/stepone.png)

   Az **Adatszinkronizálási csoport létrehozása** lapon módosítsa a következő beállításokat:

   | Beállítás                        | Leírás |
   | ------------------------------ | ------------------------------------------------- |
   | **Szinkronizálási csoport neve** | Adja meg az új szinkronizálási csoport nevét. Ez a név különbözik magától az adatbázistól. |
   | **Metaadat-adatbázis szinkronizálása** | Válassza ki, hogy létrehoz-e (ajánlott) vagy meglévő adatbázist szeretne használni.<br/><br/>Ha az **Új adatbázis**lehetőséget választja, válassza az Új adatbázis létrehozása **lehetőséget.** Ezután az **SQL Database** lapon nevezze el és konfigurálja az új adatbázist, és válassza az **OK gombot.**<br/><br/>Ha a **Meglévő adatbázis használata**lehetőséget választja, válassza ki az adatbázist a listából. |
   | **Automatikus szinkronizálás** | Válassza **a Be** vagy a **Ki**lehetőséget .<br/><br/>Ha a **Be**lehetőséget választja, adjon meg egy számot, és a Szinkronizálásgyakoriság csoportban válassza a **Másodperc**, **Perc,** **Óra**vagy **Napok** **lehetőséget.** |
   | **Ütközésfeloldás** | Válassza a **Hub win** vagy a Member **win lehetőséget.**<br/><br/>**A hub-győzelem** azt jelenti, hogy ütközés esetén a központi adatbázisban lévő adatok felülírják a tagadatbázis ütköző adatait.<br/><br/>**A tagnyerés** azt jelenti, hogy ütközés esetén a tagadatbázisban lévő adatok felülírják a központi adatbázis ütköző adatait. |

   > [!NOTE]
   > A Microsoft azt javasolja, hogy hozzon létre egy új, üres adatbázist a **Metaadat-adatbázis szinkronizálása számára.** A Data Sync táblákat hoz létre ebben az adatbázisban, és gyakori munkaterhelést futtat. Ez az adatbázis a kijelölt régió összes szinkronizálási **csoportjának metaadat-adatbázisaként** van megosztva, és nem módosíthatja az adatbázist vagy annak nevét anélkül, hogy eltávolítaná a régió összes szinkronizálási csoportját és szinkronizálási ügynökét.

   Válassza **az OK gombot,** és várja meg a szinkronizálási csoport létrehozását és üzembe helyezését.

## <a name="add-sync-members"></a>Szinkronizálási tagok hozzáadása

Az új szinkronizálási csoport létrehozása és üzembe helyezése után a **Szinkronizálási tagok hozzáadása (2. lépés)** kiemelve jelenik meg az **Új szinkronizálási csoport** lapon.

A **Hub Database** szakaszban adja meg annak az SQL-adatbázis-kiszolgálónak a meglévő hitelesítő adatait, amelyen a központi adatbázis található. Ebben a szakaszban ne adjon meg *új* hitelesítő adatokat.

![2. lépés beállításai](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Azure SQL-adatbázis hozzáadása

A **Tagadatbázis** szakaszban szükség esetén adjon hozzá egy Azure SQL-adatbázist a szinkronizálási csoporthoz az **Azure SQL-adatbázis hozzáadása**lehetőség kiválasztásával. Megnyílik **az Azure SQL Database konfigurálása** lap.

  ![2. lépés - adatbázis konfigurálása](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  Az **Azure SQL Database konfigurálása** lapon módosítsa a következő beállításokat:

  | Beállítás                       | Leírás |
  | ----------------------------- | ------------------------------------------------- |
  | **Tag név szinkronizálása** | Adja meg az új szinkronizálási tag nevét. Ez a név különbözik magától az adatbázisnévtől. |
  | **Előfizetés** | Válassza ki a kapcsolódó Azure-előfizetésszámlázási célokra. |
  | **Azure SQL Server** | Jelölje ki a meglévő SQL Database-kiszolgálót. |
  | **Azure SQL Database** | Jelölje ki a meglévő SQL-adatbázist. |
  | **Útvonaltervek szinkronizálása** | Válassza **a Kétirányú szinkronizálás**, A **központhoz**vagy **a központból**lehetőséget. |
  | **Felhasználónév** és **jelszó** | Adja meg annak az SQL Adatbázis-kiszolgálónak a meglévő hitelesítő adatait, amelyen a tagadatbázis található. Ebben a szakaszban ne adjon meg *új* hitelesítő adatokat. |

  Válassza **az OK gombot,** és várja meg az új szinkronizálási tag létrehozását és üzembe helyezését.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Helyszíni SQL Server-adatbázis hozzáadása

A **Tagadatbázis** szakaszban tetszés szerint adjon hozzá egy helyszíni SQL Server kiszolgálót a szinkronizálási csoporthoz a **Helyszíni adatbázis hozzáadása**lehetőség kiválasztásával. Megnyílik **a Helyszíni konfigurálás** lap, ahol a következő műveleteket teheti meg:

1. Válassza **a Szinkronizálási ügynök átjárójának kiválasztása**lehetőséget. Megnyílik **a Szinkronizálási ügynök kiválasztása** lap.

   ![Szinkronizálási ügynök létrehozása](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. A **Szinkronizálási ügynök kiválasztása** lapon válassza ki, hogy meglévő ügynököt szeretne-e használni, vagy létrehoz egy ügynököt.

   Ha a **Létező ügynökök**lehetőséget választja, válassza ki a meglévő ügynököt a listából.

   Ha az Új ügynök létrehozása lehetőséget **választja,** tegye a következőket:

   1. Töltse le az adatszinkronizálási ügynököt a megadott hivatkozásról, és telepítse arra a számítógépre, amelyen az SQL Server található. Az ügynököt közvetlenül az [SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693)ből is letöltheti.

      > [!IMPORTANT]
      > Meg kell nyitnia az 1433-as kimenő TCP-portot a tűzfalon, hogy az ügyfélügynök kommunikáljon a kiszolgálóval.

   1. Adja meg az ügynök nevét.

   1. Válassza **a Kulcs létrehozása és létrehozása lehetőséget,** és másolja az ügynökkulcsot a vágólapra.

   1. A **Szinkronizálási ügynök kiválasztása** lap bezárásához **kattintson** az OK gombra.

1. Az SQL Server számítógépen keresse meg és futtassa az Ügyfélszinkronizálási ügynök alkalmazást.

   ![Az adatszinkronizálási ügyfélügynök-alkalmazás](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. A szinkronizálási ügynök alkalmazásban válassza az **Ügynökkulcs küldése**lehetőséget. Megnyílik **a Metaadat-adatbázis konfigurációjának szinkronizálása** párbeszédpanel.

    1. A **Metaadatok szinkronizálása adatbázis konfigurációja** párbeszédpanelen illessze be az Azure Portalról másolt ügynökkulcsot. Adja meg a meglévő hitelesítő adatokat az Azure SQL Database-kiszolgáló, amelyen a metaadat-adatbázis található. (Ha metaadat-adatbázist hozott létre, ez az adatbázis ugyanazon a kiszolgálón található, mint a központi adatbázis.) Válassza **az OK gombot,** és várja meg, amíg a konfiguráció befejeződik.

        ![Adja meg az ügynökkulcs és a kiszolgáló hitelesítő adatait](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Ha tűzfalhibaüzenet jelenik meg, hozzon létre egy tűzfalszabályt az Azure-ban, amely engedélyezi az SQL Server számítógépről érkező bejövő forgalmat. A szabályt manuálisan is létrehozhatja a portálon vagy az SQL Server Management Studio (SSMS) rendszerben. Az SSMS-ben úgy csatlakozhat az Azure-beli központi adatbázishoz, hogy <hub_database_name>.database.windows.net néven adja meg a nevét.

    1. Válassza a **Regisztráció** lehetőséget az SQL Server-adatbázis regisztrálásához az ügynökkel. Megnyílik **az SQL Server konfigurációja** párbeszédpanel.

        ![SQL Server adatbázis hozzáadása és konfigurálása](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. Az **SQL Server konfigurációja** párbeszédpanelen válassza a csatlakozást SQL Server vagy Windows-hitelesítés használatával. Ha az SQL Server hitelesítést választja, adja meg a meglévő hitelesítő adatokat. Adja meg az SQL Server nevét és a szinkronizálni kívánt adatbázis nevét, és válassza a **Kapcsolat tesztelése** lehetőséget a beállítások teszteléséhez. Ezután válassza a **Mentés lehetőséget,** és a regisztrált adatbázis megjelenik a listában.

        ![Az SQL Server adatbázis regisztrálva van](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. Zárja be az Ügyfélszinkronizálási ügynök alkalmazást.

1. A portálon a **Helyszíni konfigurálás** lapon válassza **az Adatbázis kiválasztása lehetőséget.**

1. Az **Adatbázis kiválasztása** lap **Tagnév szinkronizálása** mezőjében adja meg az új szinkronizálási tag nevét. Ez a név különbözik magától az adatbázistól. Jelölje ki az adatbázist a listából. Az **Útvonaltervek szinkronizálása** mezőben válassza a **Kétirányú szinkronizálás**, **A központ vagy**a **Központ**számára lehetőséget.

    ![A helyszíni adatbázis kiválasztása](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. Az **Adatbázis kiválasztása** lap bezárásához kattintson az **OK gombra.** Ezután az **OK** gombra a **Helyszíni konfigurálás a** lap bezárásához, és várja meg az új szinkronizálási tag létrehozását és üzembe helyezését. Végül kattintson az **OK gombra** a **Szinkronizálási tagok kijelölése** lap bezárásához.

> [!NOTE]
> Az SQL Data Sync és a helyi ügynök összekapcsolásához adja hozzá a felhasználónevét a *szerepkörhöz DataSync_Executor.* A Data Sync létrehozza ezt a szerepkört az SQL Server-példányon.

## <a name="configure-sync-group"></a>Szinkronizálási csoport konfigurálása

Az új szinkronizálási csoporttagok létrehozása és üzembe helyezése után a **Szinkronizálási csoport konfigurálása (3. lépés)** kiemelve jelenik meg az **Új szinkronizálási csoport** lapon.

![3. lépés beállításai](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. A **Táblák** lapon jelöljön ki egy adatbázist a szinkronizálási csoporttagok listájából, és válassza **a Séma frissítése**lehetőséget .

1. A listából jelölje ki a szinkronizálni kívánt táblákat. Alapértelmezés szerint az összes oszlop ki van jelölve, ezért tiltsa le a nem szinkronizálni kívánt oszlopok jelölőnégyzetét. Ügyeljen arra, hogy az elsődleges kulcs oszlopa ki legyen jelölve.

1. Kattintson a **Mentés** gombra.

1. Alapértelmezés szerint az adatbázisok szinkronizálása az ütemezett vagy manuális futtatásig nem történik meg. Manuális szinkronizálás futtatásához keresse meg az SQL-adatbázist az Azure Portalon, válassza a **Szinkronizálás más adatbázisokkal**lehetőséget, és válassza ki a szinkronizálási csoportot. Megnyílik **az Adatszinkronizálás** lap. Válassza a **Szinkronizálás** elemet.

    ![Kézi szinkronizálás](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>GYIK

**Milyen gyakran szinkronizálhatja az adatszinkronizálás az adataimat?**

A szinkronizálások közötti minimális időtartam öt perc.

**Az SQL Data Sync teljes mértékben létrehoz táblákat?**

Ha a szinkronizálási sématáblák hiányoznak a céladatbázisból, az SQL-adatszinkronizálás létrehozza azokat a kiválasztott oszlopokkal. Ez azonban nem eredményez teljes hűségű sémát a következő okok miatt:

- Csak az Ön által kiválasztott oszlopok jönnek létre a céltáblában. A nem kiválasztott oszlopokat a program figyelmen kívül hagyja.
- Csak a kiválasztott oszlopindexek jönnek létre a céltáblában. A nem kiválasztott oszlopok indexeit a program figyelmen kívül hagyja.
- Az XML-típusoszlopok indexei nem jönnek létre.
- A CHECK-megkötések nem jönnek létre.
- A forrástáblák eseményindítói nem jönnek létre.
- A nézetek és a tárolt eljárások nem jönnek létre.

E korlátozások miatt javasoljuk a következőket:

- Éles környezetben saját maga hozza létre a teljes hűségű sémát.
- A szolgáltatással való kísérletezés során használja az automatikus kiépítési funkciót.

**Miért látom a nem általam létrehozott táblákat?**

Az Adatszinkronizálás további táblákat hoz létre az adatbázisban a változások nyomon követéséhez. Ne törölje ezeket, vagy az Adatszinkronizálás nem működik.

**Az adataim konvergensek a szinkronizálás után?**

Nem feltétlenül. Vegyen egy szinkronizálási csoportot egy hubbal és három küllővel (A, B és C), ahol a szinkronizálások hub ról A-ra, hubról B-re és Hub ról C-re. Ha az A adatbázis a Hub to A szinkronizálás *után* módosul, akkor a változás nem kerül a B vagy a C adatbázisba a következő szinkronizálási feladatig.

**Hogyan kaphatok sémamódosításokat egy szinkronizálási csoportba?**

Az összes sémamódosítás manuális végrehajtása és propagálása.

1. Replikálja a séma módosításait manuálisan a hubra és az összes szinkronizálási tagra.
1. Frissítse a szinkronizálási sémát.

Új táblák és oszlopok hozzáadásához:

Az új táblák és oszlopok nincsenek hatással az aktuális szinkronizálásra, és az Adatszinkronizálás figyelmen kívül hagyja őket, amíg hozzá nem kerülnek a szinkronizálási sémához. Új adatbázis-objektumok hozzáadásakor kövesse a sorrendet:

1. Új táblák vagy oszlopok hozzáadása a központhoz és az összes szinkronizálási taghoz.
1. Új táblák vagy oszlopok hozzáadása a szinkronizálási sémához.
1. Kezdje el beszúrni az értékeket az új táblákba és oszlopokba.

Az oszlop adattípusának módosításához:

Ha módosítja egy meglévő oszlop adattípusát, az Adatszinkronizálás addig működik, amíg az új értékek megfelelnek a szinkronizálási sémában meghatározott eredeti adattípusnak. Ha például a forrásadatbázisban a típust **int-ről** **bigint-re módosítja,** az Adatszinkronizálás addig működik, amíg be nem szúr egy túl nagy értéket az **int** adattípushoz. A módosítás befejezéséhez replikálja a sémamódosítást manuálisan a hubra és az összes szinkronizálási tagra, majd frissítse a szinkronizálási sémát.

**Hogyan exportálhatok és importálhatok adatbázist az Adatszinkron segítségével?**

Miután *.bacpac* fájlként exportált egy adatbázist, és importálta a fájlt az adatbázis létrehozásához, tegye a következőket az adatszinkronizálás használatához az új adatbázisban:

1. Törölje az adatszinkronizálási objektumokat és az új adatbázis további tábláit ezzel a [parancsfájllal.](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql) A parancsfájl törli az összes szükséges Adatszinkronizálási objektumot az adatbázisból.
1. Hozza létre újra a szinkronizálási csoportot az új adatbázissal. Ha már nincs szüksége a régi szinkronizálási csoportra, törölje azt.

**Hol találhatok információt az ügyfélügynökről?**

Az ügyfélügynökkel kapcsolatos gyakori kérdésekről az Ügyintéző – gyakori kérdések című témakörben [talál.](sql-database-data-sync-agent.md#agent-faq)

## <a name="next-steps"></a>További lépések

Gratulálunk! Olyan szinkronizálási csoportot hozott létre, amely sql database-példányt és SQL Server-adatbázist is tartalmaz.

További információ az SQL Data Syncről:

- [Adatszinkronizáló ügynök az Azure SQL Data Sync-hez](sql-database-data-sync-agent.md)
- [Gyakorlati tanácsok](sql-database-best-practices-data-sync.md) és [az Azure SQL Data Sync szolgáltatással kapcsolatos problémák elhárítása](sql-database-troubleshoot-data-sync.md)
- [SQL-adatszinkronizálás figyelése az Azure Figyelő naplóival](sql-database-sync-monitor-oms.md)
- [A szinkronizálási séma frissítése transact-SQL vagy](sql-database-update-sync-schema.md) [PowerShell használatával](scripts/sql-database-sync-update-schema.md)

További információ az SQL Database-ről:

- [Az SQL Database áttekintése](sql-database-technical-overview.md)
- [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
