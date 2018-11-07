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
ms.date: 07/16/2018
ms.openlocfilehash: beab191ff33939053da942b0ce7df22238b8acef
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247313"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Az SQL Data Sync szolgáltatással kapcsolatos problémák elhárítása

Ez a cikk ismerteti az Azure SQL Data Sync szolgáltatással kapcsolatos ismert problémák elhárítása. Ha a probléma megoldása, azt itt biztosítja.

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](sql-database-sync-data.md) ismertető cikket.

## <a name="sync-issues"></a>Szinkronizálási hibák

- [A portál felhasználói Felületét a helyszíni adatbázisok az ügyfélügynök társított sikertelen szinkronizálás](#sync-fails)

- [A szinkronizálási csoport Beragadt a feldolgozási állapot](#sync-stuck)

- [Hibás adatok láthatók a az általam használt táblák](#sync-baddata)

- [Elsődleges kulcs inkonzisztens adatok sikeres szinkronizálás után láthatók](#sync-pkdata)

- [A teljesítmény jelentős csökkenéséhez láthatók](#sync-perf)

- [Ez az üzenet látható: "nem lehet beszúrni a NULL érték az az oszlop <column>. Oszlop nem engedélyezi a null értékeket." Ez mit jelent, és hogyan javíthatom azt?](#sync-nulls)

- [Hogyan kezeli a Data Syncet. a körkörös hivatkozások? Azt jelenti, amikor ugyanazokat az adatokat több szinkronizálási csoportban szinkronizálva van, és ennek eredményeképpen tartja módosítása?](#sync-circ)

### <a name="sync-fails"></a> A portál felhasználói Felületét a helyszíni adatbázisok az ügyfélügynök társított sikertelen szinkronizálás

Szinkronizálás a helyszíni adatbázisokhoz társított az ügyfélügynök az SQL Data Sync portal felhasználói felületén sikertelen lesz. A helyi számítógépen, amelyen az ügynök fut System.IO.IOException hibákat az eseménynaplóban talál. A hibák tegyük fel, hogy a lemez nincs elegendő lemezterület.

- **OK**. A meghajtó nincs elegendő lemezterület.

- **Feloldási**. Hozzon létre több helyet az a meghajtó, amelyre a % TEMP % könyvtárban található.

### <a name="sync-stuck"></a> A szinkronizálási csoport Beragadt a feldolgozási állapot

Az SQL Data Sync szinkronizálási csoport lett a feldolgozási állapot a hosszú ideig. Nem válaszol a **leállítása** parancsot, és a naplók megjelenítése nincs új bejegyzés.

Az alábbi feltételek bármelyike azt eredményezheti, hogy a szinkronizálási csoport éppen kerültek a feldolgozási állapot:

- **OK**. Az ügyfélügynök offline állapotban.

- **Feloldási**. Ellenőrizze, hogy az ügyfél ügynökéhez online-e, és próbálkozzon újra.

- **OK**. Az ügyfélügynök telepítve, vagy hiányzik.

- **Feloldási**. Ha az ügyfél ügynöke telepítve vagy egyéb hiányzó:

    1. Ha a fájl létezik, távolítsa el az ügynök XML-fájlt az SQL Data Sync telepítési mappájából.
    1. Telepítse az ügynököt a helyi számítógépen. (Ez ugyanaz vagy egy másik számítógépen is lehet). Ezután küldje el a rendszer által létrehozott a portálon az ügynök offline állapotúként jelenik ügynökkulcs.

- **OK**. Az SQL Data Sync szolgáltatás le van állítva.

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

### <a name="sync-nulls"></a> Ez az üzenet látható: "nem lehet beszúrni a NULL érték az az oszlop <column>. Oszlop nem engedélyezi a null értékeket." Ez mit jelent, és hogyan javíthatom azt? 
Ez a hibaüzenet azt jelzi, hogy a két alábbi problémák egyike történt:
-  Egy táblázat nem rendelkezik elsődleges kulccsal. A probléma megoldásához adja hozzá az elsődleges kulcs szinkronizál az összes táblákhoz való.
-  A CREATE INDEX utasítás WHERE záradék szerepel. Ez az állapot nem kezeli az adatok szinkronizálása. A probléma megoldásához távolítsa el a WHERE záradékban, vagy manuálisan végezze el a módosításokat az összes adatbázishoz. 
 
### <a name="sync-circ"></a> Hogyan kezeli a Data Syncet. a körkörös hivatkozások? Azt jelenti, amikor ugyanazokat az adatokat több szinkronizálási csoportban szinkronizálva van, és ennek eredményeképpen tartja módosítása?
Adatok szinkronizálása. a körkörös hivatkozások nem kezeli. Mindenképpen kerülje azokat. 

## <a name="client-agent-issues"></a>Ügyfél ügynök kapcsolatos problémák

- [Az ügyfél-ügynök telepítése, eltávolítása vagy javítása sikertelen](#agent-install)

- [Az ügyfélügynök megszakítom az Eltávolítás után nem működik.](#agent-uninstall)

- [Az adatbázis nem szerepel a listán az ügynök listában](#agent-list)

- [Ügyfélügynök nem indul el (Hiba 1069)](#agent-start)

- [Az ügynök kulcs nem küldhető be](#agent-key)

- [Az ügyfélügynök nem törölhető a portálról, ha annak társított helyszíni adatbázis nem érhető el](#agent-delete)

- [Szinkronizációs ügynök helyi alkalmazás nem tud kapcsolódni a helyi szinkronizálási szolgáltatás](#agent-connect)

### <a name="agent-install"></a> Az ügyfél-ügynök telepítése, eltávolítása vagy javítása sikertelen

- **OK**. Számos forgatókönyv ezt a hibát okozhatja. Ez a hiba az adott hiba okának megállapításához tekintse meg a naplókat.

- **Feloldási**. Keresse meg a konkrét okát, hozzon létre, és tekintse meg a Windows-telepítési naplókat. Bekapcsolhatja a naplózást, a parancssorba. LocalAgentHost.msi AgentServiceSetup.msi letöltött fájl esetén például hozzon létre, és vizsgálja meg a naplófájlokat a következő parancssorok használatával:

    -   Telepítések: `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
    -   Az eltávolítása: `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

    Windows Installer által végzett összes telepítés naplózását is bekapcsolhatja. A Microsoft Tudásbázis megfelelő cikkében [Windows Installer-naplózás engedélyezése](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) kapcsolja be a naplózást, a Windows Installer egykattintásos megoldást kínál. A naplók helye is tartalmazza.

### <a name="agent-uninstall"></a> Az ügyfélügynök megszakítom az Eltávolítás után nem működik.

Az ügyfélügynök nem működik, akár az eltávolítási megszüntetése után.

- **OK**. Ennek oka az, hogy az SQL Data Sync ügyfélügynök nem tárolja a hitelesítő adatokat.

- **Feloldási**. E két megoldásokkal próbálkozhat:

    -   Írja be újra a hitelesítő adatokat, az ügyfél ügynökének a services.msc használatával.
    -   Távolítsa el az ügyfél ügynökprogramjának, és a egy új telepítését. Töltse le és telepítse a legújabb ügyfél ügynököt [letöltőközpontból](https://go.microsoft.com/fwlink/?linkid=221479).

### <a name="agent-list"></a> Az adatbázis nem szerepel a listán az ügynök listában

Meglévő SQL Server-adatbázis hozzáadása a szinkronizálási csoport kísérli meg, amikor nem ügynökök listája jelenik meg az adatbázis.

Ezekben az esetekben előfordulhat, hogy a probléma:

- **OK**. Az ügyfél-ügynök és a szinkronizálási csoport különböző adatközpontokban találhatók.

- **Feloldási**. Az ügyfél-ügynök és a szinkronizálási csoport ugyanabban az adatközpontban kell lennie. Beállítására, két lehetősége van:

    -   Hozzon létre egy új ügynököt az adatközpont, ahol a szinkronizálási csoport is található. Az ügynök ezután regisztrálja az adatbázisban.
    -   A jelenlegi szinkronizálási csoport törlése. Majd hozza létre a szinkronizálási csoport az adatközpont, ahol az ügynök nem található.

- **OK**. Az ügyfélügynök-adatbázisok listája nem aktuális.

- **Feloldási**. Állítsa le és indítsa újra az ügyfélügynök-szolgáltatás.

    A helyi ügynök letölti az ügynökkulcs első benyújtása csak a társított adatbázisok listája. Nem sikerül letölteni a későbbi ügynök kulcs beküldés a társított adatbázisok listája. Adatbázisok, amelyek regisztrálva vannak az ügynök áthelyezés ideje alatt nem jelenik meg az eredeti példány.

### <a name="agent-start"></a> Ügyfélügynök nem indul el (Hiba 1069)

Tudomására jut, hogy az ügynök nem fut SQL Servert futtató számítógépre. Amikor megpróbálja manuálisan elindítani az ügynököt, megjelenik egy párbeszédpanel, amely a következő üzenetet jeleníti meg "1069. hiba: A szolgáltatás nem indult el a bejelentkezési hiba miatt."

![Szinkronizálási hiba 1069 párbeszédpanel](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **OK**. Egy valószínűleg ez a hiba oka, hogy a jelszót a helyi kiszolgálón módosult-e, mivel az ügynök és az ügynök jelszót hozott létre.

- **Feloldási**. Az ügynök jelszó frissítése a kiszolgáló jelenlegi jelszavát:

  1. Keresse meg az SQL Data Sync ügyfélügynök-szolgáltatás.  
    a. Válassza ki **Start**.  
    b. A Keresés mezőbe írja be a **services.msc**.  
    c. A keresési eredmények között, válassza ki a **szolgáltatások**.  
    d. Az a **szolgáltatások** ablakban görgessen a bejegyzés **SQL Data Sync-ügynök**.  
  1. Kattintson a jobb gombbal **SQL Data Sync-ügynök**, majd válassza ki **leállítása**.
  1. Kattintson a jobb gombbal **SQL Data Sync-ügynök**, majd válassza ki **tulajdonságok**.
  1. A **SQL Data Sync ügynök tulajdonságainak**, jelölje be a **bejelentkezés** fülre.
  1. Az a **jelszó** mezőbe írja be a jelszót.
  1. Az a **jelszó megerősítése** mezőbe írja be újból a jelszót.
  1. Válassza az **Apply** (Alkalmaz) lehetőséget, majd kattintson az **OK** gombra.
  1. Az a **szolgáltatások** ablakban kattintson a jobb gombbal a **SQL Data Sync-ügynök** szolgáltatásra, és kattintson a **Start**.
  1. Zárja be a **szolgáltatások** ablak.

### <a name="agent-key"></a> Az ügynök kulcs nem küldhető be

Miután hoz létre, vagy hozza létre a kulcsot egy ügynöknek, küldje el a kulcs az SqlAzureDataSyncAgent alkalmazáson keresztül próbál. A küldés nem lehetett végrehajtani.

![Hiba-párbeszédpanel szinkronizálása – nem küldhetnek ügynökkulcs](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Előfeltételek**. Mielőtt folytatná, ellenőrizze a következő előfeltételek vonatkoznak:

  - Az SQL Data Sync Windows-szolgáltatás fut.

  - A szolgáltatás az SQL Data Sync Windows-szolgáltatásfiókot hálózati hozzáféréssel rendelkezik.

  - A kimenő 1433-as port meg nyitva, a helyi tűzfalszabály.

  - A helyi IP-cím hozzáadódik a kiszolgáló vagy adatbázis tűzfalszabályt a szinkronizálási metaadat-adatbázisként.

- **OK**. Az ügynökkulcs egyedileg azonosítja az egyes helyi ügynök. A kulcs két feltételeknek kell megfelelniük:

  -   Az SQL Data Sync-kiszolgáló és a helyi számítógépen az ügynök ügyfélkulcsot azonosnak kell lennie.
  -   Az ügyfél ügynökkulcs csak egyszer használható.

- **Feloldási**. Az ügynök nem működik, ha, mert az egyik vagy mindkét ezek a feltételek nem teljesülnek. Az ügynök újra működéséhez lekérése:

  1. Hozzon létre egy új kulcsot.
  1. Az ügynök az új kulcs érvényesek.

  A alkalmazni az ügynököt az új kulcs:

  1. A Fájlkezelőben nyissa meg az ügynöktelepítési könyvtár. Az alapértelmezett telepítési könyvtárra a C:\\Program Files (x86)\\Microsoft SQL Data Sync szolgáltatással.
  1. Kattintson duplán a bin alkönyvtárat.
  1. Nyissa meg a SqlAzureDataSyncAgent alkalmazást.
  1. Válassza ki **Ügynökkulcs elküldése**.
  1. A rendelkezésre álló illessze be a vágólapra másolt kulcsot.
  1. Kattintson az **OK** gombra.
  1. Zárja be a program.

### <a name="agent-delete"></a> Az ügyfélügynök nem törölhető a portálról, ha annak társított helyszíni adatbázis nem érhető el

Ha a helyi végpont (azaz egy adatbázis), amely regisztrálva van egy SQL Data Sync ügyfélügynök elérhetetlenné válik, az ügyfél ügynöke nem lehet törölni.

- **OK**. A helyi ügynök nem törölhető, mert az nem érhető el adatbázis továbbra is regisztrálva van az ügynök. Ha törli az ügynököt próbál, a törlési folyamat megpróbálja elérni az adatbázist, amelyet nem sikerül.

- **Feloldási**. Használja a "kényszerítéséhez delete" törli nem érhető el az adatbázist.

> [!NOTE]
> Ha a szinkronizálási metaadat-táblát egy "kényszerítéséhez delete" után továbbra is használható `deprovisioningutil.exe` karbantartása őket.

### <a name="agent-connect"></a> Szinkronizációs ügynök helyi alkalmazás nem tud kapcsolódni a helyi szinkronizálási szolgáltatás

- **Feloldási**. Próbálja meg a következőket:

  1. Lépjen ki az alkalmazást.  
  1. Az összetevő-szolgáltatások Panel megnyitásához.  
    a. A tálcán a Keresés mezőbe írja be **services.msc**.  
    b. A keresési eredmények között kattintson duplán a **szolgáltatások**.  
  1. Állítsa le a **SQL Data Sync** szolgáltatás.
  1. Indítsa újra a **SQL Data Sync** szolgáltatás.  
  1. újra meg kell nyitniuk az alkalmazást;

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

- **OK**. Az ügyfélügynök offline állapotban.

- **Feloldási**. Győződjön meg arról, hogy az ügyfél ügynökéhez online állapotban, és próbálkozzon újra.

- **OK**. Az ügyfélügynök telepítve, vagy hiányzik.

- **Feloldási**. Ha az ügyfél ügynöke telepítve vagy egyéb hiányzó:  
    a. Ha a fájl létezik, távolítsa el az ügynök XML-fájlt az SQL Data Sync telepítési mappájából.  
    b. Telepítse az ügynököt a helyi számítógépen. (Ez ugyanaz vagy egy másik számítógépen is lehet). Ezután küldje el a rendszer által létrehozott a portálon az ügynök offline állapotúként jelenik ügynökkulcs.

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
    a.  Ha a fájl létezik, távolítsa el az ügynök XML-fájlt az SQL Data Sync telepítési mappájából.  
    b.  Telepítse az ügynököt a helyi számítógépen. (Ez ugyanaz vagy egy másik számítógépen is lehet). Ezután küldje el a rendszer által létrehozott a portálon az ügynök offline állapotúként jelenik ügynökkulcs.  
    c. Próbálja ki a szinkronizálási csoport törlése.

### <a name="setup-restore"></a> Mi történik, ha vissza tudok állítani egy elveszett vagy sérült adatbázis?

Elveszett vagy sérült adatbázis egy biztonsági másolatból állítsa vissza, ha van egy nem okozott összetartási az adatok a szinkronizálási csoport, amelyhez tartozik az adatbázis.

## <a name="next-steps"></a>További lépések
Az SQL Data Sync szolgáltatással kapcsolatos további információkért lásd:

-   [Több felhőalapú és helyszíni adatbázis közötti adatszinkronizálás az Azure SQL Data Synckel](sql-database-sync-data.md)  
-   [Az Azure SQL Data Sync beállítása](sql-database-get-started-sql-data-sync.md)  
-   [Ajánlott eljárások az Azure SQL Data Synchez](sql-database-best-practices-data-sync.md)  
-   [Az Azure SQL Data Sync monitorozása a Log Analytics használatával](sql-database-sync-monitor-oms.md)  
-   Teljes PowerShell-példák az SQL Data Sync konfigurálásáról:  
    -   [A PowerShell használata több Azure SQL Database-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [A PowerShell használata egy Azure-beli SQL Database-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)  

Az SQL Database kapcsolatos további információkért lásd:

-   [Az SQL Database áttekintése](sql-database-technical-overview.md)
-   [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
