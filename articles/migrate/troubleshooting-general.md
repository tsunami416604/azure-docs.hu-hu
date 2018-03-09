---
title: "Azure áttelepítése elhárítása |} Microsoft Docs"
description: "Ismert problémák a Azure át, és a hibaelhárítási tippek gyakori hibák áttekintése."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: troubleshooting
ms.date: 02/21/2018
ms.author: raynew
ms.openlocfilehash: e1e7a1a57f780ef477379dfb1ceaead0c8654970
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-azure-migrate"></a>Az Azure Migrate hibaelhárítása

## <a name="troubleshoot-common-errors"></a>Gyakori hibák elhárítása

[Az Azure áttelepítése](migrate-overview.md) értékelésére a helyszíni munkaterhelések Azure való áttelepítésre. Ez a cikk segítségével elhárítása üzembe helyezése és használata a Azure áttelepítése során.


**Gyűjtő nem tud csatlakozni az internethez**

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
3. A telepítő a a **üdvözli** kattintson **tovább**. Az a **licencfeltételeket** kattintson **elfogadom** elfogadja a licencfeltételeket.
4. A **célmappa**, hagyja, vagy módosítsa az alapértelmezett telepítési mappa > **következő**.
5. A **ügynök telepítésének beállításai**, jelölje be **Azure Naplóelemzés (OMS)** > **következő**.
6. Kattintson a **Hozzáadás** hozzáadása egy új munkaterületet. Illessze be a projekt azonosítója és a másolt kulcsot. Ezután kattintson a **Next** (Tovább) gombra.
7. Győződjön meg arról, hogy az ügynök a projekt csatlakozhat. Ha nem, akkor ellenőrizze a beállításokat. Ha az ügynök képes kapcsolódni, de a gyűjtő nem, forduljon a támogatási szolgálathoz.


**802. hiba: a dátum és idő szinkronizálási hiba jelenik.**

A kiszolgáló óra lehet, hogy ki a szinkronizálás a több mint öt perc által az aktuális idővel. A gyűjtő virtuális gépek a következőképpen egyezik az aktuális idő, az idő módosítása:

1. Nyisson meg egy rendszergazdai parancssort, a virtuális Gépen.
2. Ellenőrizze az időzóna, futtassa a w32tm /tz.
3. Szinkronizálja az időt, futtassa a w32tm/resync.

**A projekt kulcs "==" szimbólumok vége felé van. A kódolt más alfanumerikus karakter hosszú lehet a gyűjtő által. Ez várható?**

Igen, minden projekt kulcs végződik "==". A gyűjtő feldolgozása előtt titkosítja a projekt kulcsot.

**A lemezek és a hálózat adapterek teljesítményadatait jeleníti meg értékűként**

Ez akkor fordulhat elő, ha a statisztikákat a beállítás a vCenter Server szintje legalább hármat. A három szint vagy magasabb vCenter tárolja VM teljesítményelőzményei számítási, tárolási és hálózati. A három szint kisebb, mint a vCenter tárolási és hálózati adatok, de csak a Processzor- és adatok nem tárolja. Ebben a forgatókönyvben teljesítmény adatokat jeleníti meg, mint nulla, a Azure áttelepítése és Azure áttelepítése mérete javaslat lemezek és a metaadatok a helyszíni készülékekről gyűjtött hálózatokat tartalmaz.

Ahhoz, hogy a lemezek és a hálózati teljesítmény adatok gyűjtését, módosítsa a statisztika beállítások szintjét három. Ezt követően legalább egy napot várni felderítése a környezetében, és értékeléséhez azt. 

**I telepített ügynökök, és a függőségi képi megjelenítés csoportok létrehozásához használt. A gépek megjelenítése "Ügynököt a" művelet "Függőségeinek megtekintése" helyett most utáni feladatátvétel esetén**
* POST tervezett vagy nem tervezett feladatátvétel a helyszíni gépek ki vannak kapcsolva, és egyenértékű gépeken vannak hoz létre az Azure-ban. Ezek a gépek szerezzen be egy másik MAC-címet. Ezek megszerezheti egy másik IP-cím alapján, hogy a felhasználó úgy döntene, hogy megőrzi a helyi IP-címet, vagy nem. Eltérnek a MAC és IP-címeket is, ha az Azure áttelepítése nem rendel hozzá a helyszíni gépeket Szolgáltatástérkép függőségi adatokat, és megkérdezi a felhasználót az ügynökök helyett függőségeinek megtekintése telepítéséhez.
* A feladatátvételi teszt utáni a helyszíni gépeket bekapcsolva maradjon, várt módon. Egyenértékű gépek hoz létre az Azure-ban különböző MAC-címet szerezzenek be, és előfordulhat, hogy a különböző IP-cím megszerzésére. Kivéve, ha a felhasználó ezek a gépek OMS kimenő forgalmat blokkolja, az Azure áttelepítése nem rendel hozzá a helyszíni gépeket Szolgáltatástérkép függőségi adatokat, és megkérdezi a felhasználót az ügynökök helyett függőségeinek megtekintése telepítéséhez.


## <a name="troubleshoot-readiness-issues"></a>Készültségi elhárítása

**A probléma** | **Fix**
--- | ---
Nem támogatott rendszerindítási típus | Azure nem támogatja a virtuális gépek az EFI rendszerindítási típusa. Javasoljuk, hogy a rendszerindító típus konvertálása BIOS áttelepítés futtatása előtt. <br/><br/>Használhat [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) a ilyen virtuális gépek az áttelepítés végrehajtásához, mivel azt konvertálja a virtuális gép rendszerindító típusú BIOS az áttelepítés során.
A lemezek száma meghaladja a korlátot | Távolítsa el a nem használt lemezeket a gépen az áttelepítés előtt.
A lemezméret meghaladja a korlátot | Azure támogatja a lemez mérete legfeljebb 4 TB-os. Áttelepítés előtt kisebb, mint 4 TB-os lemezeken igazodjon. 
A lemez nem érhető el a megadott helyen | Ellenőrizze, hogy a lemez a célként megadott helyen az áttelepítés előtt.
A lemez a megadott redundanciával nem érhető el | A lemez kell használniuk az értékelés (alapértelmezés szerint LRS) beállítások között megadott redundancia tárolási típusát.
Belső hiba miatt nem sikerült meghatározni a lemez alkalmasságát | Próbálja meg létrehozni egy új assessment a csoporthoz. 
Nem található a szükséges magokkal és memóriával rendelkező virtuális gép | Azure azokat nem a megfelelő Virtuálisgép-típussá. Csökkentse a memória és a helyi gép magok száma, az áttelepítés előtt. 
Egy vagy több nem alkalmas lemezt. | Győződjön meg arról, hogy a helyi lemezek 4 TB-os vagy az áttelepítés futtatása előtt.
Egy vagy több nem alkalmas hálózati adapterrel. | Távolítsa el a nem használt hálózati adapterek a gépről áttelepítés előtt.
Virtuális gép alkalmasságát belső hiba miatt nem állapítható meg. | Próbálja meg létrehozni egy új assessment a csoporthoz. 
Belső hiba miatt egy vagy több lemezt alkalmazhatósági nem állapítható meg. | Próbálja meg létrehozni egy új assessment a csoporthoz.
Belső hiba miatt egy vagy több hálózati adapter alkalmazhatósági nem állapítható meg. | Próbálja meg létrehozni egy új assessment a csoporthoz.
A virtuális gép nem található a szükséges tárolási teljesítmény eléréséhez. | A tárolási teljesítményt (IOPS/átvitel) a gép szükséges túllépi Azure VM-támogatási szolgálathoz. Az áttelepítés előtt gép tárolási követelmények csökkentése.
A virtuális gép nem található a szükséges hálózati teljesítmény eléréséhez. | A hálózati teljesítmény (be/ki) a gép szükséges túllépi Azure VM-támogatási szolgálathoz. Csökkentse a hálózati követelmények, a gép. 
A virtuális gép nem található a megadott tarifacsomagot. | Ha ez a tarifacsomag be van állítva, a Standard, fontolja meg a virtuális gép downsizing Azure áttelepítése előtt. Ha a méretezési réteg Basic, érdemes a assessment árképzési szintjének módosítása a szabványos. 
A virtuális gép nem található a megadott helyen. | Használjon egy másik célhelyet az áttelepítés előtt.
Ismeretlen operációs rendszer | Az operációs rendszer a virtuális gép vCenter-kiszolgáló, amelyek miatt Azure áttelepítése nem tudja azonosítani a virtuális gép Azure készültségét mint az "Egyéb" van megadva. Győződjön meg arról, hogy az a gépen futó operációs rendszer [támogatott](https://aka.ms/azureoslist) az Azure-ban a gép áttelepítése előtt.
Feltételesen támogatott Windows operációs rendszer | Az operációs rendszer támogatási dátum végét a rendszer megfelelt, és szüksége van egy egyéni támogatja a szerződés (CSA) a [támogatja az Azure-ban](https://aka.ms/WSosstatement), fontolja meg az operációs rendszer frissítése az Azure-bA áttelepítése előtt.
Nem támogatott Windows operációs rendszer | Csak az Azure támogatja [Windows operációsrendszer-verziók kijelölt](https://aka.ms/WSosstatement), érdemes frissíteni az operációs rendszer, a gép az Azure-bA áttelepítése előtt. 
Feltételesen által támogatott Linux operációs rendszer | Azure csak hozzá támogatást [kijelölt Linux operációsrendszer-verziók](../virtual-machines/linux/endorsed-distros.md), érdemes frissíteni az operációs rendszer, a gép az Azure-bA áttelepítése előtt.
Unendorsed Linux operációs rendszer | A gép előfordulhat, hogy indítsa el az Azure-ban, de nem az operációs rendszer támogatott Azure által biztosított, érdemes frissíteni az operációs rendszer egy [által támogatott Linux verzió](../virtual-machines/linux/endorsed-distros.md) Azure áttelepítése előtt
Az operációs rendszer bitszáma nem támogatott | Virtuális gépek 32 bites operációs rendszer előfordulhat, hogy indítsa el az Azure-ban, de javasoljuk, hogy 32 bites operációs rendszere a virtuális gép frissíthető a 64 bites Azure áttelepítése előtt.
Visual Studio előfizetést igényel. | A gépek rendelkezik egy Windows ügyfél OS belül futó, amely csak a Visual Studio-előfizetéssel támogatott.


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
 

## <a name="vcenter-errors"></a>vCenter hibák

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>Error UnhandledException Internal error occured: System.IO.FileNotFoundException

Ez a gyűjtő verzióin kisebb 1.0.9.5 látott probléma. Ha a gyűjtő verzió 1.0.9.2 vagy előtti-GA verziók 1.0.8.59 hasonlóan, a probléma fognak adódni. Kövesse a [itt részletes válaszhoz fórumokban a megadott hivatkozás](https://social.msdn.microsoft.com/Forums/azure/en-US/c1f59456-7ba1-45e7-9d96-bae18112fb52/azure-migrate-connect-to-vcenter-server-error?forum=AzureMigrate).

[A gyűjtő a probléma megoldásához frissítse](https://aka.ms/migrate/col/checkforupdates).

### <a name="error-unabletoconnecttoserver"></a>Error UnableToConnectToServer

Nem sikerült kapcsolódni a vCenter-kiszolgáló "Servername.com:9443" hiba miatt: nem tudta volna fogadni az üzenetet https://Servername.com:9443/sdk figyel végpont történt.

Ez akkor fordul elő, amikor a gyűjtő számítógép nem tudja feloldani a megadott vCenter-kiszolgáló neve vagy a port speficified nem megfelelő. Alapértelmezés szerint a port nincs megadva, ha adatgyűjtő megpróbál csatlakozni a 443-as port számát.

1. Próbálja a gyűjtő gépről Servername.com pingelését.
2. Ha az 1. lépés sikertelen, próbálja meg IP-cím keresztül csatlakozik a vCenter-kiszolgálóhoz.
3. Azonosítsa a megfelelő port számát a vCenter eléréséhez.
4. Végül ellenőrizze, hogy a vCenter-kiszolgáló működik, és van-e.
 

