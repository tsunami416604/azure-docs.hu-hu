---
title: Teljesítmény-diagnosztika az Azure Virtual Machines szolgáltatáshoz | Microsoft Docs
description: Bemutatja a Windows rendszerhez készült Azure Performance Diagnosticst.
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
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080604"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Azure-beli virtuális gépek teljesítménydiagnosztikája

A teljesítmény-diagnosztika eszköz segítséget nyújt a Windows vagy Linux rendszerű virtuális gépekre (VM) hatással lehet a teljesítménnyel kapcsolatos problémák elhárítására. A támogatott hibaelhárítási forgatókönyvek közé tartozik az ismert problémák és az ajánlott eljárások gyors ellenőrzése, valamint a lassú virtuális gépek teljesítményével vagy a processzor, a lemezterület vagy a memória magas kihasználtságával kapcsolatos összetett problémák.

A teljesítmény-diagnosztika közvetlenül a Azure Portal futtatható, ahol áttekintheti az elemzéseket és a jelentéseket a különböző naplók, gazdag konfigurációk és diagnosztikai adatok használatával. Javasoljuk, hogy a teljesítmény-diagnosztika futtatása előtt tekintse át az elemzéseket és a diagnosztikai adatait, mielőtt felvette a kapcsolatot Microsoft ügyfélszolgálata.

> [!NOTE]
> Windows rendszeren a teljesítmény-diagnosztika jelenleg támogatott olyan virtuális gépeken, amelyeken a .NET SDK 4,5-as vagy újabb verziója van telepítve. A teljesítmény-diagnosztika klasszikus virtuális gépeken való futtatásának lépéseiért lásd: [Azure Performance Diagnostics VM-bővítmény](performance-diagnostics-vm-extension.md).

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

### <a name="windows"></a>Windows

Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8,1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 standard, Windows Server 2012 Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 standard.

### <a name="linux"></a>Linux

Oracle Linux Server 6,10 [`*`], 7,3, 7,6, 7,5 (Oracle-Database-EE 13,8 Piactéri rendszerkép), CentOS 6,5`*`[], 7,6, RHEL 7,2, 7,5, 8,0`*`[], Ubuntu 14,04, 16,04, 18,04, Debian 8, 9, 10`*`[], SLES 12 SP4 [`*`]

>[!Note]
>[`*`] Tekintse meg az [ismert problémákat](how-to-use-perfinsights-linux.md#known-issues)

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>A teljesítmény-diagnosztika telepítése és futtatása a virtuális gépen

A teljesítmény-diagnosztika olyan virtuálisgép-bővítményt telepít, amely egy PerfInsights nevű diagnosztikai eszközt futtat. A PerfInsights [Windows](https://aka.ms/perfinsights) és [Linux](https://aka.ms/perfinsightslinux)rendszereken egyaránt elérhető. A teljesítmény-diagnosztika telepítéséhez és futtatásához kövesse az alábbi lépéseket:

1. A parancsok bal oldali oszlopában válassza a **virtuális gépek**lehetőséget.
1. A virtuális gépek neveinek listájából válassza ki azt a virtuális gépet, amelyen diagnosztikát kíván futtatni.
1. A parancsok jobb oldali oszlopában válassza a **teljesítmény diagnosztika**elemet.

    ![Képernyőkép a Azure Portalről, a Performance Diagnostics telepítése gomb kiemelve](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > Ezen a képernyőfelvételen a virtuális gépek neveinek panelje rejtett.
1. Válasszon Storage-fiókot (nem kötelező)

    Ha egyetlen Storage-fiókot szeretne használni több virtuális gép teljesítmény-diagnosztikai eredményeinek tárolásához, akkor az eszköztár **Beállítások** gombjára kattintva kiválaszthatja a Storage-fiókot. A Storage-fiók kiválasztása után kattintson az **OK** gombra.

    Ha nem ad meg Storage-fiókot, a rendszer alapértelmezés szerint új Storage-fiókot hoz létre.

    ![Képernyőfelvétel a teljesítmény-diagnosztika panelről, a Beállítások eszköztár gombjának kiemelésével](media/performance-diagnostics/settings-button.png)

    ![Képernyőfelvétel a Storage-fiók kiválasztásáról a teljesítmény-diagnosztika beállításai panelen](media/performance-diagnostics/select-storage-account.png)

1. Válassza a **teljesítmény-diagnosztika telepítése** gombot.
1. Jelölje be a **diagnosztika futtatása** jelölőnégyzetet, ha a telepítés befejezése után szeretné futtatni a diagnosztikát. Ha ezt a beállítást választja, kiválaszthatja a teljesítmény-elemzési forgatókönyvet és a kapcsolódó beállításokat.

    ![A teljesítmény-diagnosztika telepítési gombjának képernyőképe](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Válassza ki a futtatandó elemzési forgatókönyvet

A következő elemzési forgatókönyvek érhetők el a Azure Portal. A teljesítménnyel kapcsolatos problémától függően válasszon ki egy elemzést. Adja meg az elemzéshez szükséges időtartamot és nyomkövetési beállításokat.

* **Gyors teljesítmény elemzése**  
    Ellenőrzi az ismert problémákat, elemzi az ajánlott eljárásokat, és gyűjti a diagnosztikai adatokat. Ez az elemzés több percet is igénybe vehet. További információ [Windows](https://aka.ms/perfinsights/quick) vagy [Linux](https://aka.ms/perfinsightslinux/quick) rendszerről

* **Teljesítmény elemzése**  
    A a gyors teljesítmény elemzését és a nagy erőforrás-használatot figyelő összes ellenőrzést tartalmazza. Ezzel a verzióval elháríthatja az általános teljesítménnyel kapcsolatos problémákat, például a nagy CPU-, memória-és lemezhasználat-használatot. Ez az elemzés 30 másodperctől 15 percet vesz igénybe a kiválasztott időtartamtól függően. További információ [Windows](https://aka.ms/perfinsights/vmslow) vagy [Linux](https://aka.ms/perfinsightslinux/vmslow) rendszerről

* **Fejlett teljesítmény-elemzés**`*`  
    A a teljesítmény elemzésének összes ellenőrzését tartalmazza, és egy vagy több nyomkövetést gyűjt, ahogy az a következő részekben is látható. Ez a forgatókönyv a további nyomkövetést igénylő összetett problémák elhárítására használható. A forgatókönyv hosszabb időszakokra való futtatása a virtuális gép méretétől és a kiválasztott nyomkövetési beállításoktól függően növeli a diagnosztika kimenetének teljes méretét. Ez az elemzés 30 másodperctől 15 percet vesz igénybe, a kiválasztott időtartamtól függően. [További információ](https://aka.ms/perfinsights/advanced)

* **Azure Files elemzése**`*`  
    A teljesítmény elemzésének összes ellenőrzését tartalmazza, és rögzíti a hálózati nyomkövetési és SMB-számlálókat. Ez a forgatókönyv az Azure Files teljesítményének hibakeresésére használható. Ez az elemzés 30 másodperctől 15 percet vesz igénybe, a kiválasztott időtartamtól függően. [További információ](https://aka.ms/perfinsights/azurefiles)

>[!Note]
>[`*`] Ezek az elemzési forgatókönyvek csak Windows rendszeren támogatottak.

![Képernyőfelvétel a diagnosztika panelről a teljesítmény-diagnosztika panelen](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Tünetek megadása (nem kötelező)

A listából válassza ki az előválasztott tüneteket, vagy adjon hozzá új tüneteket. Ez segít az elemzés jövőbeli fejlesztésében.

### <a name="provide-support-request-number-if-available-optional"></a>Támogatási kérelem számának megadása, ha elérhető (nem kötelező)

Ha egy meglévő támogatási jegyhez tartozó Microsoft támogatási szakemberrel dolgozik, adja meg a támogatási jegy számát.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Tekintse át az adatvédelmi szabályzatot és a jogi feltételeket, és jelölje be a visszaigazolni kívánt jelölőnégyzetet (kötelező)

A diagnosztika futtatásához el kell fogadnia a jogi feltételeket, és el kell fogadnia az adatvédelmi szabályzatot.

### <a name="select-ok-to-run-the-diagnostics"></a>A diagnosztika futtatásához kattintson az OK gombra.

Egy értesítés jelenik meg, amikor a rendszer teljesítményfigyelőként elindítja a telepítést. A telepítés befejezése után megjelenik egy értesítés, amely azt jelzi, hogy a telepítés sikeres volt. A kiválasztott elemzés ezután a megadott időtartamra fut. Ez jó alkalom lenne a teljesítménnyel kapcsolatos probléma újbóli előállítására, hogy a diagnosztikai adatok rögzítve legyenek a megfelelő időben.

Az elemzés befejezése után a következő elemek fel lesznek töltve az Azure-táblákba és a megadott Storage-fiókban található bináris nagy objektum (BLOB) tárolóba:

* A futtatással kapcsolatos összes információ és kapcsolódó információ
* Kimeneti tömörített (. zip) fájl ( **PerformanceDiagnostics_yyyy-mm-dd_hh-mm-SS-fff. zip** ) a Windows rendszerben, valamint egy **PerformanceDiagnostics_yyyy-mm-dd_hh-mm-SS-fff. tar. gz** nevű tar Ball-fájl (a naplófájlokat tartalmazó Linux rendszeren)
* HTML-jelentés

A feltöltés után egy új diagnosztikai jelentés szerepel a Azure Portal.

![Képernyőkép a diagnosztikai jelentések listájáról a teljesítmény-diagnosztika panelen](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>A teljesítmény-diagnosztika beállításainak módosítása

A **Beállítások** eszköztár gomb használatával módosíthatja azt a Storage-fiókot, ahol a diagnosztikai eredmények és a kimenet tárolható. Ugyanazt a Storage-fiókot használhatja több, teljesítmény-diagnosztikát használó virtuális gép számára. A Storage-fiók módosításakor a rendszer nem törli a régi jelentéseket és az adatfelismeréseket. Azonban többé nem fognak megjelenni a diagnosztikai jelentések listájában.

## <a name="review-insights-and-performance-diagnostics-report"></a>Az áttekintések és a teljesítmény-diagnosztika jelentés áttekintése

Minden diagnosztikai Futtatás tartalmazza a begyűjtött információk és javaslatok listáját, az érintett erőforrásokat, a naplófájlokat és az összegyűjtött egyéb részletes diagnosztikai adatokat, valamint egy jelentést az offline megtekintéshez. Az összegyűjtött diagnosztikai adatok teljes listájáért tekintse meg a **PerfInsights által gyűjtött információkat** a [Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) vagy [Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights)rendszeren.

### <a name="select-a-performance-diagnostics-report"></a>Teljesítmény-diagnosztikai jelentés kiválasztása

A diagnosztikai jelentések listája segítségével megkeresheti az összes futtatott diagnosztikai jelentést. A lista tartalmazza a felhasznált elemzés részleteit, a talált megállapításokat és azok hatását. Válasszon ki egy sort a további részletek megtekintéséhez.

![A diagnosztikai jelentés kiválasztásának képernyőképe a teljesítmény-diagnosztika panelen](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Teljesítmény-diagnosztikai jelentés áttekintése

Az egyes teljesítmény-diagnosztikai jelentések több megállapítást is tartalmazhatnak, és magas, közepes vagy alacsony szintű hatást jelezhetnek. Az egyes betekintés javaslatokat is tartalmaz a probléma csökkentése érdekében. Az áttekintések egyszerű szűrésre vannak csoportosítva.

A befolyásoló szintek a teljesítménnyel kapcsolatos problémákra, például a helytelen konfiguráció, az ismert problémák vagy más felhasználók által jelentett problémákra épülnek. Előfordulhat, hogy még nem tapasztal egy vagy több felsorolt problémát. Előfordulhat például, hogy az SQL-naplófájlok és az adatbázisfájlok ugyanazon az adatlemezen vannak. Ez az állapot magas potenciállal rendelkezik a szűk keresztmetszetek és az egyéb teljesítményproblémák esetén, ha az adatbázis használata magas, míg előfordulhat, hogy nem észleli a problémát, ha a használat alacsony.

![A teljesítmény diagnosztikai jelentés áttekintés paneljének képernyőképe](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>A teljesítménnyel kapcsolatos diagnosztikai ismeretek és javaslatok áttekintése

Kiválaszthat egy képet az érintett erőforrásokkal, javasolt megoldásokkal és hivatkozásokkal kapcsolatos további részletek megtekintéséhez.

![Képernyőfelvétel a teljesítmény-diagnosztika részleteiről](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>A teljes teljesítményű diagnosztikai jelentés letöltése és áttekintése

A **jelentés letöltése** gombra kattintva letöltheti a további részletes diagnosztikai adatokat tartalmazó HTML-jelentéseket, például a tárterületet és a hálózati konfigurációt, a teljesítményszámlálókat, a nyomkövetést, a folyamatok listáját és a naplókat. A tartalom a kiválasztott elemzéstől függ. A speciális hibaelhárításhoz a jelentés további információkat és interaktív diagramokat is tartalmazhat, amelyek a nagy CPU-használattal, a nagy lemezterülettel és a túlzott memóriát használó folyamatokkal kapcsolatosak. A teljesítmény-diagnosztikai jelentéssel kapcsolatos további információkért lásd: [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) vagy [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Teljesítmény-diagnosztikai jelentések kezelése

Egy vagy több teljesítményteszt-jelentést a **jelentés törlése** gomb használatával törölhet.

## <a name="how-to-uninstall-performance-diagnostics"></a>A teljesítmény-diagnosztika eltávolítása

A teljesítmény-diagnosztika eltávolítható egy virtuális gépről. Ez a művelet eltávolítja a virtuálisgép-bővítményt, de nincs hatással a Storage-fiókban található diagnosztikai információkra.

![Képernyőfelvétel a teljesítmény-diagnosztika panel eszköztáráról az Eltávolítás gomb kiemelésével](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Hol található a virtuális gépen tárolt diagnosztikai adatok

Az összes teljesítményteszt-bepillantást és jelentést a saját Storage-fiókjában tárolja a rendszer. Az adatellenőrzések tárolása az Azure-táblákon belül történik. A jelentések tömörített fájlját egy azdiagextnresults nevű bináris nagy objektum (BLOB) tároló tárolja.

A Storage-fiók adatait az eszköztár beállítások gombjának használatával tekintheti meg.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Az adatgyűjtés Hogyan a Microsoft ügyfélszolgálatával

A diagnosztikai jelentések több módon is megoszthatók a Microsofttal.

**1. lehetőség:** A legújabb jelentés automatikus megosztása  
Ha támogatási jegyet nyit a Microsofttal, fontos, hogy megossza a teljesítmény-diagnosztikai jelentést. Ha úgy döntött, hogy megosztja ezeket az információkat a Microsofttal a diagnosztika futtatásakor (az "Elfogadom a**diagnosztika adatainak megosztása**a Microsofttal" jelölőnégyzet bejelölésével), akkor a Microsoft sas-hivatkozással férhet hozzá a jelentéshez a Storage-fiók használatával. a kimeneti zip-fájlba a Futtatás dátumától számítva legfeljebb 30 napig. Csak a legfrissebb jelentés érhető el a támogatási szakembernek.

**2. lehetőség:** Közös hozzáférési aláírás létrehozása a diagnosztikai jelentés tömörített fájljához  
Megosztott hozzáférési aláírások használatával megoszthat egy hivatkozást a jelentések tömörített fájljára. Ehhez kövesse az alábbi lépéseket:

1. A Azure Portal tallózással keresse meg azt a Storage-fiókot, amelyben a diagnosztikai adatait tárolja.
1. A **blob Service** szakaszban válassza a **Blobok** elemet.
1. Válassza ki a **azdiagextnresults** tárolót.
1. Válassza ki a megosztani kívánt teljesítmény-diagnosztikai kimeneti tömörített fájlt.
1. A **sas** előállítása lapon válassza ki a megosztás feltételeit.
1. Kattintson **a blob sas-jogkivonat és URL-cím létrehozásához**.
1. Másolja a **blob sas URL-címét**, és ossza meg a támogatási szakemberrel.

**3. lehetőség:** A jelentés letöltése a Storage-fiókból

A teljesítmény-diagnosztika jelentés tömörített fájlját a 2. lépés 1 – 4. lépése alapján is megkeresheti. Válassza ki a fájlt, majd ossza meg e-mailben, vagy kérje meg a támogatási szakembert, hogy töltse fel a fájlt.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>A diagnosztikai adatok Hogyan rögzítése a megfelelő időpontban

Minden teljesítményteszt-futtatás két szakaszból áll:

1. Telepítse vagy frissítse a Performance Diagnostics virtuálisgép-bővítményt.
1. Futtassa a diagnosztikát a megadott időtartamra.

Jelenleg a virtuálisgép-bővítmény telepítésének befejezésekor nem lehet egyszerűen megismerni a módját. A virtuálisgép-bővítmény telepítéséhez általában körülbelül 45 másodperc és 1 percet vesz igénybe. A virtuálisgép-bővítmény telepítése után futtathatja a Reprodukálási lépéseit, hogy a teljesítmény-diagnosztika rögzítse a megfelelő adathalmazt a hibaelhárításhoz.

## <a name="next-steps"></a>További lépések

Ha még nem tudja megállapítani a probléma okát, és további segítségre van szüksége, a teljesítmény-diagnosztikai megállapítások és jelentések áttekintése után megnyithatja a támogatási jegyet a Microsoft ügyfélszolgálatával.

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg az [Azure támogatási](https://azure.microsoft.com/support/options/)webhelyét, és válassza a **támogatás kérése**lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
