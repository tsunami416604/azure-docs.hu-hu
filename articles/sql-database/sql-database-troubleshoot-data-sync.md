---
title: "Az Azure SQL adatszinkronizálás hibaelhárítása |} Microsoft Docs"
description: "További tudnivalók az Azure SQL adatszinkronizálás kapcsolatos gyakori hibák elhárítása"
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: bbfcac5a54f04f20dbdeeecef7c06b91128b8c6a
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2017
---
# <a name="troubleshoot-issues-with-azure-sql-data-sync-preview"></a>Problémák az Azure SQL adatszinkronizálás (előzetes verzió)

Ez a cikk ismerteti, amelyről ismert, hogy az SQL adatszinkronizálás (előzetes verzió) csoport aktuális kapcsolatos problémák elhárítása. Ha egy problémát a megoldás, azt itt biztosítja.

SQL adatszinkronizálás áttekintését lásd: [adatok szinkronizálásának több felhőalapú és helyszíni adatbázisokat az Azure SQL adatszinkronizálás (előzetes verzió)](sql-database-sync-data.md).
                                                           
## <a name="my-client-agent-doesnt-work"></a>Az ügyfélügynök nem működik

### <a name="description-and-symptoms"></a>Leírás és jelenségek

Az ügyfélügynök használatára tett kísérlet során a következő hibaüzeneteket kap.

"A szinkronizálási hiba történt a paraméter www.microsoft.com/.../05:GetBatchInfoResult deszerializálása során kivétel miatt sikertelen. További részleteket lásd a belső kivétel leírásában olvasható.

"Belső kivételre vonatkozó üzenet:"Microsoft.Synchronization.ChangeBatch"típus érvénytelen gyűjteménytípus, mert nincs alapértelmezett konstruktora."


### <a name="cause"></a>Ok

Ezt a hibát a probléma az SQL-adatok szinkronizálással (előzetes verzió).

A probléma legvalószínűbb oka:

-   Windows 8 Developer Preview verzióját futtatja, vagy

-   .NET 4.5 telepítve van.

### <a name="solution-or-workaround"></a>Megoldás vagy megoldás

Győződjön meg arról, hogy az ügyfélügynök számítógépre telepít egy, hogy nem fut a Windows 8 Developer Preview verzióját, és, hogy a .NET-keretrendszer 4.5 nincs telepítve.

## <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>Az ügyfélügynök I szakítsa meg az Eltávolítás után nem működik.

### <a name="description-and-symptoms"></a>Leírás és jelenségek

Az ügyfélügynök nem működik, annak ellenére, hogy az Eltávolítás megszakította.

### <a name="cause"></a>Ok

A hiba oka, hogy SQL adatszinkronizálás (előzetes verzió) ügyfélügynökének nem tárolja a hitelesítő adatokat.

### <a name="solution-or-workaround"></a>Megoldás vagy megoldás

Nincsenek két megoldást:

-   Először a "Services.msc" parancs használatával adja meg újra a hitelesítő adatait, az ügyfél ügynökének.

-   Ezután távolítsa el az ügyfél ügynökprogramjának, és egy új telepítése. Töltse le és telepítse a legújabb ügyfélügynök a [letöltőközpontból](http://go.microsoft.com/fwlink/?linkid=221479).

## <a name="my-database-isnt-listed-in-the-agent-dropdown"></a>Az adatbázis nem szerepel a listában az ügynök a legördülő

### <a name="description-and-symptoms"></a>Leírás és jelenségek

Amikor kísérel meg hozzáadni egy meglévő SQL Server-adatbázis szinkronizálási csoporthoz, az adatbázis nem szerepel a legördülő listában.

### <a name="cause"></a>Ok

A probléma több lehetséges oka is van:

-   Az ügynök és a szinkronizálási ügyfélcsoport erőforrások más adatok.

-   Az adatbázisok listája a ügyfél ügynöke nincs aktuális.

### <a name="solution"></a>Megoldás

A megoldás a OK függ.

#### <a name="the-client-agent-and-sync-group-are-in-different-data-centers"></a>Az ügynök és a szinkronizálási ügyfélcsoport más adatok központok

Az ügyfélügynök, mind a szinkronizálási csoport ugyanabban az adatközpontban kell rendelkeznie. Ez a konfiguráció állíthat be a következők valamelyikét:

-   Hozzon létre egy új ügynök a szinkronizálás csoportként azonos adatközpontba. Regisztrálja az adatbázis az adott ügynökhöz. Lásd: [Útmutató: ügyfél SQL adatszinkronizálás (előzetes verzió) ügynök telepítése](#install-a-sql-data-sync-client-agent) további információt.

-   A jelenlegi szinkronizálási csoport törlése. Hozza létre újra a az ügynök azonos adatközpontba.

#### <a name="the-client-agents-list-of-databases-is-not-current"></a>Az adatbázisok listája a ügyfél ügynöke nincs aktuális

Állítsa le és indítsa újra az ügyfélügynök-szolgáltatás.
A helyi ügynök letölti a társított adatbázisokban csak a az ügynök kulcs első küldése, nem a következő ügynök kulcs jelentések listáját. Így egy ügynök áthelyezés során regisztrálása adatbázisok nem jelennek meg az eredeti ügynök-példányon.

## <a name="client-agent-doesnt-start-error-1069"></a>Ügyfélügynök nem indul el (Hiba 1069)

### <a name="description-and-symptoms"></a>Leírás és jelenségek

Azt tapasztalja, hogy a az ügynök nem fut az SQL-kiszolgálót futtató számítógépen. Ha megpróbálja manuálisan indítsa el az ügynököt, a következő hibaüzenettel hiba-párbeszédpanel kapott "1069 hiba: A szolgáltatás nem indult el a bejelentkezési hiba miatt."

![Szinkronizálási hiba 1069 párbeszédpanel](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

### <a name="cause"></a>Ok

A hiba legvalószínűbb oka az, hogy a jelszót a helyi kiszolgálón megváltozott az ügynök létrehozott és rendelte hozzá bejelentkezési jelszót.

### <a name="solution-or-workaround"></a>Megoldás vagy megoldás

Frissítse az ügynök jelszó a kiszolgáló jelenlegi jelszavát.

1. Keresse meg az SQL adatszinkronizálás (előzetes verzió) ügyfélügynök előzetes szolgáltatás.

    a. Kattintson a **Start**.

    b. A keresőmezőbe írja be a "Services.msc parancsot".

    c. A keresési eredmények között kattintson a "Szolgáltatások".

    d. Az a **szolgáltatások** ablakban, keresse meg a bejegyzés **SQL adatszinkronizálás (előzetes verzió) ügynök előzetes**.

2. Kattintson a jobb gombbal az bejegyzést, és válassza ki **leállítása**.

3. Kattintson a jobb gombbal az bejegyzést, és kattintson a **tulajdonságok**.

4. Az a **SQL adatszinkronizálás (előzetes verzió) Preview tulajdonságai** ablak, kattintson a **jelentkezzen be** fülre.

5. Adja meg a jelszavát a jelszó szövegmezőjét.

6. A jelszót a jelszó megerősítése szövegmezőben.

7. Kattintson az **Apply** (Alkalmaz), majd az **OK** gombra.

8. Az a **szolgáltatások** ablak, kattintson a jobb gombbal a **SQL adatszinkronizálás (előzetes verzió) ügynök Preview** szolgáltatást, majd kattintson az **Start**.

9. Zárja be a **szolgáltatások** ablak.

## <a name="i-get-a-disk-out-of-space-message"></a>"Nincs elegendő lemezterület lemez" üzenet jelenik meg

### <a name="cause"></a>Ok

A "lemez szabad terület" üzenet is megjelenhet, ha törölni kell a fájlok továbbra is mögött. Ez az állapot a víruskereső szoftver miatt fordulhat elő, vagy mert fájlok megnyitása a delete művelet során.

### <a name="solution"></a>Megoldás

A megoldás, hogy törölje kézzel a fájlszinkronizálás alatt `%temp%` (`del \*sync\* /s`), és távolítsa el a alkönyvtárai is.

> [!IMPORTANT]
> Várjon, amíg a szinkronizálás befejeződése előtt törölje a fájlokat.

## <a name="i-cannot-delete-my-sync-group"></a>Nem lehet törölni a Szinkronizáló csoport

### <a name="description-and-symptoms"></a>Leírás és jelenségek

A szinkronizálás csoport törlése a kísérlet sikertelen lesz.

### <a name="causes"></a>Okok

Az alábbiak egyikét sem szinkronizálási csoport törlése hibát eredményezhet.

-   Az ügyfélügynök offline állapotban.

-   Az ügyfélügynök telepítve, vagy hiányzik. 

-   Egy adatbázis offline állapotban. 

-   Kiépítés vagy szinkronizálása a szinkronizálási csoportot. 

### <a name="solutions"></a>Megoldások

Szinkronizálási csoport törlése a hiba elhárítása érdekében ellenőrizze a következőket:

-   Ellenőrizze, hogy az ügyfélügynök online-e, majd próbálkozzon újra.

-   Ha az ügyfél-ügynök telepítve vagy egyéb hiányzik:

    a. Távolítsa el ügynök XML-fájlt az SQL adatszinkronizálás (előzetes verzió) telepítési mappájából, ha a fájl létezik-e.

    b. Helyszíni ugyanazon vagy egy másik számítógépre telepítse az ügynököt, küldje el az ügynök kulcs jön létre, amely offline azt az ügynök a portálról.

-   **Az SQL adatszinkronizálás (előzetes verzió) szolgáltatás leáll.**

    a. Az a **Start** menü, keresse meg a szolgáltatások.

    b. A keresési eredmények között kattintson a szolgáltatások elemre.

    c. Keresés a **SQL adatszinkronizálás (előzetes verzió) Preview** szolgáltatás.

    d. Ha a szolgáltatás állapota **leállítva**, kattintson a jobb gombbal a szolgáltatás nevét, és válassza ki **Start** a legördülő menüből.

-   Ellenőrizze az SQL-adatbázisokat és SQL Server adatbázisok és ellenőrizze, hogy mind online állapotban.

-   Várjon, amíg a kiépítési vagy a szinkronizálási folyamat befejeződik. Ismételje meg a szinkronizálási csoport törlése.

## <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-associated-with-the-client-agent"></a>Az ügyfélügynök társított a portál felhasználói felületének helyszíni adatbázisok szinkronizálása sikertelen

### <a name="description-and-symptoms"></a>Leírás és jelenségek

Az SQL adatszinkronizálás (előzetes verzió) portál felhasználói felületének helyszíni adatbázisokhoz szinkronizálás meghiúsul az ügynökhöz rendelt. A helyi rendszert futtató számítógépen az ügynök az eseménynaplóban, amely meghatározza, hogy, hogy rendelkezik-e elegendő hely a lemezen System.IO.IOException hibába ütközik.

### <a name="solution-or-workaround"></a>Megoldás vagy megoldás

Hozzon létre nagyobb területet a meghajtón, amelyen a % TEMP % könyvtárban található.

## <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>A helyszíni SQL Server adatbázis I nem törölhető

### <a name="cause"></a>Ok

Valószínűleg kívánt regisztrációjának törlése egy adatbázist, amelyet már töröltek.

### <a name="solution-or-workaround"></a>Megoldás vagy megoldás

A helyszíni SQL Server adatbázis regisztrációjának törléséhez válassza ki az adatbázist, majd kattintson **kényszerített törlése**.

Ha ez a művelet nem sikerült távolítsa el az adatbázist a szinkronizálási csoportból, tegye a következőket:

1. Állítsa le és indítsa újra az ügyfél ügynök gazdaszolgáltatása.

    a. Kattintson a Start menüből.

    b. Adja meg *services.msc* be a keresőmezőbe.

    c. A Programok telepítése és szakasza az eredménypanelen kattintson duplán a **szolgáltatások**.

    d. Keresse meg és kattintson a jobb gombbal a szolgáltatás **SQL adatszinkronizálás (előzetes verzió)**.

    e. Ha a szolgáltatás fut, állítsa le.

    f. Kattintson a jobb gombbal, és válassza ki **Start**.

    g. Ellenőrizze, hogy az adatbázis már nincs regisztrálva. Ha már nincs regisztrálva, az elkészült. Ellenkező esetben folytassa a következő lépéssel.

2. Nyissa meg az ügynök-ügyfélalkalmazás (SqlAzureDataSyncAgent).

3. Kattintson a **hitelesítő adatok szerkesztése** és, hogy elérhető-e, adja meg az adatbázis hitelesítő adatait.

4. Regisztráció megszüntetése folytatásához.

## <a name="i-cannot-submit-the-agent-key"></a>Nem küldhető be az ügynök kulcs

### <a name="description-and-symptoms"></a>Leírás és jelenségek

Miután hoz létre, vagy hozza létre újból a kulcsot az ügynökök, küldje el a SqlAzureDataSyncAgent alkalmazáson keresztül kulcs próbál, de a küldése nem teljes.

![Hiba-párbeszédpanel szinkronizálása - ügynök kulcs nem tudja elküldeni](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

A folytatás előtt ellenőrizze, hogy az alábbi feltételek közül az egyik hibát nincs a probléma okát.

-   Az SQL adatszinkronizálás (előzetes verzió) Windows-szolgáltatás fut.

-   A szolgáltatási fiók SQL adatszinkronizálás (előzetes verzió) Preview Windows-szolgáltatás hálózati hozzáfér.

-   Az ügyfél ügynök nem képes a lokátor szolgáltatással való kapcsolatfelvételre. Ellenőrizze, hogy a következő beállításkulcs értéke a "https://locator.sync.azure.com/LocatorServiceApi.svc"

    -   Egy x86 a számítógép:`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

    -   Egy x64 a számítógép:`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

### <a name="cause"></a>Ok

Az ügynök kulcs egyedileg azonosítja az egyes helyi ügynök. A kulcs működéshez két feltételeknek kell megfelelniük:

-   A SQL adatszinkronizálás (előzetes verzió) kiszolgáló és a helyi számítógép ügynök ügyfélkulcsot azonosnak kell lennie.

-   Az ügynök ügyfélkulcsot csak egyszer használható.

### <a name="solution-or-workaround"></a>Megoldás vagy megoldás

Az ügynök nem működik, ha van, mert az egyik vagy mindkét ezek a feltételek nem teljesülnek. Az ügynököt újra beolvasása:

1. Hozzon létre egy új kulcsot.

2. Az ügynök az új kulcsot alkalmazni.

Az ügynök alkalmazza az új kulccsal, tegye a következőket:

1. A fájl Intézőben keresse meg az ügynök telepítési könyvtárát. Az alapértelmezett telepítési könyvtár `c:\\program files (x86)\\microsoft sql data sync`.

2. Kattintson duplán a `bin` alkönyvtár.

3. Indítsa el a `SqlAzureDataSyncAgent` alkalmazás.

4. Kattintson a **küldje el az ügynököt kulcs**.

5. A kulcs beillesztése a vágólapról illessze be a rendelkezésre álló helyen.

6. Kattintson az **OK** gombra.

7. Zárja be a program.

## <a name="i-do-not-have-sufficient-privileges-to-start-system-services"></a>Nincs elegendő jogosultsággal a rendszerszolgáltatások indításához

### <a name="cause"></a>Ok

Két esetben ez a hiba akkor fordul elő:

-   A felhasználónév és/vagy a jelszó nem megfelelő.

-   A megadott felhasználói fiók nem rendelkezik elegendő jogosultságokkal a szolgáltatás.

### <a name="solution-or-workaround"></a>Megoldás vagy megoldás

Adja meg a napló---a-szolgáltatásként hitelesítő adatok a felhasználói fiókhoz.

1. Navigáljon a **Start |} Vezérlőpult |} Felügyeleti eszközök |}  Helyi biztonsági házirend |} Helyi házirend |} Felhasználó tartalomvédelem**.

2. Keresse meg és kattintson a **jelentkezzen be a szolgáltatás** bejegyzés.

3. A felhasználói fiók hozzáadása a **jelentkezzen be a szolgáltatás Tulajdonságok** párbeszédpanel.

4. Kattintson a **alkalmazása** majd **OK**.

5. Zárja be az ablakokat.

## <a name="local-sync-agent-app-is-unable-to-connect-to-the-local-sync-service"></a>Helyi szinkronizálási ügynök alkalmazás nem tud kapcsolódni a helyi szinkronizálási szolgáltatás

### <a name="solution-or-workaround"></a>Megoldás vagy megoldás

Próbálja meg a következőket:

1. Bezárhatja az alkalmazást.

2. Az összetevő-szolgáltatások Panel megnyitásához.

    a. A tálcán a keresési mezőbe írja be a "Services.msc" parancsot.

    b. A keresési eredmények között kattintson duplán a "Szolgáltatások".

3. Állítsa le és indítsa újra az "SQL adatok szinkronizálása (előzetes verzió) előzetese" szolgáltatás.

4. Indítsa újra az alkalmazást.

## <a name="install-uninstall-or-repair-fails"></a>Telepítése, eltávolítása vagy javítása sikertelen

### <a name="cause"></a>Ok

A hiba több lehetséges oka is van. E hiba a konkrét hiba okának megállapításához tekintse meg a naplók kell.

### <a name="solution-or-workaround"></a>Megoldás vagy megoldás

A hibát tapasztal az adott ok megkereséséhez szüksége hozza létre, és a Windows Installer talál. Ha bekapcsolja a naplózást a parancssorból. Tegyük fel például, hogy a letöltött AgentServiceSetup.msi fájl LocalAgentHost.msi. Létrehozni, és vizsgálja meg a naplófájlokat a következő parancsokat:

-   Telepítések:`msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`

-   Az eltávolítása:`msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

Windows Installer által végzett összes telepítés naplózását is engedélyezheti. A Microsoft Tudásbázis [Windows Installer naplózásának engedélyezéséről](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) bekapcsolja a naplózást, a Windows Installer egy kattintással megoldást biztosít. Ezek a naplók helye is tartalmazza.

## <a name="a-database-has-an-out-of-date-status"></a>Egy adatbázis állapota "Elavult"

### <a name="cause"></a>Ok

SQL adatszinkronizálás (előzetes verzió), amely rendelkezik, a rendszer kapcsolat nélküli 45 nap vagy több (a számítanak a óta, az adatbázis offline állapotba került) adatbázisok eltávolítja a szolgáltatásból. Ha egy adatbázis 45 nap vagy több offline állapotban, és ezután a ismét online elérhető lesz, a beállítás "Elavult".

### <a name="solution-or-workaround"></a>Megoldás vagy megoldás

Annak biztosítása, hogy az adatbázis egyik nyissa meg offline állapotú 45 nap vagy több elkerülhetők a "Elavult" állapotú.

Ha egy adatbázis állapota "Elavult", végre kell hajtani a következőket:

1. Távolítsa el a "Elavult" adatbázis a szinkronizálási csoportból.

2. Az adatbázis hozzáadása a szinkronizálási csoporthoz újból.

> [!WARNING]
> Miközben offline állapotban volt ehhez az adatbázishoz való végrehajtott valamennyi módosítást elvesznek.

## <a name="a-sync-group-has-an-out-of-date-status"></a>A szinkronizálás csoport állapota "Elavult"

### <a name="cause"></a>Ok

Ha egy vagy több módosítások nem alkalmazza a teljes megőrzési időszak 45 napot, a szinkronizálási csoport is elavulttá válnak.

### <a name="solution-or-workaround"></a>Megoldás vagy megoldás

A "Elavult" állapotú elkerülése érdekében ellenőrizze az eredményt a szinkronizálási feladatok az előzménymegjelenítőben, rendszeres időközönként, és vizsgálja meg, és hárítsa el a módosításokat alkalmazni.

Ha a szinkronizálás csoport állapota "Elavult" kell törölni a Szinkronizáló csoportot, és hozza létre újra.

## <a name="i-see-erroneous-data-in-my-tables"></a>A táblázatban látható hibás adatot

### <a name="description-and-symptoms"></a>Leírás és jelenségek

Ha azonos nevű, de az adatbázis különböző sémák táblák szinkronban van szó, lásd: a táblázatokban hibás adatot szinkronizálás után.

### <a name="cause-and-fix"></a>OK és a javítás

Létesítésének folyamatát kell használnia SQL adatszinkronizálás (előzetes verzió) ugyanazzal a névvel, de eltérő sémákban táblák a azonos nyomkövetési táblát használ. Ennek eredményeképpen a táblákban módosítások megjelennek az azonos nyomkövetési táblát, és ez a viselkedés, ami hibás adatokat vált szinkronizálás során.

### <a name="resolution-or-workaround"></a>Megoldás vagy megoldás

Győződjön meg arról, hogy részt szinkronizálási táblák nevei eltérőek, még akkor is, ha különböző sémák tartoznak.

## <a name="i-see-inconsistent-primary-key-data-after-a-successful-synchronization"></a>Inkonzisztens elsődleges kulcs adatok látom sikeres szinkronizálás után

### <a name="description-and-symptoms"></a>Leírás és jelenségek

A szinkronizálás után, amely az elvártnak megfelelően sikeres, és a napló megjelenítése sikertelen, vagy kihagyott sorok, azt láthatja, hogy elsődleges kulcs adatok inkonzisztens a szinkronizálási csoportban található adatbázisok között.

### <a name="cause"></a>Ok

Ez a működésmód szándékos. Bármely elsődleges kulcsként megadott oszlop eredménye a sorok, ahol az elsődleges kulcs megváltozott inkonzisztens adatok változásairól.

### <a name="resolution-or-workaround"></a>Megoldás vagy megoldás

A probléma megelőzése érdekében győződjön meg arról, hogy megváltozott-e az elsődleges kulcsként megadott oszlop nincsenek adatok.

A probléma kijavítása után azt, el kell dobnia az érintett sor a a szinkronizálási csoportban található összes végpontok, majd helyezze a sort.

## <a name="i-see-a-significant-degradation-in-performance"></a>A jelentős akár teljesítménycsökkenés látható

### <a name="description-and-symptoms"></a>Leírás és jelenségek

A teljesítmény csökkenti jelentősen, valószínűleg a pont, ahol még nem tudja elindítani az adatok szinkronizálása felhasználói felületén.

### <a name="cause"></a>Ok

A legvalószínűbb ok az szinkronizálási ciklust. Szinkronizálási ciklust következik be, amikor egy szinkronizálása szinkronizálás szinkronizálási csoport B, a szinkronizálás A csoport eseményindítók által ezután elindítja a szinkronizálási csoport A. által a szinkronizálás Lehet, hogy a tényleges helyzet összetettebb, az ismétlődő kettőnél több szinkronizálási csoportok használata esetén azonban a lényeges szempontja, hogy nincs átfedésben lévő, egy másik szinkronizálási csoportok által okozott szinkronizálások körkörös váltanak.

### <a name="resolution-or-workaround"></a>Megoldás vagy megoldás

A legjobb javítás megelőzési. Győződjön meg arról, hogy nem kell a körkörös hivatkozás a szinkronizálási csoportok. Minden sor egy szinkronizálási csoport szinkronizált egy másik szinkronizálási csoportonként nem lehet szinkronizálni.

## <a name="client-agent-cannot-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>Ügyfélügynök nem törölhető a portálról, ha a kapcsolódó helyszíni adatbázis nem érhető el.

### <a name="description-and-symptoms"></a>Leírás és jelenségek

Ha a helyi végpont (Ez azt jelenti, hogy egy adatbázis), amely regisztrálva van az ügyféloldali ügynök SQL adatszinkronizálás (előzetes verzió) érhető el, az ügyfél ügynökéhez nem lehet törölni.

### <a name="cause"></a>Ok

A helyi ügynök nem törölhető, mert az nem érhető el adatbázis továbbra is regisztrálva van az ügynök. Az ügynök törlését kísérli meg, a törlési folyamat megpróbálja elérni az adatbázist, amely nem sikerül.

### <a name="resolution-or-workaround"></a>Megoldás vagy megoldás

Használja a "Törlés force" törli a nem érhető el az adatbázist.

> [!NOTE]
> Ha után "kényszerített törlés" sync metaadat-táblát, továbbra is használja deprovisioningutil.exe törölnie őket.

## <a name="a-sync-group-cannot-be-deleted-within-three-minutes-of-uninstallingstopping-the-agent"></a>A szinkronizálási csoportot nem lehet törölni az ügynök eltávolítása/leállítása három percen belül

### <a name="description-and-symptoms"></a>Leírás és jelenségek

Nem tudunk a társított SQL adatszinkronizálás (előzetes verzió) ügyfélügynökének eltávolítása/leállítása három percen belül szinkronizálási csoport törlése.

### <a name="resolution-or-workaround"></a>Megoldás vagy megoldás

1. Távolítsa el a szinkronizálási csoportot, amíg kapcsolódik az internethez kapcsolódó szinkronizálási ügynökök (ajánlott).

2. Ha az ügynök nélküli, de a telepített, is online állapotba kerüljön a helyi számítógépen. Majd várja meg az online SQL adatszinkronizálás (előzetes verzió) portálon megjelenik az ügynök állapotát, és távolítsa el a szinkronizálási csoportot.

3. Ha az ügynök offline állapotban, mert el lett távolítva, tegye a következőket. Próbálja meg a szinkronizálási csoport törlése.

    a.  Távolítsa el az ügynök XML-fájlt az SQL adatszinkronizálás (előzetes verzió) telepítési mappájából, ha a fájl létezik-e.

    b.  Telepítse az ügynököt az azonos, vagy egy másik a helyszíni számítógépek, küldje el az ügynök kulcs jön létre, amely offline azt az ügynök a portálról.

## <a name="my-sync-group-is-stuck-in-the-processing-state"></a>A szinkronizálás csoportomnak Beragadt feldolgozási állapotát

### <a name="description-and-symptoms"></a>Leírás és jelenségek

A szinkronizálási csoportot SQL adatszinkronizálás (előzetes verzió) már a feldolgozási állapotát hosszú időn, nem válaszol a leállítási parancsot, és a naplók nem új bejegyzés megjelenítése.

### <a name="causes"></a>Okok

A következő feltételek eredményezhet a szinkronizálási csoport alatt Beragadt feldolgozási állapotát.

-   **Az ügyfélügynök offline állapotban.** Ellenőrizze, hogy az ügyfélügynök online-e, majd próbálkozzon újra.

-   **Az ügyfélügynök telepítve, vagy hiányzik.** Ha az ügyfél-ügynök telepítve vagy egyéb hiányzik:

    1. Távolítsa el ügynök XML-fájlt az SQL adatszinkronizálás (előzetes verzió) telepítési mappájából, ha a fájl létezik-e.

    2. Telepítse az ügynököt a helyszíni ugyanazon vagy egy másik számítógépen. Az ügynök-kulcsot a portálon jön létre az ügynök, amely offline állapotúként jelenik majd elküldeni.

-   **Az SQL adatszinkronizálás (előzetes verzió) szolgáltatás leáll.**

    1. Az a **Start** menü, keresse meg a szolgáltatások.

    2. A keresési eredmények között kattintson a szolgáltatások elemre.

    3. Keresés a **SQL adatszinkronizálás (előzetes verzió)** szolgáltatás.

    4. Ha a szolgáltatás állapota **leállítva**, kattintson a jobb gombbal a szolgáltatás nevét, és válassza ki **Start** a legördülő menüből.

### <a name="solution-or-workaround"></a>Megoldás vagy megoldás

Ha nem tudja kijavítani a problémát, a szinkronizálási csoport állapotát visszaállítható által a Microsoft támogatási szolgálatához. Ahhoz, hogy az állapot alaphelyzetbe állítása, hozzon létre egy fórumbejegyzést a rendelkezik a [Azure SQL Database fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), és az előfizetés-Azonosítóval és a szinkronizálás csoport azonosítója, amely alaphelyzetbe kell állítani a csoport. A Microsoft terméktámogatási szolgálatának a POST válaszol, és jelzi, ha az állapot alaphelyzetbe lett állítva.

## <a name="next-steps"></a>Következő lépések
SQL adatszinkronizálás kapcsolatos további információkért lásd:

-   [Szinkronizálja az adatokat több felhőalapú és helyszíni adatbázisokat az Azure SQL adatszinkronizálás](sql-database-sync-data.md)
-   [Ismerkedés az Azure SQL-adatok szinkronizálása](sql-database-get-started-sql-data-sync.md)
-   [Ajánlott eljárások az Azure SQL-adatok szinkronizálása](sql-database-best-practices-data-sync.md)

-   PowerShell-példák bemutatják, hogyan konfigurálja az SQL adatszinkronizálás befejezése:
    -   [A PowerShell szolgáltatás használatával több Azure SQL-adatbázisok közötti szinkronizálása](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Egy Azure SQL-adatbázis és a helyszíni SQL Server-adatbázisok közötti szinkronizálása a PowerShell használatával](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Töltse le az SQL Data szinkronizálási REST API dokumentációja](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

SQL-adatbázis kapcsolatos további információkért lásd:

-   [SQL-adatbázis – áttekintés](sql-database-technical-overview.md)
-   [Adatbázis életciklusának kezelésére](https://msdn.microsoft.com/library/jj907294.aspx)
