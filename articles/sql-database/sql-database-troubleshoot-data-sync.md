---
title: "Az Azure SQL adatszinkronizálás (előzetes verzió) hibaelhárítása |} Microsoft Docs"
description: "További tudnivalók az Azure SQL adatszinkronizálás (előzetes verzió) kapcsolatos gyakori hibák elhárítása."
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 50cabbaa584671e52c1ea7efbd2ad990b8438272
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2017
---
# <a name="troubleshoot-issues-with-sql-data-sync-preview"></a>Problémák az SQL adatszinkronizálás (előzetes verzió)

Ez a cikk ismerteti az Azure SQL adatszinkronizálás (előzetes verzió) kapcsolatos ismert problémák elhárítása. Ha a probléma megoldása, azt itt biztosítja.

SQL adatszinkronizálás (előzetes verzió) áttekintését lásd: [adatok szinkronizálásának több felhőalapú és helyszíni adatbázisokat az Azure SQL adatszinkronizálás (előzetes verzió)](sql-database-sync-data.md).

## <a name="sync-issues"></a>Szinkronizációs problémák

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a>Szinkronizálás nem sikerül, a portál felhasználói felület az ügyfélügynök társított helyszíni adatbázisokhoz

#### <a name="description-and-symptoms"></a>Leírás és jelenségek

Szinkronizálás a helyszíni adatbázisokhoz, az ügynök társított SQL adatszinkronizálás (előzetes verzió) portál felhasználói felület sikertelen lesz. A helyi számítógépen, hogy az ügynök fut System.IO.IOException hibákat az eseménynaplóban talál. A hibák tegyük fel például, hogy rendelkezik-e elegendő hely a lemezen.

#### <a name="resolution"></a>Megoldás:

Hozzon létre több helyet az a meghajtó, amelyen a % TEMP % könyvtárban található.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a>A szinkronizálás csoportomnak Beragadt feldolgozási állapotát

#### <a name="description-and-symptoms"></a>Leírás és jelenségek

A szinkronizálás csoportot SQL adatszinkronizálás (előzetes verzió) hosszú ideig le lett feldolgozási állapotát. Nem válaszol a **leállítása** parancsot, és a naplók megjelenítése nem új bejegyzés.

#### <a name="cause"></a>Ok

A következő feltételek eredményezhet a szinkronizálási csoport alatt Beragadt feldolgozási állapotát:

-   **Az ügyfélügynök offline állapotban**. Ellenőrizze, hogy az ügyfélügynök online-e, és próbálkozzon újra.

-   **Az ügyfélügynök telepítve, vagy hiányzó**. Ha az ügyfél-ügynök telepítve vagy egyéb hiányzik:

    1. Távolítsa el az ügynököt XML-fájl a SQL adatszinkronizálás (előzetes verzió) telepítése, ha a fájl létezik-e.
    2. Telepítse az ügynököt a helyi számítógépen (az azonos vagy eltérő számítógépen is lehet). Majd küldje el a az ügynök, amely offline állapotúként jelenik a portálon létrehozott ügynök kulcsot.

-   **Az adatszinkronizálás SQL szolgáltatás leáll**.

    1. Az a **Start** menü, keressen a **szolgáltatások**.
    2. A keresési eredmények között, válassza ki a **szolgáltatások**.
    3. Keresés a **SQL adatszinkronizálás (előzetes verzió)** szolgáltatás.
    4. Ha a szolgáltatás állapota **leállítva**, kattintson a jobb gombbal a szolgáltatás nevét, majd válassza ki **Start**.

#### <a name="resolution"></a>Megoldás:

Ha a fenti adatokat a szinkronizálási csoport nem kilépni a feldolgozási állapotát, a Microsoft Support alaphelyzetbe állíthatja a szinkronizálási csoport állapotát. Szeretné, hogy a szinkronizálás csoport állapotának alaphelyzetbe állítása, az a [Azure SQL Database fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), hozzon létre egy post. A feladás egy vagy több az előfizetés-Azonosítóval és a csoport, amely alaphelyzetbe kell állítani a szinkronizálási Csoportazonosító tartalmaznak. A Microsoft Support engineer a feladás egy vagy több válaszol, és lehetővé teszi, hogy tudja, amikor az állapot alaphelyzetbe lett állítva.

### <a name="i-see-erroneous-data-in-my-tables"></a>A táblázatban látható hibás adatot

#### <a name="description-and-symptoms"></a>Leírás és jelenségek

Ha táblákat, amelyek ugyanazon névvel rendelkeznek, de ezek a különböző adatbázis sémák szinkronizálási szerepelnek, a szinkronizálás után látható táblázatokban hibás adatot.

#### <a name="cause"></a>Ok

Létesítésének folyamatát kell használnia SQL adatszinkronizálás (előzetes verzió), amelyek ugyanazon névvel rendelkeznek, de amelyek eltérő sémákban táblák a azonos nyomkövetési táblát használ. Ebből kifolyólag a táblákban is tükröződnek követési ugyanabban a táblában. Ennek hatására a hibás adatot módosítások szinkronizálása során.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy a táblákat, amelyek szerepet játszanak a szinkronizálás nevei eltérőek, akkor is, ha a táblák egy adatbázis különböző sémák tartozik.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a>Inkonzisztens elsődleges kulcs adatok látom sikeres szinkronizálás után

#### <a name="description-and-symptoms"></a>Leírás és jelenségek

A szinkronizálás az elvártnak megfelelően sikeres, és a napló sikertelen vagy kihagyott sorok jeleníti meg, de azt láthatja, hogy elsődleges kulcs adatok inkonzisztens a szinkronizálási csoportban található adatbázisok között.

#### <a name="cause"></a>Ok

Ez az eredmény az elvárt működés. Bármely elsődleges kulcsként megadott oszlop eredménye a sorok, ahol az elsődleges kulcs megváltozott inkonzisztens adatok változásairól.

#### <a name="resolution"></a>Megoldás:

A probléma megelőzése érdekében győződjön meg arról, hogy megváltozott-e az elsődleges kulcsként megadott oszlop nincsenek adatok.

A probléma kijavítása után azt, a sort, végpontjai inkonzisztens adatokat a szinkronizálási csoport törlése. Ezután helyezze vissza a sort.

### <a name="i-see-a-significant-degradation-in-performance"></a>A jelentős akár teljesítménycsökkenés látható

#### <a name="description-and-symptoms"></a>Leírás és jelenségek

A teljesítmény csökkenti jelentősen, valószínűleg a pont, ahol még nem tudja megnyitni az adatok szinkronizálása felhasználói felületén.

#### <a name="cause"></a>Ok

A legvalószínűbb ok az szinkronizálási ciklust. Szinkronizálási ciklust akkor fordul elő, amikor A sync által szinkronizálási csoport elindítja a egy szinkronizálás által szinkronizálási csoport a B kiszolgálóra, majd elindítja a szinkronizálási által szinkronizálású csoport A. Lehet, hogy a tényleges helyzet összetettebb, és a hurok kettőnél több szinkronizálási csoportok vonatkozhat. A problémát az okozza, hogy nincs körkörös kiváltó feladatai, amelyek egymással átfedésben lévő szinkronizálási csoportok.

#### <a name="resolution"></a>Megoldás:

A legjobb javítás megelőzési. Győződjön meg arról, hogy a szinkronizálási csoportok nem rendelkezik. a körkörös hivatkozás. Egy másik szinkronizálási csoport által nem szinkronizálható, minden sor egy szinkronizálási csoport van-e szinkronizálva.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a>Ez az üzenet látható: "NULL érték nem lehet beszúrni az oszlop \<oszlop\>. Oszlop nem szerepelhet nulls érték." Ez mit jelent, és hogyan lehet megoldani ezt a problémát? 
Az üzenet azt jelzi, hogy a két alábbi problémák egyike történt:
-  Egy táblázat nem rendelkezik elsődleges kulccsal. A probléma megoldásához, most szinkronizálása összes táblának elsődleges kulcs hozzáadása.
-  A CREATE INDEX utasítás WHERE záradék található. Ez a feltétel nem kezeli az adatszinkronizálás (előzetes verzió). A probléma megoldásához távolítsa el a WHERE záradékot, vagy manuálisan végezze el a módosításokat az összes adatbázisra. 
 
### <a name="how-does-data-sync-preview-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>Hogyan adatszinkronizálás (előzetes verzió). a körkörös hivatkozások kezelni? Ez azt jelenti, ha ugyanazokat az adatokat több szinkronizálási csoportban van-e szinkronizálva, és emiatt tartja módosítása?
Adatszinkronizálás (előzetes verzió). a körkörös hivatkozások nem tud kezelni. Feltétlenül elkerüljék azokat. 

## <a name="client-agent-issues"></a>Ügyfél ügynökökkel kapcsolatos problémák

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a>Az ügyfél-ügynök telepítése, eltávolítása vagy javítása sikertelen

#### <a name="cause"></a>Ok

Számos forgatókönyv ezt a hibát okozhatja. Ez a hiba az adott hiba okának megállapításához tekintse meg a naplókat.

#### <a name="resolution"></a>Megoldás:

Található a meghatározott a hiba okát, létrehozni, és tekintse meg a Windows Installer-naplókat. Ha bekapcsolja a naplózást, a parancssorba. Például LocalAgentHost.msi AgentServiceSetup.msi letöltött fájl esetén létrehozni, és vizsgálja meg a naplófájlokat a következő parancssor használatával:

-   Telepítések:`msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
-   Az eltávolítása:`msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

A Windows Installer által elvégzett összes telepítések naplózást is kapcsolhatja. A Microsoft Tudásbázis [Windows Installer naplózásának engedélyezéséről](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) bekapcsolja a naplózást, a Windows Installer egy kattintással megoldást biztosít. A naplók helye is tartalmazza.

### <a name="my-client-agent-doesnt-work"></a>Az ügyfélügynök nem működik

#### <a name="description-and-symptoms"></a>Leírás és jelenségek

Az alábbi üzenetek kap az ügyfélügynök használatára tett kísérlet során:

"A szinkronizálási hiba történt a paraméter www.microsoft.com/.../05:GetBatchInfoResult deszerializálása során kivétel miatt sikertelen. Belső kivétel leírásában olvasható további információt talál."

"Belső kivételre vonatkozó üzenet:"Microsoft.Synchronization.ChangeBatch"típus érvénytelen gyűjteménytípus, mert nincs alapértelmezett konstruktora."

#### <a name="cause"></a>Ok

Ez az egy ismert probléma az SQL adatszinkronizálás (előzetes verzió) telepítése. Ez az üzenet ennek legvalószínűbb oka, a következők egyikét:

-   Windows 8 Developer Preview verzióját futtatja.
-   .NET-keretrendszer 4.5 telepítve van.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy az ügyfél ügynököt telepít a számítógépen nem fut a Windows 8 Developer Preview verzióját, és hogy van-e telepítve a .NET-keretrendszer 4.5.

### <a name="my-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a>Az ügyfélügynök I szakítsa meg az Eltávolítás után nem működik.

#### <a name="description-and-symptoms"></a>Leírás és jelenségek

Az ügyfélügynök nem működik, ha megszakítja az eltávolítást.

#### <a name="cause"></a>Ok

Ennek oka az, hogy az SQL adatszinkronizálás (előzetes verzió) ügyfélügynök nem tárolja a hitelesítő adatokat.

#### <a name="resolution"></a>Megoldás:

E két megoldásokkal próbálkozhat:

-   A services.msc használatával adja meg újra a hitelesítő adatokat, az ügyfél ügynökének.
-   Távolítsa el az ügyfél ügynökprogramjának, és telepítse a egy újat. Töltse le és telepítse a legújabb ügyfélügynök a [letöltőközpontból](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="my-database-isnt-listed-in-the-agent-list"></a>Az adatbázis nem szerepel-e az ügynök

#### <a name="description-and-symptoms"></a>Leírás és jelenségek

Kísérel meg hozzáadni egy meglévő SQL Server-adatbázis szinkronizálási csoporthoz, az ügynökök listája az adatbázis nem jelenik meg

#### <a name="cause"></a>Ok

Ezek a forgatókönyvek probléma okozhatja:

-   Az ügynök és a szinkronizálási ügyfélcsoport különböző adatközpontokban van.
-   Az ügyfélügynök az adatbázisok listája nem érvényes.

#### <a name="resolution"></a>Megoldás:

A megoldás a OK függ.

- **Különböző adatközpontokban van az ügynök és a szinkronizálás. ügyfélcsoport**

    Az ügyfélügynök és a szinkronizálási csoport ugyanabban az adatközpontban kell lennie. Beállítására, két lehetőség közül választhat:

    -   Hozzon létre egy új ügynök az adatközpontban, ahol a szinkronizálási csoport. Az ügynök ezután regisztrálja az adatbázis.
    -   A jelenlegi szinkronizálási csoport törlése. Ezt követően hozza létre az adatközpont, amelyen az ügynök nem található a szinkronizálású csoport.

- **Az ügyfélügynök az adatbázisok listája nem érvényes**

    Állítsa le és indítsa újra az ügyfélügynök-szolgáltatás.

    A helyi ügynök letölti a társított adatbázisokban csak a az ügynök kulcs első benyújtása listáját. Nem sikerül letölteni a társított adatbázisokban az ezt követő ügynök kulcs elküldött javaslatokra listáját. Egy ügynök áthelyezés során regisztrált adatbázisok eredeti agent-példány nem jelennek meg.

### <a name="client-agent-doesnt-start-error-1069"></a>Ügyfélügynök nem indul el (Hiba 1069)

#### <a name="description-and-symptoms"></a>Leírás és jelenségek

Azt tapasztalja, hogy a az ügynök nem fut az SQL-kiszolgálót futtató számítógépen. Ha megpróbálja manuálisan elindítani az ügynököt, megjelenik egy párbeszédpanel, amely megjeleníti az üzenet "1069 hiba: A szolgáltatás nem indult el a bejelentkezési hiba miatt."

![Szinkronizálási hiba 1069 párbeszédpanel](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

#### <a name="cause"></a>Ok

A hiba legvalószínűbb oka az, hogy a jelszót a helyi kiszolgálón az ügynök és az ügynök jelszó létrehozása óta megváltozott-e.

#### <a name="resolution"></a>Megoldás:

Frissítse az ügynök jelszó a jelenlegi kiszolgáló jelszavát:

1. Keresse meg az SQL adatszinkronizálás (előzetes verzió) ügyfélügynök előzetes szolgáltatás.  
    a. Válassza ki **Start**.  
    b. A keresési mezőbe, írja be a **services.msc**.  
    c. A keresési eredmények között, válassza ki a **szolgáltatások**.  
    d. Az a **szolgáltatások** ablakban, keresse meg a bejegyzés **SQL adatszinkronizálás (előzetes verzió) ügynök előzetes**.  
2. Kattintson a jobb gombbal **SQL adatszinkronizálás (előzetes verzió) ügynök Preview**, majd válassza ki **leállítása**.
3. Kattintson a jobb gombbal **SQL adatszinkronizálás (előzetes verzió) ügynök Preview**, majd válassza ki **tulajdonságok**.
4. A **SQL adatszinkronizálás (előzetes verzió) Preview tulajdonságai**, jelölje be a **jelentkezzen be** fülre.
5. Az a **jelszó** mezőbe írja be a jelszót.
6. Az a **jelszó megerősítése** mezőbe írja be újból a jelszót.
7. Válassza ki **alkalmaz**, majd válassza ki **OK**.
8. Az a **szolgáltatások** ablak, kattintson a jobb gombbal a **SQL adatszinkronizálás (előzetes verzió) ügynök Preview** szolgáltatásra, és kattintson a **Start**.
9. Zárja be a **szolgáltatások** ablak.

### <a name="i-cant-submit-the-agent-key"></a>Nem küldhető be az ügynök kulcs

#### <a name="description-and-symptoms"></a>Leírás és jelenségek

Miután hoz létre, vagy hozza létre újból a kulcsot az ügynökök, megpróbálja küldje el a kulcsot a SqlAzureDataSyncAgent alkalmazáson keresztül. A küldése nem teljes.

![Hiba-párbeszédpanel szinkronizálása - ügynök kulcs nem tudja elküldeni](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

Mielőtt folytatná, ellenőrizze az alábbi feltételek:

-   Az SQL adatszinkronizálás (előzetes verzió) Windows-szolgáltatás fut.  
-   A szolgáltatási fiók SQL adatszinkronizálás (előzetes verzió) Preview Windows-szolgáltatás hálózati hozzáfér.    
-   Az ügyfélügynök is képes kapcsolódni a lokátor szolgáltatást. Ellenőrizze, hogy rendelkezik-e a érték https://locator.sync.azure.com/LocatorServiceApi.svc a következő beállításkulcsot:  
    -   Egy x86 a számítógép:`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`  
    -   Egy x64 a számítógép:`HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\SQL Azure Data Sync\\LOCATORSVCURI`

#### <a name="cause"></a>Ok

Az ügynök kulcs egyedileg azonosítja az egyes helyi ügynök. A kulcs két feltételeknek kell megfelelniük:

-   A SQL adatszinkronizálás (előzetes verzió) kiszolgáló és a helyi számítógép ügynök ügyfélkulcsot azonosnak kell lennie.
-   Az ügynök ügyfélkulcsot csak egyszer használható.

#### <a name="resolution"></a>Megoldás:

Az ügynök nem működik, ha van, mert az egyik vagy mindkét ezek a feltételek nem teljesülnek. Az ügynököt újra beolvasása:

1. Hozzon létre egy új kulcsot.
2. Az ügynök az új kulcsot alkalmazni.

Az új kulcs alkalmazása az ügynököt:

1. A Fájlkezelőben nyissa meg az ügynök telepítési könyvtárába. Az alapértelmezett telepítési mappát a C:\\Program Files (x86)\\Microsoft SQL adatszinkronizálás.
2. Kattintson duplán a bin alkönyvtárat.
3. Indítsa el a SqlAzureDataSyncAgent alkalmazást.
4. Válassza ki **küldje el az ügynököt kulcs**.
5. A rendelkezésre álló helyen illessze be a vágólapra másolt kulcsot.
6. Kattintson az **OK** gombra.
7. Zárja be a program.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a>Az ügyfélügynök nem törölhető a portálról, ha a kapcsolódó helyszíni adatbázis nem érhető el.

#### <a name="description-and-symptoms"></a>Leírás és jelenségek

Ha a helyi végpont (Ez azt jelenti, hogy egy adatbázis), amely regisztrálva van az ügyféloldali ügynök SQL adatszinkronizálás (előzetes verzió) érhető el, az ügyfél ügynökéhez nem lehet törölni.

#### <a name="cause"></a>Ok

A helyi ügynök nem törölhető, mert az nem érhető el adatbázis továbbra is regisztrálva van az ügynök. Az ügynök törlését kísérli meg, a törlési folyamat megpróbálja elérni az adatbázist, amely nem sikerül.

#### <a name="resolution"></a>Megoldás:

Használja a "Törlés force" törli a nem érhető el az adatbázist.

> [!NOTE]
> Szinkronizálási metaadat-táblát "kényszerítése delete" követően is, ha a deprovisioningutil.exe segítségével tisztítása őket.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a>Helyi szinkronizálási ügynök alkalmazást nem lehet kapcsolódni a helyi szinkronizálási szolgáltatás

#### <a name="resolution"></a>Megoldás:

Próbálja meg a következőket:

1. Bezárhatja az alkalmazást.  
2. Az összetevő-szolgáltatások Panel megnyitásához.  
    a. Adja meg a keresési mezőbe, a tálcán, **services.msc**.  
    b. A keresési eredmények között kattintson duplán a **szolgáltatások**.  
3. Állítsa le a **SQL adatszinkronizálás (előzetes verzió) Preview** szolgáltatás.
4. Indítsa újra a **SQL adatszinkronizálás (előzetes verzió) Preview** szolgáltatás.  
5. Nyissa meg újra az alkalmazást.

## <a name="setup-and-maintenance-issues"></a>Telepítési és karbantartási kapcsolatos problémák

### <a name="i-get-a-disk-out-of-space-message"></a>"Nincs elegendő lemezterület lemez" üzenet jelenik meg

#### <a name="cause"></a>Ok

Ha vissza fájlok akkorról törölni kell a "lemez szabad terület" üzenet jelenhet meg. Ennek az oka lehet a víruskereső szoftver, vagy a fájl nyitva, ha próbálkozások törlése.

#### <a name="resolution"></a>Megoldás:

Törölje kézzel a szinkronizálási fájlokat, amelyek a % temp % mappában (`del \*sync\* /s`). Törölje a alkönyvtárai a % temp % mappában.

> [!IMPORTANT]
> Ne törölje a fájlokat, amíg a szinkronizálás folyamatban van.

### <a name="i-cant-delete-my-sync-group"></a>Nem lehet törölni a Szinkronizáló csoport

#### <a name="description-and-symptoms"></a>Leírás és jelenségek

A szinkronizálás csoport törlése sikertelen.

#### <a name="causes"></a>Okok

A következő esetekben előfordulhat, a szinkronizálási csoport törlése sikertelen:

-   Az ügyfélügynök offline állapotban.
-   Az ügyfélügynök telepítve, vagy hiányzik. 
-   Egy adatbázis offline állapotban. 
-   Kiépítés vagy szinkronizálása a szinkronizálási csoportot. 

#### <a name="resolution"></a>Megoldás:

Szinkronizálási csoport törlése nem sikerült feloldani:

-   Győződjön meg arról, hogy online állapotban-e az ügyfélügynök, és próbálkozzon újra.
-   Ha az ügyfél-ügynök telepítve vagy egyéb hiányzik:  
    a. Távolítsa el az ügynököt XML-fájl a SQL adatszinkronizálás (előzetes verzió) telepítése, ha a fájl létezik-e.  
    b. Telepítse az ügynököt a helyi számítógépen (az azonos vagy eltérő számítógépen is lehet). Majd küldje el a az ügynök, amely offline állapotúként jelenik a portálon létrehozott ügynök kulcsot.
-   Győződjön meg arról, hogy az SQL adatszinkronizálás (előzetes verzió) szolgáltatás fut:  
    a. Az a **Start** menü, keressen a **szolgáltatások**.  
    b. A keresési eredmények között, válassza ki a **szolgáltatások**.  
    c. Keresés a **SQL adatszinkronizálás (előzetes verzió) Preview** szolgáltatás.  
    d. Ha a szolgáltatás állapota **leállítva**, kattintson a jobb gombbal a szolgáltatás nevét, majd válassza ki **Start**.
-   Győződjön meg arról, hogy az SQL-adatbázisok és SQL Server-adatbázisok mind online állapotban.
-   Várjon, amíg a kiépítési vagy a szinkronizálási folyamat befejeződik, és ismételje meg a szinkronizálási csoport törlése.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a>A helyszíni SQL Server adatbázis I nem törölhető

#### <a name="cause"></a>Ok

Nagy valószínűséggel kívánt regisztrációjának törlése egy adatbázist, amelyet már töröltek.

#### <a name="resolution"></a>Megoldás:

A helyszíni SQL Server adatbázis regisztrációjának törléséhez válassza ki az adatbázist, és válassza ki **kényszerített törlése**.

Ha ez a művelet sikertelen az adatbázis eltávolítása a szinkronizálási csoportból:

1. Állítsa le és indítsa újra az ügyfél ügynök gazdaszolgáltatása:  
    a. Válassza ki a **Start** menü.  
    b. A keresési mezőbe, írja be a **services.msc**.  
    c. Az a **programok** szakasza a keresési eredmények ablaktáblán kattintson duplán a **szolgáltatások**.  
    d. Kattintson a jobb gombbal a **SQL adatszinkronizálás (előzetes verzió)** szolgáltatás.  
    e. Ha a szolgáltatás fut, állítsa le.  
    f. Kattintson a jobb gombbal a szolgáltatás, és válassza ki **Start**.  
    g. Ellenőrizze, hogy az adatbázis továbbra is regisztrálva lesz. Ha már nincs regisztrálva, az elkészült. Ellenkező esetben folytassa a következő lépéssel.
2. Nyissa meg az ügynök-ügyfélalkalmazás (SqlAzureDataSyncAgent).
3. Válassza ki **hitelesítő adatok szerkesztése**, és adja meg a hitelesítő adatokat az adatbázisban.
4. Regisztráció megszüntetése folytatásához.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a>Nincs elegendő jogosultsággal a rendszerszolgáltatások indításához

#### <a name="cause"></a>Ok

Két esetben ez a hiba akkor fordul elő:
-   A felhasználónév és/vagy a jelszó nem megfelelő.
-   A megadott felhasználói fiók nem rendelkezik elegendő jogosultságokkal a szolgáltatás.

#### <a name="resolution"></a>Megoldás:

Megadja a naplófájl---a-szolgáltatásként hitelesítő adatok a felhasználói fiókhoz:

1. Ugrás a **Start** > **vezérlőpultot** > **felügyeleti eszközök** > **helyi biztonsági házirend**  >  **Helyi házirend** > **felhasználó tartalomvédelem**.
2. Válassza ki **jelentkezzen be a szolgáltatás**.
3. Az a **tulajdonságok** párbeszédpanel mezőben adja meg a felhasználói fiók.
4. Válassza ki **alkalmaz**, majd válassza ki **OK**.
5. Zárjon be minden ablakot.

### <a name="a-database-has-an-out-of-date-status"></a>Egy adatbázis állapota "Elavult"

#### <a name="cause"></a>Ok

SQL adatszinkronizálás (előzetes verzió) eltávolítja a szolgáltatásból 45 nap vagy több (a számítanak a óta, az adatbázis offline állapotba került) kapcsolat nélküli adatbázisok. Ha egy adatbázis 45 nap vagy több offline állapotban, és ezután a ismét online elérhető lesz, állapotú-e **elavult**.

#### <a name="resolution"></a>Megoldás:

Elkerülheti a egy **elavult** biztosításával, hogy nincs adatbázis kapcsolat nélküli módba 45 nap vagy több állapotát.

Ha egy adatbázis állapota **elavult**:

1. Távolítsa el az adatbázist, amely rendelkezik egy **elavult** a szinkronizálási csoport állapotát.
2. Az adatbázis hozzáadása a szinkronizálási csoporthoz újból.

> [!WARNING]
> Miközben offline állapotban volt ehhez az adatbázishoz való végrehajtott valamennyi módosítást elvesznek.

### <a name="a-sync-group-has-an-out-of-date-status"></a>A szinkronizálás csoport állapota "Elavult"

#### <a name="cause"></a>Ok

Ha egy vagy több módosítások nem alkalmazza a teljes megőrzési időszak 45 napot, a szinkronizálási csoport is elavulttá válnak.

#### <a name="resolution"></a>Megoldás:

Elkerülése érdekében egy **elavult** szinkronizálási csoportjának, állapotát, vizsgálja meg a szinkronizálási feladatok az előzménymegjelenítőben, rendszeresen eredményeit. Vizsgálja meg és hárítsa el a módosításokat alkalmazni.

Ha a szinkronizálás csoport állapota **elavult**a sync-csoport törléséhez, majd hozza létre újból.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a>A szinkronizálás csoportot nem lehet törölni, eltávolítása vagy az ügynök leállítása három percen belül

#### <a name="description-and-symptoms"></a>Leírás és jelenségek

A szinkronizálás csoport eltávolítása vagy leállítja a társított SQL adatszinkronizálás (előzetes verzió) ügyfélügynökének három percen belül nem törölhető.

#### <a name="resolution"></a>Megoldás:

1. Távolítsa el a szinkronizálási csoportot, amíg kapcsolódik az internethez kapcsolódó szinkronizálási ügynökök (ajánlott).
2. Ha az ügynök offline állapotban, de van telepítve, is online állapotba kerüljön a helyi számítógépen. Várjon, amíg az állapot, hogy az ügynök frissítésként jelenik meg **Online** az SQL adatszinkronizálás (előzetes verzió) portálon. Ezután távolítsa el a szinkronizálási csoportot.
3. Ha az ügynök offline állapotban, mert el lett távolítva:  
    a.  Távolítsa el az ügynököt XML-fájl a SQL adatszinkronizálás (előzetes verzió) telepítése, ha a fájl létezik-e.  
    b.  Telepítse az ügynököt a helyi számítógépen (az azonos vagy eltérő számítógépen is lehet). Majd küldje el a az ügynök, amely offline állapotúként jelenik a portálon létrehozott ügynök kulcsot.  
    c. Próbálja meg törölni a szinkronizálású csoport.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a>Mi történik, ha szeretnék elveszett vagy sérült adatbázis visszaállítása?

Ha elveszett vagy sérült adatbázis visszaállítása egy biztonsági másolatból, valószínűleg egy nonconvergence az adatok a szinkronizálási csoportok, amelyhez az adott adatbázis is tartozik.

## <a name="next-steps"></a>Következő lépések
SQL adatszinkronizálás (előzetes verzió) kapcsolatos további információkért lásd:

-   [Szinkronizálja az adatokat több felhőalapú és helyszíni adatbázisokat az Azure SQL adatszinkronizálás (előzetes verzió)](sql-database-sync-data.md)  
-   [Azure SQL adatszinkronizálás (előzetes verzió) beállítása](sql-database-get-started-sql-data-sync.md)  
-   [Ajánlott eljárások az Azure SQL adatszinkronizálás (előzetes verzió)](sql-database-best-practices-data-sync.md)  
-   [A figyelő az Azure SQL adatszinkronizálás (előzetes verzió) az OMS szolgáltatáshoz](sql-database-sync-monitor-oms.md)  
-   Teljes PowerShell-példák bemutatják, hogyan konfigurálja az SQL adatszinkronizálás (előzetes verzió):  
    -   [A PowerShell szolgáltatás használatával több Azure SQL-adatbázisok közötti szinkronizálása](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Egy Azure SQL-adatbázis és a helyszíni SQL Server-adatbázisok közötti szinkronizálása a PowerShell használatával](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Az SQL adatszinkronizálás (előzetes verzió) REST API-dokumentáció letöltése](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

SQL-adatbázis kapcsolatos további információkért lásd:

-   [SQL-adatbázis – áttekintés](sql-database-technical-overview.md)
-   [Adatbázis életciklusának kezelésére](https://msdn.microsoft.com/library/jj907294.aspx)
