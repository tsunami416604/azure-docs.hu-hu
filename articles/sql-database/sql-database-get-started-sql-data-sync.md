---
title: Az Azure SQL Data Sync beállítása |} A Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan lehet az Azure SQL Data Sync beállítása
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 01/25/2018
ms.openlocfilehash: 03ea4a7db27e32b370be89adf308ded9a22f9e37
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478423"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Oktatóanyag: A helyszíni Azure SQL Database és SQL Server között az SQL Data Sync beállítása

Ebben az oktatóanyagban elsajátíthatja, hogyan hoz létre a szinkronizálási csoport, amely tartalmazza az Azure SQL Database és az SQL Server-példányok Azure SQL Data Sync beállítása. A szinkronizálási csoport egyéni konfigurálva, és a beállított ütemezés szerint szinkronizálja.

Az oktatóanyag feltételezi, hogy az SQL Database és SQL Server legalább némi tapasztalattal rendelkezik.

Az SQL Data Sync áttekintéséhez lásd: [adatok szinkronizálásának felhőbeli és helyszíni adatbázisok az Azure SQL Data Sync szolgáltatással](sql-database-sync-data.md).

A PowerShell-példák az SQL Data Sync konfigurálásáról, tekintse meg a [szinkronizálása az Azure SQL Database-adatbázis közötti](scripts/sql-database-sync-data-between-sql-databases.md) vagy [az Azure SQL Database és a helyszíni SQL Server-adatbázis](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> Az Azure SQL Data Sync does **nem** támogatja az Azure SQL Database felügyelt példánya jelenleg.

## <a name="create-sync-group"></a>Szinkronizálási csoport létrehozása

1. A böngészőben navigáljon az Azure Portalon. Keresse meg az SQL database, az irányítópultról vagy, válassza ki a **SQL-adatbázisok** ikonra az eszköztáron, majd a a **SQL-adatbázisok** lapra, jelölje be az adatok szinkronizálása az eseményközpont-adatbázisként használni kívánt adatbázist.

    > [!NOTE]
    > A hub adatbázis egy szinkronizálási-topológia központi végpont, amelyben a szinkronizálási csoport több adatbázis-végponttal rendelkezik. A központi adatbázis összes többi tag adatbázis a szinkronizálási csoport végpontokkal rendelkező szinkronizálni.

1. Az a **SQL-adatbázis** a kiválasztott adatbázishoz, válassza a lap **szinkronizálás más adatbázisokkal**.

    ![Szinkronizálás más adatbázisok lehetőség](media/sql-database-get-started-sql-data-sync/datasync-overview.png)

1. Az a **szinkronizálás más adatbázisokkal** lapon jelölje be **új szinkronizálási csoport**. A **új szinkronizálási csoport** lap, melyen a **szinkronizálási csoport létrehozása (1. lépés)** kiemelt.

  ![1. lépés-beállítások](media/sql-database-get-started-sql-data-sync/stepone.png)

  Az a **adatok szinkronizálási csoport létrehozása** lapon, a következő beállításokat módosíthatja:

  | Beállítás                        | Leírás |
  | ------------------------------ | ------------------------------------------------- |
  | **Szinkronizálási csoport neve** | Adjon meg egy nevet az új szinkronizálási csoport. Ez a név nem azonos azzal a maga az adatbázis nevét. |
  | **Metaadat-adatbázis szinkronizálása** | Válassza ki a (javasolt) adatbázis létrehozása vagy egy meglévő adatbázis használatát.<br/><br/>Ha úgy dönt, **új adatbázis**válassza **új adatbázis létrehozása.** Ezután a a **SQL Database** lapon nevezze el és konfigurálja az új adatbázist, és válassza **OK**.<br/><br/>Ha úgy dönt, **létező adatbázis használata**, válassza ki az adatbázist a listából. |
  | **Automatická Synchronizace** | Válassza ki **a** vagy **ki**.<br/><br/>Ha úgy dönt, **a**, adjon meg egy számot, és válassza ki **másodperc**, **perc**, **óra**, vagy **nap** a a **Szinkronizálási gyakoriság** szakaszban. |
  | **Ütközések feloldása** | Válassza ki **a központ nyert** vagy **a tag nyert**.<br/><br/>**A központ nyert** azt jelenti, ha ütközés lép fel, adatokat a központi adatbázis felülírja a tag adatbázis adatai.<br/><br/>**A tag nyert** azt jelenti, ha ütközés lép fel, a tag adatbázis adatainak felülírja a központi adatbázis adatai. |

  > [!NOTE]
  > Hozzon létre egy új, üres adatbázis használata a Microsoft javasolja a **szinkronizálási metaadat-adatbázis**. Adatszinkronizálás hoz létre a táblák az adatbázisban, és a gyakori számítási feladat. Az adatbázis megosztása a **szinkronizálási metaadat-adatbázis** az összes szinkronizálási csoportok egy kiválasztott régióban, és nem módosítható az adatbázis vagy a neve szinkronizálási csoportok és a szinkronizációs ügynökök eltávolítása a régióban nélkül.

  Válassza ki **OK** és várjon, amíg a szinkronizálási csoport létrehozása és üzembe helyezve.

## <a name="add-sync-members"></a>Szinkronizálási tagok hozzáadása

Az új szinkronizálási csoport létrehozása és üzembe helyezését követően **(2. lépés) szinkronizálási tagok hozzáadása** kiemelve a **új szinkronizálási csoport** lapot.

Az a **központi adatbázis** területén adja meg a meglévő hitelesítő adatokat az SQL Database-kiszolgáló, amelyen a központi adatbázis is található. Ne adjon meg *új* ebben a szakaszban a hitelesítő adatokat.

![2. lépés-beállítások](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Az Azure SQL Database hozzáadása

Az a **Tagadatbázis** című igény szerint adja hozzá az Azure SQL Database a szinkronizálási csoport kiválasztásával **hozzáadása az Azure SQL Database**. A **konfigurálása az Azure SQL Database** lap megnyitásakor.

  ![2. lépés: az adatbázis konfigurálása](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  Az a **konfigurálása az Azure SQL Database** lapon, a következő beállításokat módosíthatja:

  | Beállítás                       | Leírás |
  | ----------------------------- | ------------------------------------------------- |
  | **Szinkronizálási tag neve** | Adjon meg egy nevet az új szinkronizálási tag. Ez a név nem azonos azzal a az adatbázis nevét, magát. |
  | **Előfizetés** | Válassza ki a társított Azure-előfizetés számlázási célból. |
  | **Azure SQL Server** | Válassza ki a meglévő SQL Database-kiszolgálóhoz. |
  | **Azure SQL Database** | Válassza ki a meglévő SQL-adatbázis. |
  | **Szinkronizálási irányok** | Válassza ki **kétirányú szinkronizálás**, **a hub**, vagy **a hubról**. |
  | **Felhasználónév** és **jelszó** | Adja meg a meglévő hitelesítő adatokat az SQL Database-kiszolgálóhoz, amelyen a tag adatbázis is található. Ne adjon meg *új* ebben a szakaszban a hitelesítő adatokat. |

  Válassza ki **OK** és várja meg a létrehozott és telepített új szinkronizálási tag.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Egy helyszíni SQL Server-adatbázis hozzáadása

Az a **Tagadatbázis** szakaszt, ha szükséges, hozzáadhat egy helyszíni SQL Server, a szinkronizálási csoport kiválasztásával **egy helyszíni adatbázis hozzáadása**. A **konfigurálása a helyszíni** lap megnyitásakor, ahol a következő műveleteket végezheti el:

1. Válassza ki **szinkronizációs ügynök átjárójának kiválasztása**. A **szinkronizációs ügynök kiválasztása** lap megnyitásakor.

  ![A szinkronizációs ügynök létrehozása](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. Az a **válassza ki a szinkronizálási ügynök** lapon, döntse el, hogy használjon egy meglévő ügynököt, vagy hozzon létre egy ügynök.

  Ha úgy dönt, **meglévő ügynökök**, válassza ki a meglévő ügynököt a listából.

  Ha úgy dönt, **hozzon létre egy új ügynök**, tegye a következőket:

    1. A megadott hivatkozás adatok szinkronizálási ügynök letöltése, és telepítse a számítógépen, ahol az SQL Server is található. Az ügynök közvetlenül is letöltheti [SQL Azure Data Sync-ügynök](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Nyissa meg a kimenő 1433-as TCP-port a tűzfalon, hogy az ügyfél-ügynök kommunikálni a kiszolgálóval rendelkezik.

    1. Adjon meg egy nevet az ügynököt.

    1. Válassza ki **létrehozása és a kulcs létrehozása** és az ügynök kulcs másolása a vágólapra.

    1. Válassza ki **OK** gombra kattintva zárja be a **szinkronizációs ügynök kiválasztása** lapot.

1. Az SQL Server-számítógépen keresse meg és futtassa az ügyfél-szinkronizálási ügynök alkalmazást.

  ![Az adatok szinkronizálása az ügyfélalkalmazás-ügynök](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. Válassza ki a szinkronizálási ügynök alkalmazásban **Ügynökkulcs elküldése**. A **szinkronizálási metaadat-adatbázis konfigurációja** párbeszédpanel nyílik meg.

    1. Az a **szinkronizálási metaadat-adatbázis konfigurációja** párbeszédablakban illessze be az Azure Portalról másolt ügynök kulcs. A meglévő hitelesítő adatokat is biztosít az Azure SQL Database-kiszolgáló, amelyen a metaadatokat tároló adatbázis is található. (A metaadatokat tároló adatbázis hozta létre, ez az adatbázis-e a központi adatbázis ugyanazon a kiszolgálón.) Válassza ki **OK** és várjon, amíg a konfiguráció befejezéséhez.

        ![Adja meg az ügynök kulcs és a kiszolgáló hitelesítő adatai](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Ha a tűzfal hibaüzenet jelenik meg, hozzon létre egy tűzfalszabályt az Azure-ban, hogy az SQL Server számítógép érkező bejövő forgalmat. A portálon vagy az SQL Server Management Studio (SSMS) manuálisan is létrehozhatja a a szabályt. Az ssms-ben, kapcsolódni a központi adatbázis az Azure-ban, < hub_database_name > név beírásával. database.windows.net.

    1. Válassza ki **regisztrálása** -ügynökkel rendelkező SQL Server-adatbázis regisztrálásához. A **SQL Server-konfigurációs** párbeszédpanel nyílik meg.

        ![Adja hozzá, és a egy SQL Server-adatbázis konfigurálása](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. Az a **SQL Server-konfigurációs** párbeszédpanelen válassza a csatlakozás az SQL Server-hitelesítés vagy a Windows-hitelesítés használatával. Ha az SQL Server-hitelesítést választja, adja meg a meglévő hitelesítő adatokat. Adja meg az SQL Server nevét és az adatbázis, amelyet szeretne szinkronizálni, és válassza ki a nevét **kapcsolat tesztelése** a beállítások teszteléséhez. Válassza ki **mentése** és a regisztrált adatbázis megjelenik a listában.

        ![SQL Server-adatbázis most már regisztrálva van](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. Zárja be az ügyfél szinkronizálása a Agent alkalmazás.

1. A portálon a a **konfigurálása a helyszíni** lapon jelölje be **válassza ki az adatbázist**.

1. Az a **adatbázis választása** lap a **szinkronizálási tag neve** mezőben adjon meg egy nevet az új szinkronizálási tag. Ez a név nem azonos azzal a maga az adatbázis nevét. Válassza ki az adatbázist a listából. Az a **szinkronizálási irányok** mezőben válassza **kétirányú szinkronizálás**, **a Hub**, vagy **a Hub**.

    ![Jelölje ki a helyi adatbázist](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. Válassza ki **OK** gombra kattintva zárja be a **adatbázis választása** lapot. Válassza ki **OK** gombra kattintva zárja be a **konfigurálása a helyszíni** lapon, és várja meg a létrehozott és telepített új szinkronizálási tag. Végül válassza **OK** gombra kattintva zárja be a **szinkronizálási tagok kiválasztása** lapot.

> [!NOTE]
> Szeretne csatlakozni az SQL Data Sync és a helyi ügynök, vegye fel a felhasználó nevét a szerepkör *DataSync_Executor*. Adatok szinkronizálása a szerepkör az SQL Server-példányt hoz létre.

## <a name="configure-sync-group"></a>Szinkronizálási csoport konfigurálása

Miután létrehozott és telepített, az új szinkronizálási csoport tagjait **konfigurálása szinkronizálási csoport (3. lépés)** kiemelten jelenik meg a **új szinkronizálási csoport** lapot.

![3. lépés-beállítások](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. Az a **táblák** lapon válasszon ki egy adatbázist a szinkronizálási csoport tagjainak listájáról, és válassza **frissítési séma**.

1. A listában jelölje ki a szinkronizálni kívánt táblák. Alapértelmezés szerint az összes oszlop ki van jelölve, így tiltsa le az oszlopok nem szeretné szinkronizálni a jelölőnégyzetet. Mindenképpen hagyja kiválasztva elsődleges kulcsának oszlopa.

1. Kattintson a **Mentés** gombra.

1. Alapértelmezés szerint adatbázisok nem szinkronizált mindaddig, amíg az ütemezett vagy kézi futtatása. Futtasson manuális szinkronizálást, lépjen az Azure Portalon, válassza az SQL Database **szinkronizálás más adatbázisokkal**, és válassza ki a szinkronizálási csoportot. A **Data Sync** lap megnyitásakor. Válassza a **Szinkronizálás** elemet.

    ![Manuális szinkronizálás](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>GYIK

**Milyen gyakran is Data Sync szinkronizálni az adatokat?**

A szinkronizálások között minimális időtartama öt perc alatt.

**Nem az SQL Data Sync teljes táblák létrehozása?**

Ha a szinkronizálási séma táblák hiányoznak a céladatbázis, az SQL Data Sync hoz létre azokat a kijelölt oszlopok. Azonban ez nem eredményezi a teljes hűségű séma a következő okok miatt:

- Csak a kiválasztott oszlopok jönnek létre a céltáblázatban. Nincs kijelölt oszlop. a rendszer figyelmen kívül hagyja.
- Csak a kijelölt oszlop indexek jönnek létre a céltáblázatban. A nem kijelölt oszlopok ezek az indexek figyelmen kívül hagyja.
- XML-típusú oszlopok indexei hozhatók létre.
- ELLENŐRZŐ korlátozások hozhatók létre.
- A forrás táblák indítóinak hozhatók létre.
- Nézetek és tárolt eljárások hozhatók létre.

Ezek a korlátozások miatt javasoljuk, hogy az alábbiakat:

- Az éles környezetekhez hozzon létre a teljes hűségű sémát saját magának.
- Kísérletezés a szolgáltatással, ha az Automatikus kiépítés funkció használatához.

**Miért látok I nem hozott létre táblák?**

Adatszinkronizálás további táblákat hoz létre az adatbázisban a change Tracking szolgáltatáshoz. Ne törölje ezeket, vagy Data Sync szolgáltatással nem működik.

**Az adatok egy irányba a szinkronizálás után?**

Nem feltétlenül. A szinkronizálások amelyeknél hubot, hogy A Hub b és c Hub egy küllős topológia három (A, B és C) a szinkronizálási csoport igénybe Ha egy adatbázishoz módosításakor *után* a hubhoz való szinkronizálást, módosítsa a következő szinkronizálási feladat B vagy C adatbázisba nem írt.

**Hogyan szerezhetem be a sémamódosítások egy szinkronizálási csoporthoz?**

Győződjön meg arról, és minden sémaváltozások manuálisan propagálása.

1. A sémaváltozások manuálisan replikálja a hub és az összes szinkronizálási tagok.
1. Szinkronizálási sémájának frissítéséhez.

Új táblák és oszlopok hozzáadásához:

Új táblák és oszlopok ne befolyásolják a jelenlegi szinkronizálási és Data Sync figyelmen kívül hagyja őket mindaddig, amíg a rendszer hozzáadja a szinkronizálási sémához. Új adatbázis-objektumok való hozzáadásakor, hajtsa végre a feladatütemezés:

1. A hub és az összes szinkronizálási tagok adjon hozzá új táblákat vagy oszlopokat.
1. Új táblák vagy oszlopok hozzáadása a szinkronizálási sémához.
1. Kezdje el az új táblák és oszlopok értékek beszúrása.

Az oszlop adattípusának módosítása:

Amikor módosítja egy létező oszlop adattípusát, a Data Sync továbbra is működik, mindaddig, amíg az új értékeket az eredeti adattípus a szinkronizálási sémában meghatározott elemnévvel illeszkednek. Például, ha a forrás-adatbázisban a típusának módosítása **int** való **bigint**, Data Sync továbbra is működni, amíg nem túl nagy értéket szúr be a **int** adattípus. Hajtsa végre a módosítást, manuálisan replikálja a sémaváltozás, a hub és az összes szinkronizálási tagok, majd a szinkronizálási sémájának frissítéséhez.

**Hogyan tudok exportálhat és importálhat egy adatbázist a Data Sync szolgáltatással?**

Egy adatbázis-exportálását követően egy *.bacpac* fájlt, és importálja a fájlt, hozzon létre egy adatbázist, tegye a következőket az új adatbázisban található Data Sync használata:

1. Távolítsa el a Data Sync objektumok és az új adatbázis használatával további táblákon [Ez a szkript](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). A parancsfájl az összes szükséges Data Sync objektumot törli az adatbázisból.
1. Hozza létre újra a szinkronizálási csoport az új adatbázissal. Ha már nincs szüksége a régi szinkronizálási csoport, törölje azt.

**Hol található az ügyfélügynök információkat?**

Az ügyfélügynök – gyakori kérdések, lásd: [ügynök – gyakori kérdések](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>További lépések

Gratulálunk! Ezzel létrehozott egy szinkronizálási csoport, amely tartalmazza az SQL Database-példány és a egy SQL Server-adatbázist.

További információ az SQL Data Syncről:

- [Az Azure SQL Data Sync adatok szinkronizációs ügynök](sql-database-data-sync-agent.md)
- [Ajánlott eljárások](sql-database-best-practices-data-sync.md) és [az Azure SQL Data Sync szolgáltatással kapcsolatos problémák elhárítása](sql-database-troubleshoot-data-sync.md)
- [Az SQL Data Sync monitorozása a Log Analytics szolgáltatással](sql-database-sync-monitor-oms.md)
- [Szinkronizálási sémájának frissítéséhez a Transact-SQL](sql-database-update-sync-schema.md) vagy [PowerShell](scripts/sql-database-sync-update-schema.md)

További információ az SQL Database-ről:

- [Az SQL Database áttekintése](sql-database-technical-overview.md)
- [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
