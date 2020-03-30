---
title: Teljesítménydiagnosztika az Azure virtuális gépekhez| Microsoft dokumentumok
description: Bemutatja az Azure Performance Diagnostics for Windows.Introduces Azure Performance Diagnostics for Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 16be3d1695608165405a3490b686a01ba6a2a62c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70080604"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Azure-beli virtuális gépek teljesítménydiagnosztikája

A teljesítménydiagnosztikai eszköz segítségével elháríthatja a Windows vagy Linux virtuális gépeket (VM) érintő teljesítményproblémákat. A támogatott hibaelhárítási forgatókönyvek közé tartozik az ismert problémák és ajánlott eljárások gyors ellenőrzése, valamint a virtuális gépek lassú teljesítményével vagy a processzor, a lemezterület vagy a memória magas használatával kapcsolatos összetett problémák.

A teljesítménydiagnosztikát közvetlenül az Azure Portalon futtathatja, ahol áttekintést kaphat a különböző naplókról, a gazdag konfigurációról és a diagnosztikai adatokról. Javasoljuk, hogy futtassa a teljesítménydiagnosztikát, és tekintse át az elemzési és diagnosztikai adatokat, mielőtt kapcsolatba lépne a Microsoft támogatási szolgálatával.

> [!NOTE]
> Windows esetén a teljesítménydiagnosztika jelenleg támogatott a .NET SDK 4.5-ös vagy újabb verziójával telepített virtuális gépeken. A teljesítménydiagnosztika futtatásához szükséges lépéseket a klasszikus virtuális gépeken az [Azure Performance Diagnostics virtuálisgép-bővítmény című témakörben.](performance-diagnostics-vm-extension.md)

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

### <a name="windows"></a>Windows

Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012 Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

### <a name="linux"></a>Linux

Oracle Linux Server`*`6.10 [ ], 7.3, 7.6, 7.5 (Oracle-Database-Ee 13.8 marketplace image), CentOS 6.5 [`*`], 7.6, RHEL 7.2, 7.5, 8.0 [`*`], Ubuntu 14.04, 16.04, 18.04, Debian 8, 9, 10 [`*`], SLES 12 SP4 ]`*`

>[!Note]
>[`*`] Kérjük, olvassa el [az ismert problémákat](how-to-use-perfinsights-linux.md#known-issues)

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Teljesítménydiagnosztika telepítése és futtatása a virtuális számítógépen

A teljesítménydiagnosztika egy PerfInsights nevű diagnosztikai eszközt futtató virtuálisgép-bővítményt telepít. A PerfInsights [Windows](https://aka.ms/perfinsights) és [Linux](https://aka.ms/perfinsightslinux)rendszeren is elérhető. A teljesítménydiagnosztika telepítéséhez és futtatásához hajtsa végre az alábbi lépéseket:

1. A parancsok bal oldali oszlopában válassza a **Virtuális gépek**lehetőséget.
1. A virtuális gépnevek listájából válassza ki azt a virtuális gép, amelyen diagnosztikát szeretne futtatni.
1. A parancsok jobb oldali oszlopában válassza a **Teljesítménydiagnosztika**lehetőséget.

    ![Képernyőkép az Azure Portalról, kiemelve a Teljesítménydiagnosztika telepítése gombot](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > Ezen a képernyőképen a virtuális gépnevek panelje rejtve van.
1. Tárfiók kiválasztása (nem kötelező)

    Ha egyetlen tárfiókot szeretne használni több virtuális gép teljesítménydiagnosztikai eredményeinek tárolásához, az eszköztár **Beállítások** gombjára kattintva választhat ki egy tárfiókot. Kattintson az **OK** gombra, miután kiválasztotta a tárfiókot.

    Ha nem ad meg tárfiókot, alapértelmezés szerint új tárfiók jön létre.

    ![Képernyőkép a Teljesítménydiagnosztika panelről, kiemelve a Beállítások eszköztár gombját](media/performance-diagnostics/settings-button.png)

    ![Képernyőkép a tárfiók beállításáról a Teljesítménydiagnosztika beállításai panelen](media/performance-diagnostics/select-storage-account.png)

1. Válassza a **Teljesítménydiagnosztika telepítése gombot.**
1. Jelölje be a **Diagnosztika futtatása jelölőnégyzetet,** ha a telepítés befejezése után szeretne diagnosztikát futtatni. Ha ezt a beállítást választja, kiválaszthatja a teljesítményelemzési forgatókönyvet és a kapcsolódó beállításokat.

    ![Képernyőkép: Teljesítménydiagnosztika telepítése gomb](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>A futtatni kívánt elemzési forgatókönyv kiválasztása

A következő elemzési forgatókönyvek érhetők el az Azure Portalon. Válasszon ki egy elemzést, a ttól függően, hogy a teljesítmény probléma, hogy van. Válassza ki az elemzéshez szükséges időtartam- és nyomkövetési beállításokat.

* **Gyors teljesítményelemzés**  
    Ismert problémák at keres, elemzi az ajánlott eljárásokat, és diagnosztikai adatokat gyűjt. Ez az elemzés több percet vesz igénybe. További információ [Windows](https://aka.ms/perfinsights/quick) vagy [Linux](https://aka.ms/perfinsightslinux/quick)

* **Teljesítményelemzés**  
    Tartalmazza az összes ellenőrzést a gyors teljesítmény elemzés és figyeli a magas erőforrás-felhasználás. Ezzel a verzióval elháríthatja az általános teljesítménybeli problémákat, például a magas processzor-, memória- és lemezhasználatot. Ez az elemzés a kiválasztott időtartamtól függően 30 másodperctől 15 percig tart. További információ [Windows](https://aka.ms/perfinsights/vmslow) vagy [Linux](https://aka.ms/perfinsightslinux/vmslow)

* **Speciális teljesítményelemzés**`*`  
    Tartalmazza a teljesítményelemzés összes ellenőrzését, és összegyűjti a nyomkövetéseket a következő szakaszokban felsoroltak szerint. Ebben a forgatókönyvben elháríthatja a további nyomkövetéseket igénylő összetett problémákat. Ebben a forgatókönyvben hosszabb ideig futtatja a diagnosztikai kimenet teljes méretét, a virtuális gép méretétől és a kiválasztott nyomkövetési beállításoktól függően. Ez az elemzés a kiválasztott időtartamtól függően 30 másodperctől 15 percig tart. [További információ](https://aka.ms/perfinsights/advanced)

* **Azure-fájlok elemzése**`*`  
    Tartalmazza a teljesítményelemzés összes ellenőrzését, valamint a hálózati nyomkövetést és az SMB-számlálókat. Ebben a forgatókönyvben az Azure-fájlok teljesítményének hibaelhárítása. Ez az elemzés a kiválasztott időtartamtól függően 30 másodperctől 15 percig tart. [További információ](https://aka.ms/perfinsights/azurefiles)

>[!Note]
>[`*`] Ezek az elemzési forgatókönyvek csak a Windows rendszerben támogatottak.

![Képernyőkép: A Diagnosztika futtatása panel a Teljesítménydiagnosztika panelen](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Tünetek biztosítása (nem kötelező)

Jelölje ki az előre kiválasztott jelenségeket a listából, vagy adjon hozzá új jelenségeket. Ez segít abban, hogy a jövőben javítsuk az elemzést.

### <a name="provide-support-request-number-if-available-optional"></a>Támogatási kérelem számának megadása, ha rendelkezésre áll (nem kötelező)

Ha egy microsoftos támogatási szakemberrel dolgozik egy meglévő támogatási jegyen, adja meg a támogatási jegy számát.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Tekintse át az adatvédelmi szabályzatot és a jogi feltételeket, és jelölje be a nyugtázás (kötelező) jelölőnégyzetet.

A diagnosztika futtatásához el kell fogadnia a jogi feltételeket, és el kell fogadnia az adatvédelmi szabályzatot.

### <a name="select-ok-to-run-the-diagnostics"></a>A diagnosztika futtatásához válassza az OK gombot.

Egy értesítés jelenik meg a teljesítménydiagnosztika telepítésének megkezdésekor. A telepítés befejezése után megjelenik egy értesítés, amely jelzi, hogy a telepítés sikeres. A kiválasztott elemzés ezután a megadott időtartamra fut. Ez jó alkalom a teljesítményprobléma reprodukálására, hogy a diagnosztikai adatok a megfelelő időben rögzíthetők legyenek.

Az elemzés befejezése után a következő elemek et tölti fel az Azure-táblák és egy bináris nagy méretű objektum (BLOB) tároló a megadott tárfiókban:

* A futtatással kapcsolatos összes elemzés és kapcsolódó információ
* Kimeneti tömörített (.zip) fájl **(PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip)** Windows rendszeren és egy kátránygolyós fájl **(PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.tar.gz)** Linuxon, amely naplófájlokat tartalmaz
* HTML-jelentés

A feltöltés után egy új diagnosztikai jelentés jelenik meg az Azure Portalon.

![Képernyőkép a diagnosztikai jelentés listájáról a Teljesítménydiagnosztika panelen](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>A teljesítménydiagnosztikai beállítások módosítása

A **Beállítások** eszköztár gombbal módosíthatja azt a tárfiókot, ahol a diagnosztikai elemzések és kimenet tárolhatók. Használhatja ugyanazt a tárfiókot több virtuális gépek, amelyek teljesítménydiagnosztika. A tárfiók módosításakor a régi jelentések és elemzések nem törlődnek. Ezek azonban a továbbiakban nem jelennek meg a diagnosztikai jelentések listájában.

## <a name="review-insights-and-performance-diagnostics-report"></a>Elemzési és teljesítménydiagnosztikai jelentés áttekintése

Minden diagnosztikai futtatás tartalmazza az elemzéseket és javaslatokat, az érintett erőforrásokat, naplófájlokat és más gazdag diagnosztikai információkat, valamint egy jelentést kapcsolat nélküli megtekintésre. Az összes összegyűjtött diagnosztikai adat teljes listáját a [Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) **PerfInsights milyen adatokat gyűjt?** [Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights)

### <a name="select-a-performance-diagnostics-report"></a>Teljesítménydiagnosztikai jelentés kiválasztása

A diagnosztikai jelentéslista segítségével megkeresheti az összes futtatott diagnosztikai jelentést. A lista tartalmazza a felhasznált elemzés részleteit, a talált elemzéseket és azok hatásszintjeit. A további részletek megtekintéséhez jelöljön ki egy sort.

![Képernyőkép: diagnosztikai jelentés kiválasztása a Teljesítménydiagnosztika panelből](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Teljesítménydiagnosztikai jelentés áttekintése

Minden teljesítménydiagnosztikai jelentés több elemzési adatokat is tartalmazhat, és magas, közepes vagy alacsony hatásszintet jelezhet. Minden egyes betekintés ajánlásokat is tartalmaz, hogy segítsen csökkenti az aggodalmat. Az elemzések csoportosítva vannak a könnyű szűrés érdekében.

A hatásszintek teljesítményproblémák lehetőségét jelentik olyan tényezők alapján, mint a helytelen konfiguráció, az ismert problémák vagy a más felhasználók által jelentett problémák. Lehet, hogy még nem tapasztal egy vagy több felsorolt problémát. Előfordulhat például, hogy az SQL naplófájlok és az adatbázisfájlok ugyanazon az adatlemezen vannak. Ez a feltétel nagy potenciállal rendelkezik a szűk keresztmetszetek és egyéb teljesítményproblémák, ha az adatbázis-használat magas, mivel előfordulhat, hogy nem veszi észre a problémát, ha a használat alacsony.

![Képernyőkép a Teljesítménydiagnosztikai jelentés áttekintéséről](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>A teljesítménydiagnosztika elemzési adatainak és ajánlásainak áttekintése

Kiválaszthat egy betekintést az érintett erőforrásokkal, a javasolt kockázatcsökkentésekkel és a hivatkozási hivatkozásokkal kapcsolatos további részletek megtekintéséhez.

![Képernyőkép a Teljesítménydiagnosztika betekintési részleteiről](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>A teljes teljesítménydiagnosztikai jelentés letöltése és áttekintése

A Jelentés **letöltése** gombbal olyan HTML-jelentést tölthet le, amely további gazdag diagnosztikai információkat tartalmaz, például tárolási és hálózati konfigurációt, teljesítményszámlálókat, nyomkövetéseket, folyamatok listáját és naplókat. A tartalom a kiválasztott elemzéstől függ. A speciális hibaelhárításhoz a jelentés további információkat és interaktív diagramokat tartalmazhat, amelyek a magas processzorhasználattal, a magas lemezhasználattal és a túlzott memóriát használó folyamatokkal kapcsolatosak. A teljesítménydiagnosztikai jelentésről további információt a [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) vagy a Linux című témakörben [talál.](how-to-use-perfinsights-linux.md#review-the-diagnostics-report)

## <a name="manage-performance-diagnostics-reports"></a>Teljesítménydiagnosztikai jelentések kezelése

Egy vagy több teljesítménydiagnosztikai jelentést a **Jelentés törlése** gombbal törölhet.

## <a name="how-to-uninstall-performance-diagnostics"></a>A teljesítménydiagnosztika eltávolítása

A teljesítménydiagnosztika eltávolítható a virtuális gépről. Ez a művelet eltávolítja a virtuálisgép-bővítményt, de nem befolyásolja a tárfiókban lévő diagnosztikai adatokat.

![Képernyőkép a Teljesítménydiagnosztika panel eszköztáráról, amelyen kiemelt Eltávolítás gomb van kiemelve](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Hol vannak a virtuális gép diagnosztikai adatai?

Minden teljesítménydiagnosztikai elemzés és jelentés a saját tárfiókjában tárolódik. Az elemzési adatok az Azure-táblákban tárolódnak. A jelentések tömörített fájlja egy azdiagextnresults nevű bináris nagy objektumtárolóban (BLOB) van tárolva.

A tárfiók adatait az eszköztár Beállítások gombjával tekintheti meg.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Hogyan oszthatom meg ezeket az adatokat a Microsoft ügyfélszolgálatával?

A diagnosztikai jelentés többféleképpen is megoszthatja a Microsofttal.

**1. lehetőség:** A legutóbbi jelentés automatikus megosztása  
Amikor megnyit egy támogatási jegyet a Microsofttal, fontos megosztani a teljesítménydiagnosztikai jelentést. Ha a diagnosztika futtatása közben úgy döntött, hogy megosztja ezeket az adatokat a Microsofttal (a**diagnosztikai adatok Microsofttal való megosztásával"** jelölőnégyzet bejelölésével, a Microsoft a futtatás dátumától számított legfeljebb 30 napig a kimeneti zip-fájlra mutató SAS-hivatkozás sal érheti el a jelentést. Csak a legfrissebb jelentés érhető el a támogatási mérnök számára.

**2. lehetőség:** Megosztott hozzáférési aláírás létrehozása a diagnosztikai jelentés tömörített fájljához  
A jelentésekben tömörített fájlra mutató hivatkozást megosztott hozzáférési aláírások használatával is megoszthatja. Ehhez kövesse az alábbi lépéseket:

1. Az Azure Portalon keresse meg azt a tárfiókot, amelyben a diagnosztikai adatok tárolása.
1. Válassza **a Blobok lehetőséget** a Blob **szolgáltatás** szakaszban.
1. Válassza ki az **azdiagextnresults** tárolót.
1. Válassza ki a megosztani kívánt Teljesítménydiagnosztika kimeneti tömörített fájlt.
1. A **SAS létrehozása** lapon válassza ki a megosztási feltételeket.
1. Kattintson **a Blob SAS-token és URL létrehozása elemre.**
1. Másolja a **Blob SAS URL-címét,** és ossza meg a támogatási szakemberrel.

**3. lehetőség:** A jelentés letöltése a tárfiókból

A teljesítménydiagnosztika jelentés tömörített fájlját a 2. Jelölje be, ha le szeretné tölteni a fájlt, majd megoszthatja e-mailben, vagy kérje meg a támogatási szakembertől a fájl feltöltésével kapcsolatos utasításokat.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Hogyan rögzíthetem a diagnosztikai adatokat a megfelelő időben?

Minden teljesítménydiagnosztika futtatása két szakaszból áll:

1. Telepítse vagy frissítse a teljesítménydiagnosztikai virtuálisgép-bővítményt.
1. Futtassa a diagnosztikát a megadott időtartamra.

Jelenleg nincs egyszerű módja annak, hogy pontosan tudja, mikor a virtuális gép bővítmény telepítése befejeződött. Általában körülbelül 45 másodperc1 perc ig a virtuális gép bővítmény telepítése. A virtuális gép bővítmény telepítése után futtathatja a repro lépéseket, hogy a teljesítménydiagnosztika rögzítse a megfelelő adathalmazt a hibaelhárításhoz.

## <a name="next-steps"></a>További lépések

Miután áttekintette a teljesítménydiagnosztikai elemzéseket és a jelentést, ha továbbra sem tudja megállapítani a probléma okát, és további segítségre van szüksége, megnyithat egy támogatási jegyet a Microsoft ügyfélszolgálatával.

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/)és válassza **a Támogatás beszereznie**lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.
