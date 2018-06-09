---
title: Azure áttelepítése elhárítása |} Microsoft Docs
description: Ismert problémák a Azure át, és a hibaelhárítási tippek gyakori hibák áttekintése.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 06/08/2018
ms.author: raynew
ms.openlocfilehash: c717cfdac83ec8d85b1fa0a874e5573a40dd4611
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235627"
---
# <a name="troubleshoot-azure-migrate"></a>Az Azure Migrate hibaelhárítása

## <a name="troubleshoot-common-errors"></a>Gyakori hibák elhárítása

[Az Azure áttelepítése](migrate-overview.md) értékelésére a helyszíni munkaterhelések Azure való áttelepítésre. Ez a cikk segítségével elhárítása üzembe helyezése és használata a Azure áttelepítése során.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Áttelepítési projekt létrehozása nem sikerült *kérelmek tartalmaznia kell a felhasználói identitás fejlécek*

A probléma akkor fordulhat elő, a felhasználók számára, akik nem rendelkeznek hozzáféréssel a szervezet Azure Active Directory (Azure AD) bérlőjévé. Amikor a felhasználó első alkalommal hozzáadódik az Azure AD-bérlő, többé kap egy e-mailt a meghívás való csatlakozásra a bérlő. Nyissa meg az e-mailt, és a bérlő beolvasása sikeresen hozzáadva a felkérés elfogadásával kell felhasználók. Ha nem látja az e-mailt, érheti el a felhasználó, aki már rendelkezik-e hozzáféréssel a bérlő számára, és kérje meg a meghívót Önnek megadott lépéseit követve küldje el újra [Itt](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Miután a meghívó e-mail érkezik, nyissa meg az e-mailt, és kattintson a hivatkozásra, az e-mailben a meghívó elfogadásának szüksége. Ha ezzel végzett, be kell jelentkeznie az Azure-portálon kívül, és a bejelentkezési újra, a böngésző frissítése nem fog működni. Az áttelepítési-projekt létrehozása és próbálkozzon.

### <a name="performance-data-for-disks-and-networks-adapters-shows-as-zeros"></a>A lemezek és a hálózat adapterek teljesítményadatait jeleníti meg értékűként

Ez akkor fordulhat elő, ha a statisztikákat a beállítás a vCenter Server szintje legalább hármat. A három szint vagy magasabb vCenter tárolja VM teljesítményelőzményei számítási, tárolási és hálózati. A három szint kisebb, mint a vCenter tárolási és hálózati adatok, de csak a Processzor- és adatok nem tárolja. Ebben a forgatókönyvben teljesítmény adatokat jeleníti meg, mint nulla, a Azure áttelepítése és Azure áttelepítése mérete javaslat lemezek és a metaadatok a helyszíni készülékekről gyűjtött hálózatokat tartalmaz.

Ahhoz, hogy a lemezek és a hálózati teljesítmény adatok gyűjtését, módosítsa a statisztika beállítások szintjét három. Ezt követően legalább egy napot várni felderítése a környezetében, és értékeléséhez azt.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>I telepített ügynökök, és a függőségi képi megjelenítés csoportok létrehozásához használt. A gépek megjelenítése "Ügynököt a" művelet "Függőségeinek megtekintése" helyett most utáni feladatátvétel esetén
* POST tervezett vagy nem tervezett feladatátvétel a helyszíni gépek ki vannak kapcsolva, és egyenértékű gépeken vannak hoz létre az Azure-ban. Ezek a gépek szerezzen be egy másik MAC-címet. Ezek megszerezheti egy másik IP-cím alapján, hogy a felhasználó úgy döntene, hogy megőrzi a helyi IP-címet, vagy nem. Eltérnek a MAC és IP-címeket is, ha az Azure áttelepítése nem rendel hozzá a helyszíni gépeket Szolgáltatástérkép függőségi adatokat, és megkérdezi a felhasználót az ügynökök helyett függőségeinek megtekintése telepítéséhez.
* A feladatátvételi teszt utáni a helyszíni gépeket bekapcsolva maradjon, várt módon. Egyenértékű gépek hoz létre az Azure-ban különböző MAC-címet szerezzenek be, és előfordulhat, hogy a különböző IP-cím megszerzésére. Kivéve, ha a felhasználó ezek a gépek Naplóelemzési kimenő forgalmat blokkolja, az Azure áttelepítése nem rendel hozzá a helyszíni gépeket Szolgáltatástérkép függőségi adatokat, és megkérdezi a felhasználót az ügynökök helyett függőségeinek megtekintése telepítéséhez.

## <a name="collector-errors"></a>Adatgyűjtő hibák

### <a name="deployment-of-collector-ova-failed"></a>Adatgyűjtő petesejtek központi telepítése nem sikerült

Ha a petesejtek részben letöltődik, illetve a böngésző ez fordulhat, ha a központi telepítése a petesejtek vSphere webes ügyfelet kell használnia. Győződjön meg arról, hogy a letöltés befejeződött, és próbálkozzon egy másik böngésző petesejtek telepítése.

### <a name="collector-is-not-able-to-connect-to-the-internet"></a>Gyűjtő nem tud csatlakozni az internethez

Ez akkor fordulhat elő, ha a gép, használja a rendszer proxy mögött van. Győződjön meg arról, hogy az engedélyezési hitelesítő adatok megadása, ha a proxy szüksége van rá.
Ha bármilyen URL-alapú tűzfal proxy segítségével szabályozhatja a kimenő kapcsolat, mindenképp érdemes engedélyezett URL-címeket az alábbi szükséges:

**URL-cím** | **Cél**  
--- | ---
*.portal.azure.com | Szükséges ellenőrizze a kapcsolatot az Azure-szolgáltatás, és érvényesítésére időszinkronizálást ad ki.
*.oneget.org | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul.

**A gyűjtő nem tud kapcsolódni a projekt a projekt azonosítójával, és kulcs másolását a portálról.**

Győződjön meg arról, hogy másolja, és a megfelelő információk beillesztett. Elhárításához telepítse a Microsoft Monitoring Agent (MMA), és győződjön meg arról, ha a MMA az alábbiak szerint csatlakozhat a project:

1. A gyűjtő VM, töltse le a [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. A telepítés elindításához kattintson duplán a letöltött fájlt.
3. A telepítő a a **üdvözli** kattintson **tovább**. A **Licencfeltételek** oldalon kattintson az **Elfogadom** gombra a feltételek elfogadásához.
4. A **célmappa**, hagyja, vagy módosítsa az alapértelmezett telepítési mappa > **következő**.
5. A **ügynök telepítésének beállításai**, jelölje be **Azure Naplóelemzés** > **következő**.
6. Kattintson a **Hozzáadás** hozzáadása egy új munkaterületet. Illessze be a projekt azonosítója és a másolt kulcsot. Ezután kattintson a **Next** (Tovább) gombra.
7. Győződjön meg arról, hogy az ügynök a projekt csatlakozhat. Ha nem, akkor ellenőrizze a beállításokat. Ha az ügynök képes kapcsolódni, de a gyűjtő nem, forduljon a támogatási szolgálathoz.


### <a name="error-802-date-and-time-synchronization-error"></a>802. hiba: Dátum és idő szinkronizálási hiba

A kiszolgáló óra lehet, hogy ki a szinkronizálás a több mint öt perc által az aktuális idővel. A gyűjtő virtuális gépek a következőképpen egyezik az aktuális idő, az idő módosítása:

1. Nyisson meg egy rendszergazdai parancssort, a virtuális Gépen.
2. Ellenőrizze az időzóna, futtassa a w32tm /tz.
3. Szinkronizálja az időt, futtassa a w32tm/resync.

### <a name="vmware-powercli-installation-failed"></a>VMware PowerCLI telepítése nem sikerült

Azure áttelepítése adatgyűjtő PowerCLI letölti és telepíti azt a készüléken. PowerCLI telepítési hiba miatt nem érhető el a PowerCLI tárház végpontjainak lehet. Elhárításához próbálja meg manuálisan telepíteni PowerCLI a gyűjtő virtuális gépet a következő lépés:

1. Nyissa meg a Windows PowerShell rendszergazdai módban
2. Nyissa meg a könyvtár C:\ProgramFiles\ProfilerService\VMWare\Scripts\
3. Futtassa a parancsfájlt InstallPowerCLI.ps1

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>Hiba UnhandledException belső hiba történt: System.IO.FileNotFoundException

Ez a hiba a gyűjtő 1.0.9.5-ösnél korábbi verzióiban jelentkezik. Ha a gyűjtő 1.0.9.2-es vagy a GA előtti, például az 1.0.8.59-es verzióját használja, ez a probléma elő fog fordulni. [Részletes válaszokért lásd a fórumokra mutató hivatkozást](https://social.msdn.microsoft.com/Forums/azure/en-US/c1f59456-7ba1-45e7-9d96-bae18112fb52/azure-migrate-connect-to-vcenter-server-error?forum=AzureMigrate).

[A probléma felszámolásához frissítse a gyűjtőt](https://aka.ms/migrate/col/checkforupdates).

### <a name="error-unabletoconnecttoserver"></a>Hiba UnableToConnectToServer

Nem sikerült kapcsolódni a vCenter-kiszolgáló "Servername.com:9443" hiba miatt: Hiba történt a nem figyel végpont https://Servername.com:9443/sdk , amely tudta volna fogadni az üzenetet.

Ha Ön a gyűjtő készülék legújabb verziója fut, ha nem, frissítse a készülékre ellenőrizze a [legújabb verziójára](https://docs.microsoft.com/azure/migrate/concepts-collector#how-to-upgrade-collector).

Ha a probléma továbbra is a legújabb verzió, annak oka lehet az adatgyűjtő géphez nem tudja feloldani a vCenter-kiszolgáló nevét vagy a megadott meghatározott port nem megfelelő. Alapértelmezés szerint a port nincs megadva, ha adatgyűjtő megpróbál csatlakozni a 443-as port számát.

1. Próbálja a gyűjtő gépről Servername.com pingelését.
2. Ha az 1. lépés meghiúsul, próbáljon meg az IP-címen keresztül csatlakozni a vCenter-kiszolgálóhoz.
3. Adja meg a megfelelő portszámot a vCenterhez történő csatlakozáshoz.
4. Végezetül pedig ellenőrizze, hogy a vCenter-kiszolgáló fut-e.

## <a name="troubleshoot-readiness-issues"></a>Készültségi elhárítása

**A probléma** | **Fix**
--- | ---
Nem támogatott rendszerindítási típus | Azure nem támogatja a virtuális gépek az EFI rendszerindítási típusa. Javasoljuk, hogy a rendszerindító típus konvertálása BIOS áttelepítés futtatása előtt. <br/><br/>Használhat [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) a ilyen virtuális gépek az áttelepítés végrehajtásához, mivel azt konvertálja a virtuális gép rendszerindító típusú BIOS az áttelepítés során.
Feltételesen támogatott Windows operációs rendszer | Az operációs rendszer támogatási dátum végét a rendszer megfelelt, és szüksége van egy egyéni támogatja a szerződés (CSA) a [támogatja az Azure-ban](https://aka.ms/WSosstatement), fontolja meg az operációs rendszer frissítése az Azure-bA áttelepítése előtt.
Nem támogatott Windows operációs rendszer | Csak az Azure támogatja [Windows operációsrendszer-verziók kijelölt](https://aka.ms/WSosstatement), érdemes frissíteni az operációs rendszer, a gép az Azure-bA áttelepítése előtt.
Feltételesen jóváhagyott Linux operációs rendszer | Azure csak hozzá támogatást [kijelölt Linux operációsrendszer-verziók](../virtual-machines/linux/endorsed-distros.md), érdemes frissíteni az operációs rendszer, a gép az Azure-bA áttelepítése előtt.
Nem jóváhagyott Linux operációs rendszer | A gép előfordulhat, hogy indítsa el az Azure-ban, de nem az operációs rendszer támogatott Azure által biztosított, érdemes frissíteni az operációs rendszer egy [által támogatott Linux verzió](../virtual-machines/linux/endorsed-distros.md) Azure áttelepítése előtt
Ismeretlen operációs rendszer | Az operációs rendszer a virtuális gép vCenter-kiszolgáló, amelyek miatt Azure áttelepítése nem tudja azonosítani a virtuális gép Azure készültségét mint az "Egyéb" van megadva. Győződjön meg arról, hogy az a gépen futó operációs rendszer [támogatott](https://aka.ms/azureoslist) az Azure-ban a gép áttelepítése előtt.
Az operációs rendszer bitszáma nem támogatott | Virtuális gépek 32 bites operációs rendszer előfordulhat, hogy indítsa el az Azure-ban, de javasoljuk, hogy 32 bites operációs rendszere a virtuális gép frissíthető a 64 bites Azure áttelepítése előtt.
Visual Studio előfizetést igényel. | A gépek rendelkezik egy Windows ügyfél OS belül futó, amely csak a Visual Studio-előfizetéssel támogatott.
A virtuális gép nem található a szükséges tárolási teljesítmény eléréséhez. | A tárolási teljesítményt (IOPS/átvitel) a gép szükséges túllépi Azure VM-támogatási szolgálathoz. Az áttelepítés előtt gép tárolási követelmények csökkentése.
A virtuális gép nem található a szükséges hálózati teljesítmény eléréséhez. | A hálózati teljesítmény (be/ki) a gép szükséges túllépi Azure VM-támogatási szolgálathoz. Csökkentse a hálózati követelmények, a gép.
A virtuális gép nem található a megadott tarifacsomagot. | Ha ez a tarifacsomag be van állítva, a Standard, fontolja meg a virtuális gép downsizing Azure áttelepítése előtt. Ha a méretezési réteg Basic, érdemes a assessment árképzési szintjének módosítása a szabványos.
A virtuális gép nem található a megadott helyen. | Használjon egy másik célhelyet az áttelepítés előtt.
Egy vagy több nem alkalmas lemezt. | Egy vagy több lemezt a virtuális Géphez csatlakozik, nem felel meg az Azure-követelményeknek. A virtuális Géphez csatlakozik, lemezek győződjön meg arról, hogy a lemez mérete < 4 TB-os, ha nem, a lemez méretének zsugorítása Azure áttelepítése előtt. Győződjön meg arról, hogy egyes lemezek számára szükséges teljesítményt (IOPS/átvitel) Azure által támogatott [felügyelt virtuális gépek lemezeit](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits).   
Egy vagy több nem alkalmas hálózati adapterrel. | Távolítsa el a nem használt hálózati adapterek a gépről áttelepítés előtt.
A lemezek száma meghaladja a korlátot | Távolítsa el a nem használt lemezeket a gépen az áttelepítés előtt.
A lemezméret meghaladja a korlátot | Azure támogatja a lemez mérete legfeljebb 4 TB-os. Áttelepítés előtt kisebb, mint 4 TB-os lemezeken igazodjon.
A lemez nem érhető el a megadott helyen | Ellenőrizze, hogy a lemez a célként megadott helyen az áttelepítés előtt.
A lemez a megadott redundanciával nem érhető el | A lemez kell használniuk az értékelés (alapértelmezés szerint LRS) beállítások között megadott redundancia tárolási típusát.
Belső hiba miatt nem sikerült meghatározni a lemez alkalmasságát | Próbálja meg létrehozni egy új assessment a csoporthoz.
Nem található a szükséges magokkal és memóriával rendelkező virtuális gép | Azure azokat nem a megfelelő Virtuálisgép-típussá. Csökkentse a memória és a helyi gép magok száma, az áttelepítés előtt.
Virtuális gép alkalmasságát belső hiba miatt nem állapítható meg. | Próbálja meg létrehozni egy új assessment a csoporthoz.
Belső hiba miatt egy vagy több lemezt alkalmazhatósági nem állapítható meg. | Próbálja meg létrehozni egy új assessment a csoporthoz.
Belső hiba miatt egy vagy több hálózati adapter alkalmazhatósági nem állapítható meg. | Próbálja meg létrehozni egy új assessment a csoporthoz.


## <a name="collect-logs"></a>Naplógyűjtéshez

**Hogyan gyűjthetem be a naplókat a VM-gyűjtőn?**

Alapértelmezés szerint a naplózás engedélyezve van. Naplók az alábbiak szerint találhatók:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

A Windows esemény-nyomkövetése gyűjtése, tegye a következőket:

1. VM-gyűjtő nyisson meg egy PowerShell-parancsablakot.
2. Futtatás **Get-Eseménynapló - naplónév alkalmazás |} exportálás csv eventlog.csv**.

**Hogyan gyűjthetem be a portál hálózati forgalmi naplók?**

1. Nyissa meg a böngészőt, és keresse meg, és jelentkezzen be [a portálra](https://portal.azure.com).
2. Nyomja le az F12 billentyűt a fejlesztői eszközök elindítása. Ha szükséges, törölje a jelet a beállítás **navigációs bejegyzések törlése**.
3. Kattintson a **hálózati** lapot, és indítsa el a hálózati forgalom rögzítése:
 - A Chrome-ban, válassza ki a **Preserve napló**. A felvétel automatikusan elindul. A piros kör azt jelzi, hogy a forgalom rögzítése folyamatban van. Ha nem jelenik meg, kattintson a fekete kör indítása
 - A peremhálózati/Internet Explorer a rögzítés automatikusan elindul. Ha nem, kattintson a zöld lejátszás gombra.
4. Próbálja meg Reprodukálja a hibát.
5. Miután a rögzítés során hiba történt rögzítés, és a rögzített tevékenység másolatának mentése:
 - A Chrome-ban, kattintson a jobb gombbal, és kattintson a **HAR elmentse tartalmú**. Ez az üzenet pillanatok alatt, és a naplók .har fájlként exportálja.
 - A peremhálózati/IE, kattintson a **exportálási rögzített forgalom** ikonra. Ez az üzenet pillanatok alatt, és exportálja a naplót.
6. Keresse meg a **konzol** fülre, és ellenőrizze az összes figyelmeztetést és hibát. A konzol naplófájl mentése:
 - A Chrome-ban kattintson a jobb gombbal bárhol a konzol naplóban. Válassza ki **Mentés másként**, kell exportálni, és a zip-a naplót.
 - Az Edge/IE, kattintson a jobb gombbal a hibákat, és válassza ki a **másolhatja az összes**.
7. Zárja be a fejlesztői eszközök.

## <a name="collector-error-codes-and-recommended-actions"></a>Adatgyűjtő-hibakódokat és a javasolt művelet

|           |                                |                                                                               |                                                                                                       |                                                                                                                                            |
|-----------|--------------------------------|-------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Hibakód | Hiba neve                      | Üzenet                                                                       | Lehetséges okok                                                                                        | Javasolt művelet                                                                                                                          |
| 601       | CollectorExpired               | A gyűjtő érvényessége lejárt.                                                        | A gyűjtő érvényessége lejárt.                                                                                    | Töltse le a gyűjtő egy új verzióját, majd próbálkozzon újra.                                                                                      |
| 751       | UnableToConnectToServer        | Nem sikerült csatlakozni a(z) %Name; vCenter Server-kiszolgálóhoz a következő hiba miatt: %ErrorMessage;.     | További részletekért tekintse meg a hibaüzenetet.                                                             | Hárítsa el a problémát, és próbálkozzon újra.                                                                                                           |
| 752       | InvalidvCenterEndpoint         | A(z) %Name; nem vCenter Server-kiszolgáló.                                  | Adja meg a VCenter Server-kiszolgáló adatait.                                                                       | Próbálja megismételni a műveletet helyes vCenter Server-kiszolgálói adatokkal.                                                                                   |
| 753       | InvalidLoginCredentials        | Nem sikerült csatlakozni a(z) %Name; vCenter Server-kiszolgálóhoz a következő hiba miatt: %ErrorMessage;. | Nem sikerült csatlakozni a vCenter Server-kiszolgálóhoz, mert a bejelentkezési hitelesítő adatok érvénytelenek.                             | Győződjön meg arról, hogy a megadott bejelentkezési hitelesítő adatok helyesek.                                                                                    |
| 754       | NoPerfDataAvaialable           | Nem érhetők el a teljesítményadatok.                                               | Ellenőrizze a statisztika szintjét a vCenter Server. Teljesítményadatok elérhető legyen a 3 kell megadni. | Módosítsa a statisztika szintjét 3-asra (5 perces, 30 perces és 2 órás időtartamhoz), és próbálkozzon legalább egy napos várakozás után.                   |
| 756       | NullInstanceUUID               | A rendszer olyan gépet észlelt, mely példányának UUID azonosítója null értékű                                  | Lehetséges, hogy a vCenter Server-kiszolgáló nem megfelelő objektummal rendelkezik.                                                      | Hárítsa el a problémát, és próbálkozzon újra.                                                                                                           |
| 757       | VMNotFound                     | A virtuális gép nem található                                                  | Lehetséges, hogy a virtuális gépet törölték: %VMID;                                                                | Győződjön meg arról, hogy a vCenter-készlet hatókörének beállításakor kiválasztott virtuális gépek léteznek az észleléskor                                      |
| 758       | GetPerfDataTimeout             | VCenter kérelem túllépte az időkorlátot. Üzenet: % Message;                                  | A vCenter Server-kiszolgáló hitelesítő adatai helytelenek                                                              | Ellenőrizze a vCenter Server hitelesítő adatokat, és győződjön meg arról, hogy a vCenter Server-kiszolgáló elérhető-e. Próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon a támogatási szolgálathoz. |
| 759       | VmwareDllNotFound              | A VMWare.Vim DLL-fájlja nem található.                                                     | A PowerCLI nincs megfelelően telepítve.                                                                   | Ellenőrizze, ha PowerCLI megfelelően van-e telepítve. Próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon a támogatási szolgálathoz.                               |
| 800       | ServiceError                   | Az Azure Migrate Collector szolgáltatás nem fut.                               | Az Azure Migrate Collector szolgáltatás nem fut.                                                       | A Services.msc használatával indítsa el a szolgáltatást, majd próbálja megismételni a műveletet.                                                                             |
| 801       | PowerCLIError                  | A VMware PowerCLI telepítése nem sikerült.                                          | A VMware PowerCLI telepítése nem sikerült.                                                                  | Próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, telepítse manuálisan, majd próbálja megismételni a műveletet.                                                   |
| 802       | TimeSyncError                  | Az idő nincs internetes időkiszolgálóval szinkronizálva.                            | Az idő nincs internetes időkiszolgálóval szinkronizálva.                                                    | Győződjön meg arról, hogy a számítógépen az időzónájának megfelelően van beállítva az idő, majd próbálja megismételni a műveletet.                                 |
| 702       | OMSInvalidProjectKey           | A megadott projektkulcs érvénytelen.                                                | A megadott projektkulcs érvénytelen.                                                                        | Próbálja megismételni a műveletet a megfelelő projektkulccsal.                                                                                              |
| 703       | OMSHttpRequestException        | Hiba történt a kérelem küldése. Üzenet: % Message;                                | Ellenőrizze a projekt azonosítóját és kulcsát, és győződjön meg arról, hogy a végpont elérhető.                                       | Próbálja megismételni a műveletet. Ha nem szűnik meg a probléma, forduljon a Microsoft támogatási szolgálatához.                                                                     |
| 704       | OMSHttpRequestTimeoutException | HTTP-kérelem túllépte az időkorlátot. Üzenet: % Message;                                     | Ellenőrizze, hogy a projektazonosító és -kulcs helyes-e, és győződjön meg róla, hogy a végpont olvasható.                                       | Próbálja megismételni a műveletet. Ha nem szűnik meg a probléma, forduljon a Microsoft támogatási szolgálatához.                                                                     |
