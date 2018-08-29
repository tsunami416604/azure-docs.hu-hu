---
title: Az Azure Migrate hibáinak elhárítása |} A Microsoft Docs
description: Ismert problémák az Azure Migrate szolgáltatás és a hibaelhárítási tippek gyakran előforduló hibák áttekintést nyújt.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/25/2018
ms.author: raynew
ms.openlocfilehash: ca34f27e1d22c6235ec0d6b965d49ec5266f17f6
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126364"
---
# <a name="troubleshoot-azure-migrate"></a>Az Azure Migrate hibaelhárítása

## <a name="troubleshoot-common-errors"></a>Gyakori hibák elhárítása

[Az Azure Migrate](migrate-overview.md) felméri a helyszíni számítási feladatokat az Azure-ba való migrálásra. Ez a cikk segítségével üzembe helyezése és az Azure Migrate szolgáltatással kapcsolatos problémák elhárítása.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Migrálási projekt létrehozása nem sikerült hiba *kérelmek felhasználó identitást meghatározó fejléceket kell tartalmaznia.*

A probléma akkor fordulhat elő, a felhasználók számára, akik nem rendelkeznek hozzáféréssel a szervezet Azure Active Directory (Azure AD) bérlőhöz. Amikor a felhasználó első alkalommal adnak hozzá az Azure AD-bérlő, hallgatója kap egy e-mailes meghívó csatlakozzanak a bérlőhöz. Nyissa meg az e-mailt, és fogadja el a meghívást, a bérlő sikeresen hozzáadja a felhasználóknak kell. Ha nem látja az e-mailt, forduljon egy felhasználó, aki már hozzáfér a bérlő, és kérje meg a megadott lépéseket követve a meghívó újraküldése [Itt](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Miután a meghívást tartalmazó e-mail érkezik, nyissa meg az e-mailt, és kattintson a hivatkozásra az e-mailben a meghívás elfogadásához kell. Miután ez megtörtént, jelentkezzen ki az Azure Portalon kell, és jelentkezzen be ismét a böngészőlap frissítése nem fog működni. Majd megpróbálkozhat a migrálási projekt létrehozása.

### <a name="performance-data-for-disks-and-networks-adapters-shows-as-zeros"></a>Lemezek és a hálózat adapterek teljesítményadatokat nullák állapota

Ez akkor fordulhat elő, ha a statisztikai beállítást szintje a vCenter-kiszolgálón kevesebb mint három. Vagy magasabb szintű három vCenter tárolja a virtuális gépek korábbi teljesítménye számítási, tárolási és hálózati. A három szint kisebb, mint a vCenter tárolási és hálózati adatok, de csak a CPU és memória adatok nem tárolja. Ebben a forgatókönyvben teljesítmény adatokat jeleníti meg, mint az Azure Migrate nulla, és az Azure Migrate biztosít a lemezek és a helyszíni gépekről gyűjtött metaadatok alapján hálózatok mérete javaslat.

Ahhoz, hogy a lemez- és teljesítményadatok gyűjtése, módosítsa a statisztikai beállítások szintjét három. Ezt követően legalább egy napot várni a környezet felderítéséhez és értékeléséhez.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>E telepített ügynökök és a függőségek képi megjelenítéséről csoportok létrehozásához használt. Most már feladatátvétel után, a gépek művelet "Ügynök telepítése" helyett "Nézet függőségei" megjelenítése
* POST tervezett vagy nem tervezett feladatátvétel esetén a helyszíni gépek ki vannak kapcsolva, és egyenértékű gépeket hoz létre az Azure-ban. Ezek a gépek egy másik MAC-címet szerezni. Előfordulhat, hogy vásárolnak, egy másik IP-cím alapján, hogy a felhasználó úgy döntött, a helyi IP-cím megőrzése, vagy nem. MAC- és IP-címek különböznek, ha az Azure Migrate a helyszíni gépek nem rendel hozzá a Service Map függőségi adatokat, és kéri a felhasználót, hogy telepítse a helyett függőségek megtekintéséhez ügynököket.
* Feladatátvételi teszt közzététele a helyszíni gépek elvárt bekapcsolva maradjon. Egyenértékű gépeket hoz létre az Azure-ban másik MAC-címet beszerezni, és előfordulhat, hogy másik IP-cím beszerzéséhez. Kivéve, ha a felhasználó blokkolja a kimenő forgalmát a Log Analytics ezeken a gépeken, az Azure Migrate a helyszíni gépek nem rendel hozzá a Service Map függőségi adatokat, és kéri a felhasználót, hogy telepítse a helyett függőségek megtekintéséhez ügynököket.

## <a name="collector-errors"></a>Naplógyűjtők hibái

### <a name="deployment-of-collector-ova-failed"></a>Központi telepítését gyűjtőt OVA nem sikerült

Ha az OVA részlegesen töltődik le, vagy a böngésző miatt ez fordulhat elő a vSphere webes ügyfél telepítése az OVA használatakor. Győződjön meg arról, hogy a letöltés befejeződött, és próbáljon üzembe helyezni egy másik böngésző az OVA.

### <a name="collector-is-not-able-to-connect-to-the-internet"></a>Gyűjtő nem sikerül csatlakozni az internethez

Ez akkor fordulhat elő, ha a gép, használja a rendszer proxy mögött van. Ellenőrizze, hogy az engedélyezési hitelesítő adatok megadása, ha a proxy szüksége van rá.
Ha bármely URL-alapú tűzfalproxyt a kimenő kapcsolat szabályozásához, ügyeljen arra, hogy az engedélyezési listára ezeket a szükséges URL-címeket használ:

**URL-cím** | **Cél**  
--- | ---
*.portal.azure.com | Szükséges ellenőrizze a kapcsolatot az Azure-szolgáltatások, és az időszinkronizálás ellenőrzéséhez bocsát ki.
*.oneget.org | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul.

**A gyűjtő nem tud kapcsolódni az internetre egy tanúsítvány érvényesítési hiba miatt**

Ez akkor fordulhat elő, ha egy lehallgató proxy segítségével csatlakozzon az internethez, és nem importálta a gyűjtő virtuális gép be a proxy tanúsítvány. A proxy tanúsítvány részletes lépésekkel importálhatja [Itt](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity).

**A gyűjtő nem tud kapcsolódni a projekt használatával a projekt Azonosítóját és a kulcs másolt a portálról.**

Győződjön meg arról, hogy másolja, és a megfelelő információt beillesztett. Hibaelhárítás, a Microsoft Monitoring Agent (MMA) telepítése, és győződjön meg arról, ha az MMA módon csatlakozhat a projekt:

1. A gyűjtő virtuális Gépen, töltse le a [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. A telepítés elindításához kattintson duplán a letöltött fájlt.
3. A telepítő a a **üdvözlő** kattintson **tovább**. A **Licencfeltételek** oldalon kattintson az **Elfogadom** gombra a feltételek elfogadásához.
4. A **célmappa**, megtartani, vagy módosítsa az alapértelmezett telepítési mappa > **tovább**.
5. A **ügynök telepítésének beállításai**válassza **Azure Log Analytics** > **tovább**.
6. Kattintson a **Hozzáadás** hozzáadása egy új Log Analytics-munkaterületet. Illessze be a projekt Azonosítóját és kulcsát, akkor a vágólapra másolt. Ezután kattintson a **Next** (Tovább) gombra.
7. Győződjön meg arról, hogy az ügynök kapcsolódik a projekthez. Ha nem érhető el, ellenőrizze a beállításokat. Ha az ügynök kapcsolódik, de a gyűjtő nem, forduljon az ügyfélszolgálathoz.


### <a name="error-802-date-and-time-synchronization-error"></a>802. hiba: Dátum és idő szinkronizálási hiba

A kiszolgáló óra lehet, hogy ki a szinkronizálást, az aktuális idő szerint több mint öt perc alatt. A gyűjtő virtuális Gépen, a következőképpen egyezik az aktuális idő, az idő módosítása:

1. Nyisson meg egy rendszergazdai jogú parancssort a virtuális gépen.
2. Ellenőrizze az időzónát, futtassa a w32tm /tz.
3. Az idő szinkronizálása w32tm/resync futtassa.

### <a name="vmware-powercli-installation-failed"></a>A VMware PowerCLI telepítése nem sikerült

Az Azure Migrate collector PowerCLI letölti és telepíti azt a készüléket. Hiba történt a PowerCLI PowerCLI adattár nem érhető el végpontok okozhatja. A hiba elhárításához próbálja meg manuális telepítését a gyűjtő virtuális Gépen a következő lépés a PowerCLI:

1. Nyissa meg a Windows Powershellt rendszergazdai módban
2. Nyissa meg a könyvtár C:\ProgramFiles\ProfilerService\VMWare\Scripts\
3. Futtassa a szkriptet InstallPowerCLI.ps1

### <a name="error-unhandledexception-internal-error-occured-systemiofilenotfoundexception"></a>Unhandledexception hiba történt: System.IO.FileNotFoundException

Ez a hiba a gyűjtő 1.0.9.5-ösnél korábbi verzióiban jelentkezik. Ha a gyűjtő 1.0.9.2-es vagy a GA előtti, például az 1.0.8.59-es verzióját használja, ez a probléma elő fog fordulni. [Részletes válaszokért lásd a fórumokra mutató hivatkozást](https://social.msdn.microsoft.com/Forums/azure/en-US/c1f59456-7ba1-45e7-9d96-bae18112fb52/azure-migrate-connect-to-vcenter-server-error?forum=AzureMigrate).

[A probléma felszámolásához frissítse a gyűjtőt](https://aka.ms/migrate/col/checkforupdates).

### <a name="error-unabletoconnecttoserver"></a>UnableToConnectToServer hiba

Nem lehet csatlakozni a vCenter-kiszolgáló "Servername.com:9443" hiba miatt: nincsenek végpont a következő volt https://Servername.com:9443/sdk , amely fogadni tudta az üzenetet.

Ha Ön a gyűjtőberendezés legújabb verzióját futtatja, ha nem, frissítse a berendezés ellenőrizze a [legújabb verzió](https://docs.microsoft.com/azure/migrate/concepts-collector#how-to-upgrade-collector).

Ha a probléma továbbra is történik a legújabb verzió, annak oka az lehet, mert a naplógyűjtő gépen nem tudja feloldani a vCenter-kiszolgáló neve vagy a megadott megadott port helytelen. Alapértelmezés szerint ha a port nincs megadva, collector megpróbálja szeretne csatlakozni a 443-as portszámhoz.

1. Próbálja meg a servername.com a naplógyűjtő gépen.
2. Ha az 1. lépés meghiúsul, próbáljon meg az IP-címen keresztül csatlakozni a vCenter-kiszolgálóhoz.
3. Adja meg a megfelelő portszámot a vCenterhez történő csatlakozáshoz.
4. Végezetül pedig ellenőrizze, hogy a vCenter-kiszolgáló fut-e.

## <a name="troubleshoot-readiness-issues"></a>Készültségi problémák elhárítása

**A probléma** | **Fix**
--- | ---
Nem támogatott rendszerindítási típus | Azure virtuális gépek nem támogatja az EFI rendszerindítás-típus. Javasoljuk, hogy a rendszerindítási típus átalakítása BIOS-ban, az áttelepítés futtatása előtt. <br/><br/>Használhat [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) ilyen virtuális gépek a migrálás elvégzésének, ahogy, konvertálja a rendszerindítási típus, a virtuális gép BIOS-ban az áttelepítés során.
Feltételesen támogatott Windows operációs rendszer | Az operációs rendszer támogatása megszűnt a letelt, és szüksége van egy egyéni támogatási megállapodás (CSA) szükséges [támogatja az Azure-ban](https://aka.ms/WSosstatement), fontolja meg az operációs rendszer frissítését az Azure-ba való migrálás előtt.
Nem támogatott Windows operációs rendszer | Az Azure támogatja a csak [Windows operációsrendszer-verziók kiválasztott](https://aka.ms/WSosstatement), fontolja meg a gép operációs rendszerének frissítését az Azure-ba való migrálás előtt.
Feltételesen jóváhagyott Linux operációs rendszer | Az Azure csak vannak jóváhagyva [Linux operációsrendszer-verziók kiválasztott](../virtual-machines/linux/endorsed-distros.md), fontolja meg a gép operációs rendszerének frissítését az Azure-ba való migrálás előtt.
Nem jóváhagyott Linux operációs rendszer | A gép elindulhat az Azure-ban, de nem az operációs rendszer biztosít támogatást az Azure-ban, fontolja meg az operációs rendszer, hogy egy [által támogatott Linux-verzió](../virtual-machines/linux/endorsed-distros.md) az Azure-ba való migrálás előtt
Ismeretlen operációs rendszer | Az operációs rendszer a virtuális gép vCenter-kiszolgáló, amely miatt az Azure Migrate nem tudja azonosítani az Azure készen áll-e a virtuális gép mint az "Egyéb" van megadva. Győződjön meg arról, hogy az a gépen futó operációs rendszer [támogatott](https://aka.ms/azureoslist) az Azure-ban, a gép áttelepítése előtt.
Az operációs rendszer bitszáma nem támogatott | Az operációs rendszer 32-bit-es virtuális gép elindulhat az Azure-ban, de javasoljuk, hogy a virtuális gép operációs rendszerének frissítése a 32 bites, 64 bites az Azure-ba való migrálás előtt.
A Visual Studio-előfizetést igényel. | A gépek rendelkezik egy Windows ügyfél operációs rendszer fut, amely csak Visual Studio-előfizetés használata támogatott.
Nem található a szükséges tárolási teljesítménynek megfelelő virtuális gép. | A tárolási teljesítményt (IOPS és átviteli sebesség) a gép meghaladja az Azure virtuális gépek támogatása szükséges. Csökkenti a tárolási követelményeket a gép áttelepítés előtt.
A virtuális gép nem található a szükséges hálózati teljesítmény. | A hálózati teljesítmény (bejövő és kimenő), a gép szükséges meghaladja az Azure virtuális gépek támogatása. Csökkentheti a hálózati követelmények, a gép.
Nem található a megadott tarifacsomagnak megfelelő virtuális gép. | A tarifacsomag beállítása Standard lesz, ha csökkenteni a virtuális gép az Azure-ba való migrálás előtt. Ha a méretezési réteg alapszintű, fontolja meg az értékelés tarifacsomagját módosítja a standard.
A virtuális gép nem található a megadott helyen. | Használjon egy másik célhelyet az áttelepítés előtt.
Egy vagy több lemez. | Egy vagy több lemezt a virtuális Géphez csatlakoztatott Azure-követelményeknek nem felel meg. A virtuális géphez csatlakoztatott összes lemez győződjön meg arról, hogy a lemez mérete < 4 TB-os, ha nem, a lemez mérete csökkentheti az Azure-ba való migrálás előtt. Győződjön meg arról, hogy egyes lemezek által igényelt teljesítményt (IOPS és átviteli sebesség) az Azure által támogatott [a virtuális gép lemezeinek felügyelt](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits).   
Egy vagy több hálózati adapter nem megfelelő. | Távolítsa el a használaton kívüli hálózati adapterek a gépről a migrálás előtt.
A lemezek száma meghaladja a korlátot | A fel nem használt lemezek eltávolítása a gépről a migrálás előtt.
A lemezméret meghaladja a korlátot | Az Azure támogatja a lemezek mérete legfeljebb 4 TB-ig. Lemezek csökkentheti az áttelepítés előtt kevesebb mint 4 TB-ig.
A lemez nem érhető el a megadott helyen | Ellenőrizze, hogy a lemez a célhelyen áttelepítése előtt.
A lemez a megadott redundanciával nem érhető el | A lemez a redundancia tárolótípus meghatározott az értékelési beállítások (alapértelmezés szerint LRS) kell használnia.
Belső hiba miatt nem sikerült meghatározni a lemez alkalmasságát | Próbálja meg a csoport egy új értékelés létrehozása.
Nem található a szükséges magokkal és memóriával rendelkező virtuális gép | Az Azure a megfelelő VM-típus nem lehetett rendben. Csökkentse a memória és magok a helyi gép áttelepítése előtt.
Nem sikerült meghatározni a virtuális gép alkalmasságát belső hiba miatt. | Próbálja meg a csoport egy új értékelés létrehozása.
Nem sikerült meghatározni a belső hiba miatt egy vagy több lemez alkalmasságát. | Próbálja meg a csoport egy új értékelés létrehozása.
Nem sikerült meghatározni a belső hiba miatt egy vagy több hálózati adapter alkalmasságát. | Próbálja meg a csoport egy új értékelés létrehozása.


## <a name="collect-logs"></a>Naplók gyűjtése

**Hogyan gyűjthet naplókat a gyűjtő virtuális Gépen?**

Alapértelmezés szerint a naplózás engedélyezve van. Naplók az alábbi helyeken találhatók:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Esemény nyomkövetése for Windows gyűjteni, tegye a következőket:

1. A gyűjtő virtuális Gépen nyisson meg egy PowerShell parancsablakot.
2. Futtatás **Get-Eseménynapló - naplónév alkalmazás |} exportálás csv-eventlog.csv**.

**Hogyan gyűjtenek portál hálózati forgalmi naplók?**

1. Nyissa meg a böngészőt, és keresse meg, és jelentkezzen be [a Portal](https://portal.azure.com).
2. Nyomja le az F12 billentyűt a fejlesztői eszközök elindítása. Ha szükséges, törölje a jelet a beállítás **navigációs bejegyzések törlése**.
3. Kattintson a **hálózati** lapra, és indítsa el a hálózati forgalom rögzítése:
 - Válassza ki a Chrome-ban, **Preserve log**. A rögzítés automatikusan elindul. A piros kör azt jelzi, hogy a forgalom rögzítése folyamatban van. Ha nem jelenik meg, kattintson a fekete körre indítása
 - Az Edge/IE rögzítés automatikusan elindul. Ha nem, kattintson a zöld lejátszás gombra.
4. Próbálja meg reprodukálni a hibát.
5. Korábban észlelt rögzítése közben a hiba, miután Rögzítés leállítása, és mentse a rögzített tevékenység másolatát:
 - A Chrome-ban, kattintson a jobb gombbal, és kattintson a **Mentés másként HAR tartalommal**. Ez zips alkalmazást, és exportálja a naplók .har fájl.
 - Az Edge/IE, kattintson a **exportálási rögzített forgalom** ikonra. Ez tömörít, és exportálja a naplót.
6. Keresse meg a **konzol** lapot, és ellenőrizze az összes figyelmeztetést és hibát. A konzol naplófájljának mentése:
 - A Chrome-ban kattintson a jobb gombbal bárhol a konzol naplóban. Válassza ki **Mentés másként**, hogy az exportálás és a napló zip.
 - Az Edge/IE, kattintson a jobb gombbal a hibákat, és válassza **másolhatja az összes**.
7. Zárja be a fejlesztői eszközöket.

## <a name="collector-error-codes-and-recommended-actions"></a>Gyűjtő hibakódok és ajánlott műveletek

|           |                                |                                                                               |                                                                                                       |                                                                                                                                            |
|-----------|--------------------------------|-------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Hibakód | Hiba neve                      | Üzenet                                                                       | Lehetséges okok                                                                                        | Javasolt művelet                                                                                                                          |
| 601       | CollectorExpired               | A gyűjtő érvényessége lejárt.                                                        | A gyűjtő érvényessége lejárt.                                                                                    | Töltse le a gyűjtő egy új verzióját, majd próbálkozzon újra.                                                                                      |
| 751       | UnableToConnectToServer        | Nem sikerült csatlakozni a(z) %Name; vCenter Server-kiszolgálóhoz a következő hiba miatt: %ErrorMessage;.     | További részletekért tekintse meg a hibaüzenetet.                                                             | Hárítsa el a problémát, és próbálkozzon újra.                                                                                                           |
| 752       | InvalidvCenterEndpoint         | A(z) %Name; nem vCenter Server-kiszolgáló.                                  | Adja meg a VCenter Server-kiszolgáló adatait.                                                                       | Próbálja megismételni a műveletet helyes vCenter Server-kiszolgálói adatokkal.                                                                                   |
| 753       | InvalidLoginCredentials        | Nem sikerült csatlakozni a(z) %Name; vCenter Server-kiszolgálóhoz a következő hiba miatt: %ErrorMessage;. | Nem sikerült csatlakozni a vCenter Server-kiszolgálóhoz, mert a bejelentkezési hitelesítő adatok érvénytelenek.                             | Győződjön meg arról, hogy a megadott bejelentkezési hitelesítő adatok helyesek.                                                                                    |
| 754       | NoPerfDataAvaialable           | Nem érhetők el a teljesítményadatok.                                               | A vCenter Server statisztikájának szintjét ellenőrzése Teljesítményadatok érhető el a 3-ra kell állítani. | Módosítsa a statisztika szintjét 3-asra (5 perces, 30 perces és 2 órás időtartamhoz), és próbálkozzon legalább egy napos várakozás után.                   |
| 756       | NullInstanceUUID               | A rendszer olyan gépet észlelt, mely példányának UUID azonosítója null értékű                                  | Lehetséges, hogy a vCenter Server-kiszolgáló nem megfelelő objektummal rendelkezik.                                                      | Hárítsa el a problémát, és próbálkozzon újra.                                                                                                           |
| 757       | VMNotFound                     | A virtuális gép nem található                                                  | Lehetséges, hogy a virtuális gépet törölték: %VMID;                                                                | Győződjön meg arról, hogy a vCenter-készlet hatókörének beállításakor kiválasztott virtuális gépek léteznek az észleléskor                                      |
| 758       | GetPerfDataTimeout             | A VCenter-kérés túllépte az időkorlátot. Üzenet: % Message;                                  | A vCenter Server-kiszolgáló hitelesítő adatai helytelenek                                                              | Ellenőrizze a vCenter Server hitelesítő adatait, és győződjön meg arról, hogy a vCenter Server elérhető. Próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz. |
| 759       | VmwareDllNotFound              | A VMWare.Vim DLL-fájlja nem található.                                                     | A PowerCLI nincs megfelelően telepítve.                                                                   | Ellenőrizze, ha a PowerCLI megfelelően van-e telepítve. Próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz.                               |
| 800       | ServiceError                   | Az Azure Migrate Collector szolgáltatás nem fut.                               | Az Azure Migrate Collector szolgáltatás nem fut.                                                       | A Services.msc használatával indítsa el a szolgáltatást, majd próbálja megismételni a műveletet.                                                                             |
| 801       | PowerCLIError                  | A VMware PowerCLI telepítése nem sikerült.                                          | A VMware PowerCLI telepítése nem sikerült.                                                                  | Próbálja megismételni a műveletet. Ha a probléma tartósan fennáll, telepítse manuálisan, és próbálja megismételni a műveletet.                                                   |
| 802       | TimeSyncError                  | Az idő nincs internetes időkiszolgálóval szinkronizálva.                            | Az idő nincs internetes időkiszolgálóval szinkronizálva.                                                    | Győződjön meg arról, hogy a számítógépen az időzónájának megfelelően van beállítva az idő, majd próbálja megismételni a műveletet.                                 |
| 702       | OMSInvalidProjectKey           | A megadott projektkulcs érvénytelen.                                                | A megadott projektkulcs érvénytelen.                                                                        | Próbálja megismételni a műveletet a megfelelő projektkulccsal.                                                                                              |
| 703       | OMSHttpRequestException        | Hiba történt a kérés küldése. Üzenet: % Message;                                | Ellenőrizze a projekt azonosítóját és kulcsát, és győződjön meg arról, hogy a végpont elérhető.                                       | Próbálja megismételni a műveletet. Ha nem szűnik meg a probléma, forduljon a Microsoft támogatási szolgálatához.                                                                     |
| 704       | OMSHttpRequestTimeoutException | HTTP-kérés túllépte az időkorlátot. Üzenet: % Message;                                     | Ellenőrizze, hogy a projektazonosító és -kulcs helyes-e, és győződjön meg róla, hogy a végpont olvasható.                                       | Próbálja megismételni a műveletet. Ha nem szűnik meg a probléma, forduljon a Microsoft támogatási szolgálatához.                                                                     |
