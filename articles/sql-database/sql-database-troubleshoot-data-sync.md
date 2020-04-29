---
title: Az SQL Data Synch hibaelhárítása
description: Ismerje meg, hogy miként lehet elhárítani az Azure SQL-adatszinkronizálás gyakori hibáit.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: d6ea604446cb9d56bb699685d24c81992bcac3a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382896"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Az SQL Data Synckel kapcsolatos hibák elhárítása

Ez a cikk az Azure SQL-adatszinkronizálás ismert problémáinak elhárítását ismerteti. Ha probléma merül fel, az itt megadott megoldás.

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](sql-database-sync-data.md) ismertető cikket.

> [!IMPORTANT]
> Az Azure SQL-adatszinkronizálás jelenleg **nem** támogatja Azure SQL Database felügyelt példányok használatát.

## <a name="sync-issues"></a>Szinkronizálási problémák

- [A szinkronizálás sikertelen a portál felhasználói felületén az ügyfél ügynökéhez társított helyszíni adatbázisok esetében](#sync-fails)

- [A szinkronizálási csoportom megakadt a feldolgozási állapotban](#sync-stuck)

- [A táblázatokban hibás információk jelennek meg](#sync-baddata)

- [A sikeres szinkronizálás után inkonzisztens elsődleges kulcs-adatértékek jelennek meg](#sync-pkdata)

- [Jelentős romlást látok a teljesítményben](#sync-perf)

- [A következő üzenet jelenik meg: "a NULL értéket nem lehet beszúrni \<az oszlop oszlopba>. Az oszlop nem engedélyezi a null értéket. " Mit jelent ez, és Hogyan javíthatom?](#sync-nulls)

- [Hogyan kezeli az adatszinkronizálás a körkörös hivatkozásokat? Ez azt jelenti, hogy ha ugyanazokat az adatfájlokat szinkronizálja több szinkronizálási csoportban, és ennek eredményeként változik?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a>A szinkronizálás sikertelen a portál felhasználói felületén az ügyfél ügynökéhez társított helyszíni adatbázisok esetében

A szinkronizálás sikertelen a SQL-adatszinkronizálás portál felhasználói felületén az ügyfél ügynökéhez társított helyszíni adatbázisokhoz. Az ügynököt futtató helyi számítógépen a System. IO. IOException hibák jelennek meg az eseménynaplóban. A hibák azt mondják, hogy a lemezen nincs elég hely.

- **OK**. A meghajtón nincs elég hely.

- **Megoldás**. Hozzon létre több helyet azon a meghajtón, amelyen a% TEMP% könyvtár található.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a>A szinkronizálási csoportom megakadt a feldolgozási állapotban

A SQL-adatszinkronizálás szinkronizálási csoportja hosszú ideje már a feldolgozási állapotban van. Nem válaszol a **Leállítás** parancsra, és a naplók nem jelenítenek meg új bejegyzéseket.

A következő feltételek bármelyike előfordulhat, hogy a szinkronizálási csoport elakad a feldolgozási állapotban:

- **OK**. Az ügyfélügynök offline állapotú.

- **Megoldás**. Győződjön meg arról, hogy az ügyfélügynök online állapotban van, és próbálkozzon újra.

- **OK**. Az ügyfélügynök el lett távolítva vagy hiányzik.

- **Megoldás**. Ha az ügyfélügynök el lett távolítva vagy más okból hiányzik:

    1. Távolítsa el az ügynök XML-fájlját az SQL Data Sync telepítési mappájából, ha létezik ez a fájl.
    1. Telepítse az ügynököt a helyszíni számítógépen (ez ugyanaz vagy egy másik számítógép is lehet). Ezután küldje el a portálon létrehozott ügynökkulcsot az offline állapotúként megjelenő ügynöknek.

- **OK**. Az SQL Data Sync szolgáltatás leállt.

- **Megoldás**. Indítsa újra a SQL-adatszinkronizálás szolgáltatást.

    1. A **Start** menüben keressen a **szolgáltatások**elemre.
    1. A keresési eredmények között válassza a **szolgáltatások**lehetőséget.
    1. A **SQL-adatszinkronizálás** szolgáltatás megkeresése.
    1. Ha a szolgáltatás állapota **leállt**, kattintson a jobb gombbal a szolgáltatás nevére, majd válassza az **Indítás**lehetőséget.

> [!NOTE]
> Ha az előző információ nem helyezi át a szinkronizálási csoportot a feldolgozási állapotból, Microsoft ügyfélszolgálata állíthatja vissza a szinkronizálási csoport állapotát. A szinkronizálási csoport állapotának alaphelyzetbe állításához a [Azure SQL Database fórumban](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)hozzon létre egy bejegyzést. A bejegyzésben adja meg az előfizetés-AZONOSÍTÓját és a szinkronizálási csoport AZONOSÍTÓját az alaphelyzetbe állítani kívánt csoport számára. Egy Microsoft ügyfélszolgálata mérnök válaszol a bejegyzésre, és tudatja Önnel, ha az állapot alaphelyzetbe áll.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a>A táblázatokban hibás információk jelennek meg

Ha az azonos nevű, de eltérő adatbázis-sémából származó táblák is szerepelnek a szinkronizálásban, a szinkronizálás után a táblákban hibás adatok jelennek meg.

- **OK**. Az SQL-adatszinkronizálás kiépítési folyamat ugyanazokat a követési táblákat használja, mint a táblák, amelyek azonos nevűek, de különböző sémákban találhatók. Ennek következtében mindkét táblából származó módosítások ugyanabban a követési táblában szerepelnek. Ez a szinkronizálás során hibás adatváltozásokat okoz.

- **Megoldás**. Győződjön meg arról, hogy a szinkronizálásban részt vevő táblák nevei eltérőek, még akkor is, ha a táblák egy adatbázis különböző sémái közé tartoznak.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a>A sikeres szinkronizálás után inkonzisztens elsődleges kulcs-adatértékek jelennek meg

A szinkronizálás sikeresként van jelezve, és a napló nem jelenít meg sikertelen vagy kihagyott sorokat, de azt is észleli, hogy az elsődleges kulcs adatai nem konzisztensek a szinkronizálási csoport adatbázisai között.

- **OK**. Ennek az eredménynek a kialakítása. Az elsődleges kulcs oszlopaiban bekövetkezett változások inkonzisztens adatértéket eredményeznek azokban a sorokban, amelyeken az elsődleges kulcs módosult.

- **Megoldás**. A probléma elkerülése érdekében győződjön meg arról, hogy a rendszer nem módosítja az elsődleges kulcs oszlopában lévő összes adattípust. A probléma elhárítása után törölje a szinkronizálási csoport összes végpontjának inkonzisztens adatait tartalmazó sort. Ezután szúrja be újra a sort.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a>Jelentős romlást látok a teljesítményben

A teljesítmény jelentősen csökken, valószínűleg arra a pontra, ahol még nem is megnyithatja az adatszinkronizálási felhasználói felületet.

- **OK**. A legvalószínűbb ok egy szinkronizálási hurok. Szinkronizálási hurok akkor következik be, amikor egy szinkronizálási csoport szinkronizálja a B szinkronizálási csoporttal a szinkronizálást, majd az A csoport szinkronizálását indítja el. A tényleges helyzet bonyolultabb lehet, és több mint két szinkronizálási csoportot is magában foglalhat a hurokban. Ennek a problémának az az oka, hogy a szinkronizálási csoportok egymással átfedésben lévő szinkronizálási szinkronizálása körkörösen aktiválódik.

- **Megoldás**. A legjobb megoldás a megelőzés. Győződjön meg arról, hogy nincs körkörös hivatkozás a szinkronizálási csoportokban. Az egyik szinkronizálási csoporttal szinkronizált sorokat nem lehet szinkronizálni egy másik szinkronizálási csoport.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a>A következő üzenet jelenik meg: "a NULL értéket nem lehet beszúrni \<az oszlop oszlopba>. Az oszlop nem engedélyezi a null értéket. " Mit jelent ez, és Hogyan javíthatom? 
Ez a hibaüzenet azt jelzi, hogy a következő két probléma egyike történt:
-  Egy táblához nem tartozik elsődleges kulcs. A probléma megoldásához vegyen fel egy elsődleges kulcsot az összes szinkronizálni kívánt táblába.
-  Létezik egy WHERE záradék a CREATE INDEX utasításban. Az adatszinkronizálás nem kezeli ezt az állapotot. A probléma megoldásához távolítsa el a WHERE záradékot, vagy manuálisan végezze el a módosításokat az összes adatbázison. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a>Hogyan kezeli az adatszinkronizálás a körkörös hivatkozásokat? Ez azt jelenti, hogy ha ugyanazokat az adatfájlokat szinkronizálja több szinkronizálási csoportban, és ennek eredményeként változik?
Az adatszinkronizálás nem kezeli a körkörös hivatkozásokat. Ne felejtse el elkerülni őket. 

## <a name="client-agent-issues"></a>Ügyfél-ügynökkel kapcsolatos problémák

Az ügyfél ügynökével kapcsolatos problémák elhárításához lásd: [az adatszinkronizálási ügynökkel kapcsolatos problémák elhárítása](sql-database-data-sync-agent.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Telepítési és karbantartási problémák

- ["Lemezről kifogyott" üzenet jelenik meg](#setup-space)

- [Nem tudom törölni a szinkronizálási csoportot](#setup-delete)

- [Nem tudom törölni a helyszíni SQL Server adatbázis regisztrációját](#setup-unreg)

- [Nem rendelkezem megfelelő jogosultsággal a rendszerszolgáltatások indításához](#setup-perms)

- [Egy adatbázis "elavult" állapottal rendelkezik](#setup-date)

- [Egy szinkronizálási csoport "elavult" állapottal rendelkezik](#setup-date2)

- [Egy szinkronizálási csoport nem törölhető az ügynök eltávolítása vagy leállítása után három percen belül](#setup-delete2)

- [Mi történik, ha egy elveszett vagy sérült adatbázist állítok vissza?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a>"Lemezről kifogyott" üzenet jelenik meg

- **OK**. Előfordulhat, hogy a "lemez elfogyva" üzenet jelenik meg, ha a maradék fájlokat törölni kell. Ennek oka az lehet, hogy a víruskereső szoftver, vagy ha a törlési műveletek során a fájlok meg vannak nyitva.

- **Megoldás**. Törölje kézzel a (z)% Temp% mappában (`del \*sync\* /s`) lévő szinkronizálási fájlokat. Ezután törölje a (z)% Temp% mappában található alkönyvtárakat.

> [!IMPORTANT]
> Ne töröljön fájlokat, amíg a szinkronizálás folyamatban van.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a>Nem tudom törölni a szinkronizálási csoportot

A szinkronizálási csoport törlésére tett kísérlet sikertelen. A következő forgatókönyvek bármelyike hibát okozhat a szinkronizálási csoport törlésében:

- **OK**. Az ügyfélügynök offline állapotú.

- **Megoldás**. Győződjön meg arról, hogy az ügyfél ügynöke online állapotban van, majd próbálkozzon újra.

- **OK**. Az ügyfélügynök el lett távolítva vagy hiányzik.

- **Megoldás**. Ha az ügyfélügynök el lett távolítva vagy más okból hiányzik:  
    a. Távolítsa el az ügynök XML-fájlját az SQL Data Sync telepítési mappájából, ha létezik ez a fájl.  
    b. Telepítse az ügynököt a helyszíni számítógépen (ez ugyanaz vagy egy másik számítógép is lehet). Ezután küldje el a portálon létrehozott ügynökkulcsot az offline állapotúként megjelenő ügynöknek.

- **OK**. Az adatbázis offline állapotban van.

- **Megoldás**. Győződjön meg arról, hogy az SQL-adatbázisok és a SQL Server-adatbázisok mind online állapotban vannak.

- **OK**. A szinkronizálási csoport kiépítés vagy szinkronizálás.

- **Megoldás**. Várjon, amíg befejeződik a kiépítési vagy szinkronizálási folyamat, majd próbálkozzon újra a szinkronizálási csoport törlésével.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a><a name="setup-unreg"></a>Nem tudom törölni a helyszíni SQL Server adatbázis regisztrációját

- **OK**. Legvalószínűbb, hogy olyan adatbázist próbál meg törölni, amely már törölve lett.

- **Megoldás**. Helyszíni SQL Server-adatbázis regisztrációjának törléséhez válassza ki az adatbázist, majd válassza a **kényszerített törlés**lehetőséget.

  Ha a művelet nem tudja eltávolítani az adatbázist a szinkronizálási csoportból:

  1. Állítsa le, majd indítsa újra az ügyfél-ügynök gazdagép szolgáltatást:  
    a. Kattintson a **Start** menüre.  
    b. A keresőmezőbe írja be a **Services. msc**kifejezést.  
    c. A keresési eredmények ablaktábla **programok** szakaszában kattintson duplán a **szolgáltatások**elemre.  
    d. Kattintson a jobb gombbal a **SQL-adatszinkronizálás** szolgáltatásra.  
    e. Ha a szolgáltatás fut, állítsa le.  
    f. Kattintson a jobb gombbal a szolgáltatásra, majd válassza az **Indítás**lehetőséget.  
    g. Győződjön meg arról, hogy az adatbázis még regisztrálva van-e. Ha már nincs regisztrálva, elkészült. Ellenkező esetben folytassa a következő lépéssel.
  1. Nyissa meg az ügyfél-ügynök alkalmazást (SqlAzureDataSyncAgent).
  1. Válassza a **hitelesítő adatok szerkesztése**lehetőséget, majd adja meg az adatbázishoz tartozó hitelesítő adatokat.
  1. Folytassa a regisztráció megszüntetésével.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a>Nem rendelkezem megfelelő jogosultsággal a rendszerszolgáltatások indításához

- **OK**. Ez a hiba két esetben fordul elő:
  -   A Felhasználónév és/vagy a jelszó helytelen.
  -   A megadott felhasználói fiók nem rendelkezik megfelelő jogosultsággal a szolgáltatásként való bejelentkezéshez.

- **Megoldás**. A szolgáltatáshoz tartozó bejelentkezési hitelesítő adatok megadása a felhasználói fióknak:

  1. Ugrás a **Start** > **Vezérlőpult** > **felügyeleti eszközök** > **helyi biztonsági házirend** > **helyi házirend** > **felhasználói Rights Management**.
  1. Válassza a **Bejelentkezés szolgáltatásként**lehetőséget.
  1. A **Tulajdonságok** párbeszédpanelen adja hozzá a felhasználói fiókot.
  1. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.
  1. Az összes Windows lezárása.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a>Egy adatbázis "elavult" állapottal rendelkezik

- **OK**. A SQL-adatszinkronizálás eltávolítja azokat az adatbázisokat, amelyek offline állapotban voltak a szolgáltatásból 45 napig vagy többet (az adatbázis offline állapotának időpontjától számítva). Ha egy adatbázis 45 vagy több napig offline állapotú, majd ismét elérhető **, az állapota elavult.**

- **Megoldás**. Elkerülheti az **elavult állapotot** azáltal, hogy az adatbázisok egyike sem érhető el 45 vagy több napig.

  Ha **az adatbázis állapota elavult:**

  1. Távolítsa el a szinkronizálási csoport **elavult állapotú** adatbázisát.
  1. Adja vissza az adatbázist a szinkronizálási csoportba.

  > [!WARNING]
  > Az adatbázison végrehajtott összes módosítást a kapcsolat nélküli üzemmódban elveszíti.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a>Egy szinkronizálási csoport "elavult" állapottal rendelkezik

- **OK**. Ha egy vagy több módosítás nem alkalmazható a 45 napos teljes megőrzési időtartamra, a szinkronizálási csoport elavult lehet.

- **Megoldás**. Egy szinkronizálási csoport **elavult** állapotának elkerüléséhez rendszeresen vizsgálja meg a szinkronizálási feladatok eredményeit az előzmények megjelenítőben. Vizsgálja meg és oldja fel a nem alkalmazandó módosításokat.

  Ha a szinkronizálási csoport állapota elavult **, törölje**a szinkronizálási csoportot, majd hozza létre újra.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a>Egy szinkronizálási csoport nem törölhető az ügynök eltávolítása vagy leállítása után három percen belül

A szinkronizálási csoport nem törölhető három percen belül a társított SQL-adatszinkronizálás ügyfél-ügynök eltávolítása vagy leállítása után.

- **Megoldás**.

  1. A szinkronizálási csoport eltávolítása, miközben a társított szinkronizálási ügynökök online (ajánlott).
  1. Ha az ügynök offline állapotban van, de telepítve van, kapcsolja online állapotba a helyszíni számítógépen. Várja meg, hogy az ügynök állapota **online** állapotú legyen a SQL-adatszinkronizálás portálon. Ezután távolítsa el a szinkronizálási csoportot.
  1. Ha az ügynök offline állapotú, mert el lett távolítva:  
    a.  Távolítsa el az ügynök XML-fájlját az SQL Data Sync telepítési mappájából, ha létezik ez a fájl.  
    b.  Telepítse az ügynököt a helyszíni számítógépen (ez ugyanaz vagy egy másik számítógép is lehet). Ezután küldje el a portálon létrehozott ügynökkulcsot az offline állapotúként megjelenő ügynöknek.  
    c. Próbálja meg törölni a szinkronizálási csoportot.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a>Mi történik, ha egy elveszett vagy sérült adatbázist állítok vissza?

Ha egy elveszett vagy sérült adatbázist állít vissza egy biztonsági másolatból, előfordulhat, hogy az adatbázishoz tartozó szinkronizálási csoportokba tartozó adatok nem konvergálnak.

## <a name="next-steps"></a>További lépések
További információ a SQL-adatszinkronizálásról:

-   Áttekintés – az [adatszinkronizálás több felhőalapú és helyszíni adatbázis között az Azure SQL-adatszinkronizálás](sql-database-sync-data.md)
-   Adatszinkronizálás beállítása
    - A portálon – [oktatóanyag: SQL-adatszinkronizálás beállítása az Azure SQL Database és a helyszíni SQL Server közötti adatszinkronizáláshoz](sql-database-get-started-sql-data-sync.md)
    - A PowerShell-lel
        -  [A PowerShell használata több Azure SQL-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [A PowerShell használata egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Adatszinkronizálási ügynök – [Az Azure SQL-adatszinkronizálás adatszinkronizálási ügynöke](sql-database-data-sync-agent.md)
-   Ajánlott eljárások – [ajánlott eljárások az Azure SQL-adatszinkronizálás](sql-database-best-practices-data-sync.md)
-   Figyelő – [SQL-adatszinkronizálás figyelése Azure monitor naplókkal](sql-database-sync-monitor-oms.md)
-   A szinkronizálási séma frissítése
    -   A Transact-SQL- [ben – automatizálja a séma változásainak az Azure-ban való replikálását SQL-adatszinkronizálás](sql-database-update-sync-schema.md)
    -   A PowerShell [használatával – egy meglévő szinkronizálási csoportban lévő szinkronizálási séma frissítéséhez használja a PowerShellt](scripts/sql-database-sync-update-schema.md) .

További információ a SQL Databaseról:

-   [Az SQL Database áttekintése](sql-database-technical-overview.md)
-   [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
