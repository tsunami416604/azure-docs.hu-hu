---
title: Az Azure SQL Data Synch hibaelhárítása |} A Microsoft Docs
description: Ismerje meg az Azure SQL Data Sync szolgáltatással kapcsolatos gyakori problémák elhárítása.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 4ab26891a98418fae75c3be18f17f1d8e02d8b36
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216713"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Az SQL Data Sync szolgáltatással kapcsolatos problémák elhárítása

Ez a cikk ismerteti az Azure SQL Data Sync szolgáltatással kapcsolatos ismert problémák elhárítása. Ha a probléma megoldása, azt itt biztosítja.

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](sql-database-sync-data.md) ismertető cikket.

> [!IMPORTANT]
> Az Azure SQL Data Sync does **nem** támogatja az Azure SQL Database felügyelt példánya jelenleg.

## <a name="sync-issues"></a>Szinkronizálási hibák

- [A portál felhasználói Felületét a helyszíni adatbázisok az ügyfélügynök társított sikertelen szinkronizálás](#sync-fails)

- [A szinkronizálási csoport Beragadt a feldolgozási állapot](#sync-stuck)

- [Hibás adatok láthatók a az általam használt táblák](#sync-baddata)

- [Elsődleges kulcs inkonzisztens adatok sikeres szinkronizálás után láthatók](#sync-pkdata)

- [A teljesítmény jelentős csökkenéséhez láthatók](#sync-perf)

- [Ez az üzenet jelenik meg: "A NULL érték nem lehet beszúrni az oszlop <column>. Oszlop nem engedélyezi a null értékeket." Ez mit jelent, és hogyan javíthatom azt?](#sync-nulls)

- [Hogyan kezeli a Data Syncet. a körkörös hivatkozások? Azt jelenti, amikor ugyanazokat az adatokat több szinkronizálási csoportban szinkronizálva van, és ennek eredményeképpen tartja módosítása?](#sync-circ)

### <a name="sync-fails"></a> A portál felhasználói Felületét a helyszíni adatbázisok az ügyfélügynök társított sikertelen szinkronizálás

Szinkronizálás a helyszíni adatbázisokhoz társított az ügyfélügynök az SQL Data Sync portal felhasználói felületén sikertelen lesz. A helyi számítógépen, amelyen az ügynök fut System.IO.IOException hibákat az eseménynaplóban talál. A hibák tegyük fel, hogy a lemez nincs elegendő lemezterület.

- **OK**. A meghajtó nincs elegendő lemezterület.

- **Feloldási**. Hozzon létre több helyet az a meghajtó, amelyre a % TEMP % könyvtárban található.

### <a name="sync-stuck"></a> A szinkronizálási csoport Beragadt a feldolgozási állapot

Az SQL Data Sync szinkronizálási csoport lett a feldolgozási állapot a hosszú ideig. Nem válaszol a **leállítása** parancsot, és a naplók megjelenítése nincs új bejegyzés.

Az alábbi feltételek bármelyike azt eredményezheti, hogy a szinkronizálási csoport éppen kerültek a feldolgozási állapot:

- **OK**. Az ügyfélügynök offline állapotú.

- **Feloldási**. Győződjön meg arról, hogy az ügyfélügynök online állapotban van, és próbálkozzon újra.

- **OK**. Az ügyfélügynök el lett távolítva vagy hiányzik.

- **Feloldási**. Ha az ügyfélügynök el lett távolítva vagy más okból hiányzik:

    1. Távolítsa el az ügynök XML-fájlját az SQL Data Sync telepítési mappájából, ha létezik ez a fájl.
    1. Telepítse az ügynököt a helyszíni számítógépen (ez ugyanaz vagy egy másik számítógép is lehet). Ezután küldje el a portálon létrehozott ügynökkulcsot az offline állapotúként megjelenő ügynöknek.

- **OK**. Az SQL Data Sync szolgáltatás leállt.

- **Feloldási**. Indítsa újra az SQL Data Sync szolgáltatást.

    1. Az a **Start** menüt, és keressen **szolgáltatások**.
    1. A keresési eredmények között, válassza ki a **szolgáltatások**.
    1. Keresse meg a **SQL Data Sync** szolgáltatás.
    1. Ha a szolgáltatás állapota **leállítva**, és kattintson a jobb gombbal a szolgáltatás nevét, majd válassza ki **Start**.

> [!NOTE]
> Ha a fenti információ nem helyezi át a szinkronizálási csoport a feldolgozási állapotát, a Microsoft Support alaphelyzetbe állíthatja a szinkronizálási csoport állapotát. Szeretné, hogy a szinkronizálási csoport állapotának alaphelyzetbe állítása, az a [Azure SQL Database fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), létrehoz egy bejegyzést. A bejegyzésben található tartalmazza az előfizetés-Azonosítóját és a csoport, amely alaphelyzetbe kell állítani a szinkronizálási csoport azonosítója. A Microsoft Support hozzászólása fog válaszolni, és értesíti, amikor az állapot vissza lett állítva.

### <a name="sync-baddata"></a> Hibás adatok láthatók a az általam használt táblák

Ha táblákat, amelyek ugyanazzal a névvel, de amelyek a különböző adatbázissémák szinkronizálást is szerepel, a szinkronizálás után látható hibás a táblákban lévő adatokat.

- **OK**. Az SQL Data Sync létesítésének folyamatát kell használnia az azonos nyomkövetési táblát, amely ugyanazzal a névvel, de amelyek a különböző sémákkal táblák használ. Emiatt a változásokat a mindkét táblázatot követési ugyanabban a táblában is megjelennek. Ennek hatására a hibás adatváltozásokat szinkronizálás során.

- **Feloldási**. Győződjön meg arról, hogy, amelyek szerepet játszanak szinkronizálást táblák nevének különböző, még akkor is, ha a táblák egy adatbázisban eltérő sémákkal tartoznak.

### <a name="sync-pkdata"></a> Elsődleges kulcs inkonzisztens adatok sikeres szinkronizálás után láthatók

Szinkronizálási az elvártnak megfelelően sikeres, és a napló nem sikertelen vagy kihagyott sorok jeleníti meg, de, ellenőrizze, hogy elsődleges kulcs adatok inkonzisztens a szinkronizálási csoport az adatbázisok között.

- **OK**. Ezt az eredményt a rendszer kialakításából fakad. Minden elsődlegeskulcs-oszlopot a módosításokat az elsődleges kulcs módosított sorok inkonzisztens adatokat eredményez.

- **Feloldási**. Ez a probléma elkerülése érdekében győződjön meg arról, hogy az elsődleges kulcsot tartalmazó adatok nem változik. A probléma megoldásához, végrehajtása után törölje a sort, a szinkronizálási csoportban lévő összes végpont inkonzisztens adatokat. Ezután helyezze vissza a sort.

### <a name="sync-perf"></a> A teljesítmény jelentős csökkenéséhez láthatók

A teljesítmény replikákban jelentősen megnő, valószínűleg a pont, ahol még nem lehet megnyitni a Data Sync felhasználói felületén.

- **OK**. A legvalószínűbb oka egy szinkronizálási ciklust. Egy szinkronizálási ciklust akkor fordul elő, A sync által szinkronizálási csoport szinkronizálási által szinkronizálási csoport B, amely majd elindítják a sync által szinkronizálási csoport A. aktiválásakor A tényleges helyzet előfordulhat, hogy nem elég bonyolult, és azt is járhat, a hurok több mint két szinkronizálási csoportok. A probléma okozza, hogy van egy kör alakú elindítása, amely a szinkronizálás egymással átfedésben lévő, egy másik szinkronizálási csoportok.

- **Feloldási**. A legjobb javítás megelőzési. Győződjön meg arról, hogy a szinkronizálási csoport nem rendelkezik. a körkörös hivatkozás. Minden sor egy szinkronizálási csoport által szinkronizálva van egy másik szinkronizálási csoport nem szinkronizálható.

### <a name="sync-nulls"></a> Ez az üzenet jelenik meg: "A NULL érték nem lehet beszúrni az oszlop <column>. Oszlop nem engedélyezi a null értékeket." Ez mit jelent, és hogyan javíthatom azt? 
Ez a hibaüzenet azt jelzi, hogy a két alábbi problémák egyike történt:
-  Egy táblázat nem rendelkezik elsődleges kulccsal. A probléma megoldásához adja hozzá az elsődleges kulcs szinkronizál az összes táblákhoz való.
-  A CREATE INDEX utasítás WHERE záradék szerepel. Ez az állapot nem kezeli az adatok szinkronizálása. A probléma megoldásához távolítsa el a WHERE záradékban, vagy manuálisan végezze el a módosításokat az összes adatbázishoz. 
 
### <a name="sync-circ"></a> Hogyan kezeli a Data Syncet. a körkörös hivatkozások? Azt jelenti, amikor ugyanazokat az adatokat több szinkronizálási csoportban szinkronizálva van, és ennek eredményeképpen tartja módosítása?
Adatok szinkronizálása. a körkörös hivatkozások nem kezeli. Mindenképpen kerülje azokat. 

## <a name="client-agent-issues"></a>Ügyfél ügynök kapcsolatos problémák

Az ügyfélügynök-problémák hibaelhárítása: [Data Sync-ügynök hibaelhárítása problémák](sql-database-data-sync-agent.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Telepítési és karbantartási kapcsolatos problémák

- ["Lemezterületét lemez" üzenet jelenik meg](#setup-space)

- [A szinkronizálási csoport nem lehet törölni](#setup-delete)

- [Tudok egy helyszíni SQL Server-adatbázis regisztrációja nem törölhető](#setup-unreg)

- [Nem rendelkezem a megfelelő jogosultsággal a rendszerszolgáltatások indításához](#setup-perms)

- [Egy adatbázis állapota "Elavult"](#setup-date)

- [Szinkronizálási csoport tartalmaz egy "Elavult" állapota](#setup-date2)

- [Szinkronizálási csoport nem lehet törölni, eltávolítása vagy leállítása folyamatban van az ügynök három percen belül](#setup-delete2)

- [Mi történik, ha vissza tudok állítani egy elveszett vagy sérült adatbázis?](#setup-restore)

### <a name="setup-space"></a> "Lemezterületét lemez" üzenet jelenik meg

- **OK**. Ha vissza fájlok akkorról törölni kell a "lemezterületét lemez" üzenet jelenhet meg. Ennek oka lehet a víruskereső szoftver, vagy a fájl nyitva, amikor próbálkozások törlése.

- **Feloldási**. Törölje kézzel a % temp % mappában lévő fájlok szinkronizálása (`del \*sync\* /s`). Törölje az alkönyvtárak a % temp % mappában.

> [!IMPORTANT]
> Ne törölje a fájlokat, amíg a szinkronizálás folyamatban van.

### <a name="setup-delete"></a> A szinkronizálási csoport nem lehet törölni

A szinkronizálási csoport törlése sikertelen. Az alábbi esetekben azt eredményezheti, hogy a szinkronizálási csoport törlése sikertelen:

- **OK**. Az ügyfélügynök offline állapotú.

- **Feloldási**. Győződjön meg arról, hogy az ügyfél ügynökéhez online állapotban, és próbálkozzon újra.

- **OK**. Az ügyfélügynök el lett távolítva vagy hiányzik.

- **Feloldási**. Ha az ügyfélügynök el lett távolítva vagy más okból hiányzik:  
    a. Távolítsa el az ügynök XML-fájlját az SQL Data Sync telepítési mappájából, ha létezik ez a fájl.  
    b. Telepítse az ügynököt a helyszíni számítógépen (ez ugyanaz vagy egy másik számítógép is lehet). Ezután küldje el a portálon létrehozott ügynökkulcsot az offline állapotúként megjelenő ügynöknek.

- **OK**. Egy adatbázis offline állapotban.

- **Feloldási**. Győződjön meg arról, hogy az SQL Database-adatbázisok és az SQL Server-adatbázisok az összes online állapotban.

- **OK**. A szinkronizálási csoport kiépítési vagy szinkronizálása.

- **Feloldási**. Várjon, amíg a kiépítési vagy a szinkronizálási folyamat befejeződik, és ismételje meg a szinkronizálási csoport törlése.

### <a name="setup-unreg"></a> Tudok egy helyszíni SQL Server-adatbázis regisztrációja nem törölhető

- **OK**. Nagy valószínűséggel kívánt regisztrációját egy adatbázist, amelyet már töröltek.

- **Feloldási**. Egy helyszíni SQL Server-adatbázis regisztrációjának törléséhez válassza ki az adatbázist, majd **törlésének kényszerítése**.

  Ha ez a művelet nem tudja törölni az adatbázist a a szinkronizálási csoport:

  1. Állítsa le és indítsa újra az ügyfél ügynök gazdaszolgáltatása:  
    a. Válassza ki a **Start** menü.  
    b. A Keresés mezőbe írja be a **services.msc**.  
    c. Az a **programok** szakasz a keresési eredmények ablaktáblán kattintson duplán a **szolgáltatások**.  
    d. Kattintson a jobb gombbal a **SQL Data Sync** szolgáltatás.  
    e. Ha a szolgáltatás fut, állítsa le.  
    f. Kattintson jobb gombbal a szolgáltatásra, és válassza **Start**.  
    g. Ellenőrizze, hogy az adatbázis továbbra is regisztrálva van. Ha már nincs regisztrálva, akkor végzett. Egyéb esetben folytassa a következő lépéssel.
  1. Nyissa meg az ügynök-ügyfélalkalmazás (SqlAzureDataSyncAgent).
  1. Válassza ki **hitelesítő adatok szerkesztése**, majd adja meg a hitelesítő adatait az adatbázis.
  1. Regisztráció megszüntetésének folytatásához.

### <a name="setup-perms"></a> Nem rendelkezem a megfelelő jogosultsággal a rendszerszolgáltatások indításához

- **OK**. Ez a hiba akkor fordul elő, két helyzetekben:
  -   A felhasználónév és/vagy a jelszó nem megfelelő.
  -   A megadott felhasználói fiók nem rendelkezik elegendő jogosultságokkal a szolgáltatás.

- **Feloldási**. Megadja a naplófájl-a--szolgáltatásként hitelesítő adatokat ahhoz a felhasználói fiókhoz:

  1. Lépjen a **Start** > **vezérlőpultot** > **felügyeleti eszközök** > **helyi biztonsági házirend**  >  **Helyi házirend** > **felhasználói Rights Management**.
  1. Válassza ki **szolgáltatásként jelentkezzen be**.
  1. Az a **tulajdonságok** párbeszédpanelen adjon hozzá a felhasználói fiókot.
  1. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.
  1. Zárjon be minden ablakot.

### <a name="setup-date"></a> Egy adatbázis állapota "Elavult"

- **OK**. SQL Data Sync eltávolítja az adatbázisok offline 45 napon, vagy több (a számít az idő, az adatbázis offline állapotba került a) a szolgáltatásból is. Ha egy adatbázis 45 nap vagy több kapcsolat nélküli üzemmódban, és visszatér online állapotba, állapotú-e **elavult**.

- **Feloldási**. Elkerülheti az **elavult** biztosításával, hogy az adatbázisok egyike kapcsolat nélküli módba 45 nap vagy több állapota.

  Ha egy adatbázis állapota **elavult**:

  1. Távolítsa el az adatbázist, amely rendelkezik egy **elavult** a szinkronizálási csoport állapotát.
  1. Vegye fel az adatbázist újra, és a szinkronizálási csoport.

  > [!WARNING]
  > Ez az adatbázis offline közben végrehajtott valamennyi módosítás elvész.

### <a name="setup-date2"></a> Szinkronizálási csoport tartalmaz egy "Elavult" állapota

- **OK**. Ha egy vagy több módosítás nem sikerül a teljes megőrzési időszak 45 napot a alkalmazni, a szinkronizálási csoport is elavulttá válnak.

- **Feloldási**. Elkerülése érdekében egy **elavult** a szinkronizálási csoport állapotát, vizsgálja meg az eredményeket a szinkronizálási feladatok rendszeresen az előzménymegjelenítőben. Vizsgálja meg, és hárítsa el a módosításokat, amelyek nem érvényesek.

  Ha a szinkronizálási csoport állapota **elavult**a szinkronizálási csoport törlése., majd hozza létre újból.

### <a name="setup-delete2"></a> Szinkronizálási csoport nem lehet törölni, eltávolítása vagy leállítása folyamatban van az ügynök három percen belül

Három eltávolítása vagy leállítása folyamatban van az SQL Data Sync társított ügyfélügynök percen belül szinkronizálási csoport nem törölhető.

- **Feloldási**.

  1. Távolítsa el a szinkronizálási csoport, amíg a kapcsolódó szinkronizálási ügynököket online (javasolt).
  1. Ha az ügynök offline állapotban, de van telepítve, is online állapotba kerüljön a helyi számítógépen. Várjon, amíg jelennek meg az ügynök állapotát **Online** az SQL Data Sync portálon. Ezt követően távolítsa el a szinkronizálási csoport.
  1. Ha az ügynök offline állapotban, mert el lett távolítva:  
    a.  Távolítsa el az ügynök XML-fájlját az SQL Data Sync telepítési mappájából, ha létezik ez a fájl.  
    b.  Telepítse az ügynököt a helyszíni számítógépen (ez ugyanaz vagy egy másik számítógép is lehet). Ezután küldje el a portálon létrehozott ügynökkulcsot az offline állapotúként megjelenő ügynöknek.  
    c. Próbálja ki a szinkronizálási csoport törlése.

### <a name="setup-restore"></a> Mi történik, ha vissza tudok állítani egy elveszett vagy sérült adatbázis?

Elveszett vagy sérült adatbázis egy biztonsági másolatból állítsa vissza, ha van egy nem okozott összetartási az adatok a szinkronizálási csoport, amelyhez tartozik az adatbázis.

## <a name="next-steps"></a>További lépések
Az SQL Data Sync szolgáltatással kapcsolatos további információkért lásd:

-   Áttekintés – [szinkronizálja az adatokat több felhőalapú és helyszíni adatbázis között az Azure SQL Data Sync szolgáltatással](sql-database-sync-data.md)
-   Data Sync beállítása
    - A portálban – [oktatóanyag: A helyszíni adatokat az Azure SQL Database és SQL Server között, az SQL Data Sync beállítása](sql-database-get-started-sql-data-sync.md)
    - A PowerShell-lel
        -  [A PowerShell használata több Azure SQL Database-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [A PowerShell használata egy Azure-beli SQL Database-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Adatok szinkronizálása az ügynök - [adatok szinkronizálása az Azure SQL Data Sync ügynök](sql-database-data-sync-agent.md)
-   Ajánlott eljárások – [ajánlott eljárások az Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   A figyelő - [SQL Data Sync monitorozása az Azure Monitor-naplók](sql-database-sync-monitor-oms.md)
-   Szinkronizálási sémájának frissítéséhez
    -   A Transact-SQL - [az Azure SQL Data Sync sémamódosítások-replikáció automatizálása](sql-database-update-sync-schema.md)
    -   PowerShell-lel – [használja a Powershellt, a meglévő szinkronizálási csoport szinkronizálási sémájának frissítéséhez](scripts/sql-database-sync-update-schema.md)

Az SQL Database kapcsolatos további információkért lásd:

-   [Az SQL Database áttekintése](sql-database-technical-overview.md)
-   [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
