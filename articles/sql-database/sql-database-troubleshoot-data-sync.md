---
title: Az SQL Data Synch hibaelhárítása
description: Ismerje meg, hogyan háríthatja el az Azure SQL Data Sync gyakori problémáit.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 8e223d0c6243bfddc1e5a56867c4c69de5e2a62e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822455"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Az SQL Data Synckel kapcsolatos hibák elhárítása

Ez a cikk ismerteti, hogyan hárítsa el az Azure SQL Data Sync ismert problémák elhárítása. Ha van megoldás egy probléma, ez itt található.

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](sql-database-sync-data.md) ismertető cikket.

> [!IMPORTANT]
> Az Azure SQL Data Sync jelenleg **nem** támogatja az Azure SQL Database felügyelt példánya.

## <a name="sync-issues"></a>Szinkronizálási problémák

- [A szinkronizálás sikertelen a portál felhasználói felületén az ügyfélügynökhöz társított helyszíni adatbázisok esetében](#sync-fails)

- [A szinkronizálási csoport beragadt a feldolgozási állapotba](#sync-stuck)

- [Hibás adatok jelennek meg a táblázatokban](#sync-baddata)

- [A sikeres szinkronizálás után inkonzisztens elsődleges kulcsadatok jelennek meg](#sync-pkdata)

- [Jelentős teljesítménycsökkenést látok.](#sync-perf)

- [A következő üzenet jelenik meg: "Nem \<lehet beszúrni a NULL értéket az oszloposzlopba>. Az oszlop nem engedélyezi a null értékeket." Mit jelent ez, és hogyan tudom megjavítani?](#sync-nulls)

- [Hogyan kezeli az Adatszinkronizálás a körkörös hivatkozásokat? Ez azt, hogy ha ugyanazokat az adatokat több szinkronizálási csoportban szinkronizálja, és ennek eredményeképpen folyamatosan változik?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a>A szinkronizálás sikertelen a portál felhasználói felületén az ügyfélügynökhöz társított helyszíni adatbázisok esetében

A szinkronizálás sikertelen az SQL Data Sync portal felhasználói felületén az ügyfélügynökhöz társított helyszíni adatbázisok esetében. Az ügynököt futtató helyi számítógépen a System.IO.IOException hibák láthatók az eseménynaplóban. A hibák azt mondják, hogy a lemezen nincs elegendő hely.

- **Oka**. A meghajtón nincs elegendő hely.

- **felbontás .** Hozzon létre több helyet azon a meghajtón, amelyen a %TEMP% könyvtár található.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a>A szinkronizálási csoport beragadt a feldolgozási állapotba

Az SQL Data Sync egy szinkronizálási csoportja már régóta feldolgozási állapotban van. Nem válaszol a **stop** parancsra, és a naplók nem mutatnak új bejegyzéseket.

Az alábbi feltételek bármelyike azt eredményezheti, hogy egy szinkronizálási csoport a feldolgozási állapotban marad:

- **Oka**. Az ügyfélügynök offline állapotú.

- **felbontás .** Győződjön meg arról, hogy az ügyfélügynök online állapotban van, és próbálkozzon újra.

- **Oka**. Az ügyfélügynök el lett távolítva vagy hiányzik.

- **felbontás .** Ha az ügyfélügynök el lett távolítva vagy más okból hiányzik:

    1. Távolítsa el az ügynök XML-fájlját az SQL Data Sync telepítési mappájából, ha létezik ez a fájl.
    1. Telepítse az ügynököt a helyszíni számítógépen (ez ugyanaz vagy egy másik számítógép is lehet). Ezután küldje el a portálon létrehozott ügynökkulcsot az offline állapotúként megjelenő ügynöknek.

- **Oka**. Az SQL Data Sync szolgáltatás leállt.

- **felbontás .** Indítsa újra az SQL Data Sync szolgáltatást.

    1. A **Start** menüben keressen **meg szolgáltatásokat.**
    1. A keresési eredmények között válassza a **Szolgáltatások**lehetőséget.
    1. Keresse meg az **SQL Data Sync** szolgáltatást.
    1. Ha a szolgáltatás állapota **Leállítva,** kattintson a jobb gombbal a szolgáltatás nevére, majd válassza a **Start parancsot.**

> [!NOTE]
> Ha az előző adatok nem távolítják el a szinkronizálási csoportot a feldolgozási állapotból, a Microsoft támogatási szolgálata visszaállíthatja a szinkronizálási csoport állapotát. A szinkronizálási csoport állapotának visszaállításához az [Azure SQL Database fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)hozzon létre egy bejegyzést. A bejegyzésben adja meg az előfizetés-azonosítóját és az alaphelyzetbe állítandó csoport szinkronizálási csoportazonosítóját. A Microsoft támogatási szakembere válaszol a bejegyzésre, és tudatja Önvel, ha az állapot alaphelyzetbe állt.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a>Hibás adatok jelennek meg a táblázatokban

Ha az azonos nevű, de különböző adatbázissémákból származó táblák at tartalmaz egy szinkronizálás, a szinkronizálás után hibás adatok jelennek meg a táblákban.

- **Oka**. Az SQL Data Sync létesítési folyamat ugyanazokat a követési táblákat használja az azonos nevű, de különböző sémákban lévő táblákhoz. Emiatt a két tábla módosításai ugyanabban a követési táblában jelennek meg. Ez hibás adatváltozásokat okoz a szinkronizálás során.

- **felbontás .** Győződjön meg arról, hogy a szinkronizálásban részt vevő táblák neve eltérő, még akkor is, ha a táblák az adatbázis különböző sémaihoz tartoznak.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a>A sikeres szinkronizálás után inkonzisztens elsődleges kulcsadatok jelennek meg

A szinkronizálás sikeresnek minősül, és a napló nem jelenít meg sikertelen vagy kihagyott sorokat, de megfigyeli, hogy az elsődleges kulcs adatai inkonzisztensek a szinkronizálási csoport adatbázisai között.

- **Oka**. Ez az eredmény a design. Bármely elsődleges kulcsoszlop módosításain inkonzisztens adatok következnek azokban a sorokban, ahol az elsődleges kulcs módosult.

- **felbontás .** A probléma elkerülése érdekében győződjön meg arról, hogy az elsődleges kulcs oszlopában nincs adat. A probléma megoldásához a probléma előfordulása után törölje azt a sort, amely inkonzisztens adatokat tartalmazó adatokat a szinkronizálási csoport összes végpontjáról. Ezután helyezze vissza a sort.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a>Jelentős teljesítménycsökkenést látok.

A teljesítmény jelentősen romlik, valószínűleg addig a pontig, ahol még az Adatszinkronizálás felhasználói felületét sem tudja megnyitni.

- **Oka**. A legvalószínűbb ok egy szinkronizálási hurok. A szinkronizálási ciklus akkor fordul elő, amikor az A szinkronizálási csoport on-val elindítja a szinkronizálást a B csoport szerint, ami ezután az A szinkronizálási csoport szerint szinkronizálást indít el. A tényleges helyzet összetettebb lehet, és kettőnél több szinkronizálási csoportot is bevonhat a hurokba. A probléma az, hogy van egy kör körös kiváltó szinkronizálás, amely által okozott szinkronizálási csoportok egymást átfedő.

- **felbontás .** A legjobb megoldás a megelőzés. Győződjön meg arról, hogy nincs körkörös hivatkozás a szinkronizálási csoportokban. Az egyik szinkronizálási csoport által szinkronizált sorokat nem szinkronizálhatja egy másik szinkronizálási csoport.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a>A következő üzenet jelenik meg: "Nem \<lehet beszúrni a NULL értéket az oszloposzlopba>. Az oszlop nem engedélyezi a null értékeket." Mit jelent ez, és hogyan tudom megjavítani? 
Ez a hibaüzenet azt jelzi, hogy a következő két probléma egyike lépett fel:
-  A táblának nincs elsődleges kulcsa. A probléma megoldásához adjon hozzá egy elsődleges kulcsot az összes szinkronizált táblához.
-  A CREATE INDEX utasításban van egy WHERE záradék. Az Adatszinkronizálás nem kezeli ezt a feltételt. A probléma megoldásához távolítsa el a WHERE záradékot, vagy manuálisan hajtsa végre a módosításokat az összes adatbázison. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a>Hogyan kezeli az Adatszinkronizálás a körkörös hivatkozásokat? Ez azt, hogy ha ugyanazokat az adatokat több szinkronizálási csoportban szinkronizálja, és ennek eredményeképpen folyamatosan változik?
Az Adatszinkronizálás nem kezeli a körkörös hivatkozásokat. Ügyeljen arra, hogy elkerüljék őket. 

## <a name="client-agent-issues"></a>Ügyfélügynöki problémák

Az ügyfélügynökkel kapcsolatos problémák elhárításához olvassa el az [Adatszinkronizálási ügynökkel kapcsolatos problémák elhárítása című témakört.](sql-database-data-sync-agent.md#agent-tshoot)

## <a name="setup-and-maintenance-issues"></a>Telepítési és karbantartási problémák

- ["Lemezterülettel elfogyott" üzenet jelenik meg](#setup-space)

- [Nem tudom törölni a szinkronizálási csoportot](#setup-delete)

- [Nem lehet törölni a helyszíni SQL Server-adatbázis regisztrációját](#setup-unreg)

- [Nincs megfelelő jogosultságom a rendszerszolgáltatások indításához](#setup-perms)

- [Az adatbázis "Elavult" állapotú](#setup-date)

- [A szinkronizálási csoport "Elavult" állapotú](#setup-date2)

- [A szinkronizálási csoport nem törölhető az ügynök eltávolítását vagy leállítását követő három percen belül](#setup-delete2)

- [Mi történik, ha visszaállítok egy elveszett vagy sérült adatbázist?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a>"Lemezterülettel elfogyott" üzenet jelenik meg

- **Oka**. Ha a maradék fájlokat törölni kell, megjelenhet a "lemezterület ből" üzenet. Ezt okozhatja víruskereső szoftver, vagy a fájlok meg vannak nyitva, amikor törlési műveleteket kísérelnek meg.

- **felbontás .** Törölje manuálisan a %temp% mappában`del \*sync\* /s`( lévő szinkronizálási fájlokat. Ezután törölje az alkönyvtárakat a %temp% mappában.

> [!IMPORTANT]
> Ne töröljön egyetlen fájlt sem, amíg a szinkronizálás folyamatban van.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a>Nem tudom törölni a szinkronizálási csoportot

A szinkronizálási csoport törlésére tett kísérlet sikertelen. Az alábbi esetek bármelyike a szinkronizálási csoport törlésének sikertelensége lehet:

- **Oka**. Az ügyfélügynök offline állapotú.

- **felbontás .** Győződjön meg arról, hogy az ügyfélügynök online állapotban van, majd próbálkozzon újra.

- **Oka**. Az ügyfélügynök el lett távolítva vagy hiányzik.

- **felbontás .** Ha az ügyfélügynök el lett távolítva vagy más okból hiányzik:  
    a. Távolítsa el az ügynök XML-fájlját az SQL Data Sync telepítési mappájából, ha létezik ez a fájl.  
    b. Telepítse az ügynököt a helyszíni számítógépen (ez ugyanaz vagy egy másik számítógép is lehet). Ezután küldje el a portálon létrehozott ügynökkulcsot az offline állapotúként megjelenő ügynöknek.

- **Oka**. Az adatbázis offline állapotban van.

- **felbontás .** Győződjön meg arról, hogy az SQL-adatbázisok és az SQL Server-adatbázisok mind online állapotban vannak.

- **Oka**. A szinkronizálási csoport kiépítése vagy szinkronizálása.

- **felbontás .** Várjon, amíg a létesítési vagy szinkronizálási folyamat befejeződik, majd próbálja meg újra lefektetheti a szinkronizálási csoport törlését.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a><a name="setup-unreg"></a>Nem lehet törölni a helyszíni SQL Server-adatbázis regisztrációját

- **Oka**. Valószínűleg egy már törölt adatbázis regisztrációjának törlését próbálja meg törölni.

- **felbontás .** A helyszíni SQL Server-adatbázis regisztrációjának megszüntetéséhez jelölje ki az adatbázist, majd válassza **a Törlés kényszerítése**lehetőséget.

  Ha ez a művelet nem tudja eltávolítani az adatbázist a szinkronizálási csoportból:

  1. Állítsa le, majd indítsa újra az ügyfélügynök gazdaszolgáltatását:  
    a. Válassza a **Start** menüt.  
    b. A keresőmezőbe írja be a **services.msc**.  
    c. A keresési eredmények ablaktábla **Programok** szakaszában kattintson duplán a **Szolgáltatások**elemre.  
    d. Kattintson a jobb gombbal az **SQL Data Sync** szolgáltatásra.  
    e. Ha a szolgáltatás fut, állítsa le.  
    f. Kattintson a jobb gombbal a szolgáltatásra, és válassza a **Start parancsot.**  
    g. Ellenőrizze, hogy az adatbázis még regisztrálva van-e. Ha már nincs regisztrálva, akkor kész. Ellenkező esetben folytassa a következő lépéssel.
  1. Nyissa meg az ügyfélügynök-alkalmazást (SqlAzureDataSyncAgent).
  1. Válassza **a Hitelesítő adatok szerkesztése**lehetőséget, majd adja meg az adatbázis hitelesítő adatait.
  1. Folytassa a regisztráció törlését.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a>Nincs megfelelő jogosultságom a rendszerszolgáltatások indításához

- **Oka**. Ez a hiba két esetben fordul elő:
  -   A felhasználónév és/vagy a jelszó helytelen.
  -   A megadott felhasználói fiók nem rendelkezik megfelelő jogosultságokkal a szolgáltatásként való bejelentkezéshez.

- **felbontás .** Bejelentkezési hitelesítő adatok megadása a felhasználói fiókhoz:

  1. Nyissa meg a**Vezérlőpult** > **felügyeleti eszközök** > helyi biztonsági házirend > helyi**házirendjének** > felhasználói jogkezelése című**Local Security Policy****lapját.** **Start** > 
  1. Válassza **a Bejelentkezés szolgáltatásként**lehetőséget.
  1. A **Tulajdonságok** párbeszédpanelen adja hozzá a felhasználói fiókot.
  1. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.
  1. Zárjon be minden ablakot.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a>Az adatbázis "Elavult" állapotú

- **Oka**. Az SQL Data Sync eltávolítja azokat az adatbázisokat, amelyek 45 napig vagy annál több napig offline állapotban vannak a szolgáltatásból (az adatbázis offline állapotba kerülésének időpontjától számítva). Ha egy adatbázis 45 napig vagy annál több napig offline állapotban van, majd újra online állapotba kerül, állapota **elavult.**

- **felbontás .** Elkerülheti **az elavult** állapotot, ha biztosítja, hogy egyik adatbázis se legyen offline állapotba 45 napig vagy tovább.

  Ha egy adatbázis állapota **elavult:**

  1. Távolítsa el az **elavult** állapotú adatbázist a szinkronizálási csoportból.
  1. Adja hozzá újra az adatbázist a szinkronizálási csoporthoz.

  > [!WARNING]
  > Az adatbázisban offline állapotban végzett összes módosítás elvesz.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a>A szinkronizálási csoport "Elavult" állapotú

- **Oka**. Ha egy vagy több módosítás nem alkalmazható a teljes 45 napos megőrzési időszakra, a szinkronizálási csoport elavulttá válhat.

- **felbontás .** A szinkronizálási csoport **elavult állapotának** elkerülése érdekében rendszeresen vizsgálja meg a szinkronizálási feladatok eredményeit az előzménymegjelenítőben. Vizsgálja meg és oldja fel a nem alkalmazható módosításokat.

  Ha egy szinkronizálási csoport állapota **Elavult,** törölje a szinkronizálási csoportot, majd hozza létre újra.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a>A szinkronizálási csoport nem törölhető az ügynök eltávolítását vagy leállítását követő három percen belül

A társított SQL Data Sync ügyfélügynök eltávolítását vagy leállítását követő három percen belül nem törölhet i szinkronizálási csoportot.

- **felbontás .**

  1. A szinkronizálási csoport eltávolítása, amíg a társított szinkronizálási ügynökök online állapotban vannak (ajánlott).
  1. Ha az ügynök offline állapotban van, de telepítve van, hozza online állapotba a helyszíni számítógépen. Várja meg, amíg az ügynök állapota **online** állapotúként jelenik meg az SQL Data Sync portálon. Ezután távolítsa el a szinkronizálási csoportot.
  1. Ha az ügynök offline állapotban van, mert eltávolították:  
    a.  Távolítsa el az ügynök XML-fájlját az SQL Data Sync telepítési mappájából, ha létezik ez a fájl.  
    b.  Telepítse az ügynököt a helyszíni számítógépen (ez ugyanaz vagy egy másik számítógép is lehet). Ezután küldje el a portálon létrehozott ügynökkulcsot az offline állapotúként megjelenő ügynöknek.  
    c. Próbálja meg törölni a szinkronizálási csoportot.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a>Mi történik, ha visszaállítok egy elveszett vagy sérült adatbázist?

Ha egy elveszett vagy sérült adatbázist visszaállít egy biztonsági másolatból, előfordulhat, hogy az adatbázishoz tartozó szinkronizálási csoportokban nem konvergenciája van.

## <a name="next-steps"></a>További lépések
Az SQL Data Sync programról további információt a következő témakörben talál:

-   Áttekintés – [Adatok szinkronizálása több felhőbeli és helyszíni adatbázisban az Azure SQL Data Sync segítségével](sql-database-sync-data.md)
-   Adatszinkronizálás beállítása
    - A portálon – [Oktatóanyag: Az SQL Data Sync beállítása az Adatok szinkronizálásához az Azure SQL Database és a helyszíni SQL Server között](sql-database-get-started-sql-data-sync.md)
    - A PowerShell-lel
        -  [A PowerShell használata több Azure SQL-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [A PowerShell használata egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Adatszinkronizálási ügynök – [Adatszinkronizálási ügynök az Azure SQL Data Sync szolgáltatáshoz](sql-database-data-sync-agent.md)
-   Gyakorlati tanácsok – [Gyakorlati tanácsok az Azure SQL Data Sync szolgáltatáshoz](sql-database-best-practices-data-sync.md)
-   Figyelő – [SQL-adatszinkronizálás figyelése az Azure Figyelő naplóival](sql-database-sync-monitor-oms.md)
-   A szinkronizálási séma frissítése
    -   Transact-SQL - [A sémamódosítások replikációjának automatizálása az Azure SQL Data Sync-ben](sql-database-update-sync-schema.md)
    -   A PowerShell használatával – [A PowerShell használatával frissítse a szinkronizálási sémát egy meglévő szinkronizálási csoportban](scripts/sql-database-sync-update-schema.md)

Az SQL Database adatbázisról további információt a következő témakörben talál:

-   [Az SQL Database áttekintése](sql-database-technical-overview.md)
-   [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
