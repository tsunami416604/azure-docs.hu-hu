---
title: Oktatóanyag – a Windows rendszerű virtuális gépek konfigurációjának kezelése az Azure-ban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan azonosíthatja a módosításokat, és hogyan kezelheti a csomagok frissítéseit egy Windows rendszerű virtuális gépen
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 013938f37258b5aa8c4e9751bdc8cf1e7b826ef1
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71680035"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Oktatóanyag: Változások figyelése és Windowsos virtuális gépek frissítése az Azure-ban

Az Azure [change Tracking](../../automation/change-tracking.md) segítségével könnyedén azonosíthatja a módosításokat, és [Update Management](../../automation/automation-update-management.md) lehetővé teszi az Azure Windows rendszerű virtuális gépek operációsrendszer-frissítéseinek kezelését.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Windows-frissítések kezelése
> * A módosítások és a leltár monitorozása

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Ebben az oktatóanyagban az Azure-monitorozás és a frissítéskezelés konfigurálásához egy Azure-beli windowsos virtuális gép szükséges. Először a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozza létre a virtuális gépet a [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre az *USA keleti régiója* helyen. Ha még nem létezik, létrejön a *myResourceGroupMonitorMonitor* nevű erőforráscsoport a támogató hálózati erőforrásokkal együtt:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Az erőforrások és a virtuális gép létrehozása néhány percig tart.

## <a name="manage-windows-updates"></a>Windows-frissítések kezelése

Update Management lehetővé teszi az Azure-beli Windows rendszerű virtuális gépek frissítéseinek és javításának kezelését. A virtuális gépről gyorsan felmérheti az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, és áttekintheti a telepítési eredményeket, hogy ellenőrizze, sikeres volt-e a frissítések telepítése a virtuális gépen.

A díjszabással kapcsolatos információkért lásd: [az Automation díjszabása az Update Management](https://azure.microsoft.com/pricing/details/automation/)szolgáltatáshoz.

### <a name="enable-update-management"></a>Az Update Management engedélyezése

Az Update management engedélyezése a virtuális géphez:

1. A képernyő bal oldalán válassza a **Virtuális gépek** elemet.
2. Válasszon ki egy virtuális gépet a listából.
3. A virtuális gép képernyőjének **Műveletek** szakaszában kattintson a **Frissítéskezelés** elemre. Ekkor megnyílik **Az Update Management engedélyezése** képernyő.

A rendszer ellenőrzi, hogy az Update Management engedélyezve van-e a virtuális gépen.
A rendszer eközben azt is ellenőrzi, hogy létezik-e Log Analytics-munkaterület és egy csatlakoztatott Automation-fiók, valamint hogy a megoldás már jelen van-e a munkaterületen.

A [Log Analytics](../../log-analytics/log-analytics-overview.md)-munkaterület az Update Management, valamint a hasonló funkciók és szolgáltatások által létrehozott adatok gyűjtésére szolgál.
A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez.
A frissítést igénylő virtuális gépeken további műveletek elvégzése érdekében az Azure Automation runbookok futtatását is lehetővé teszi a virtuális gépeken (pl. letöltés és frissítések alkalmazása).

Az ellenőrzési folyamat arra is kiterjed, hogy a virtuális gépen működik-e a Microsoft Monitoring Agent (MMA) és az Automation hibrid runbook-feldolgozó.
Ez az ügynök kommunikál a virtuális géppel, továbbá begyűjti a frissítési állapottal kapcsolatos információkat.

Válassza ki az Log Analytics munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** gombra a megoldás engedélyezéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

Ha az előkészítés közben az alábbi előfeltételek bármelyike hiányzik, a rendszer automatikusan hozzáadja azt:

* [Log Analytics](../../log-analytics/log-analytics-overview.md)-munkaterület
* [Automatizálás](../../automation/automation-offering-get-started.md)
* Engedélyezett [hibrid runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md) a virtuális gépen

Megnyílik az **Update Management** képernyő. Konfigurálja a használni kívánt helyet, Log Analytics munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés**gombra. Ha a mezők szürkén jelennek meg, az azt jelenti, hogy egy másik automatizálási megoldás már engedélyezve van a virtuális gépen, ezért az ahhoz tartozó munkaterületet és Automation-fiókot kell használnia.

![Az Update Management megoldás engedélyezése](./media/tutorial-monitoring/manageupdates-update-enable.png)

A megoldás engedélyezése akár 15 percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot. A megoldás engedélyezését követően a virtuális gép hiányzó frissítéseivel kapcsolatos információk Azure Monitor naplókra áramlanak. Az adatok legalább 30 perc és legfeljebb 6 óra múlva állnak készen az elemzésre.

### <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

A **Frissítéskezelés** engedélyezése után megjelenik a **Frissítéskezelés** képernyő. Miután elkészült a frissítések kiértékelése, a **Hiányzó frissítések** lapon megjelenik a hiányzó frissítések listája.

 ![Frissítés állapotának megtekintése](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy olyan telepítést, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek. Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

Ütemezzen egy új frissítéstelepítést a virtuális géphez. Ehhez kattintson a **Frissítéskezelés** képernyő felső részén található **Frissítések központi telepítésének ütemezése** elemre. Az **Új frissítéstelepítés** képernyőn adja meg a következő információkat:

Új frissítés központi telepítésének létrehozásához válassza a **frissítés központi telepítésének ütemezett**beállítást. Megnyílik az **új frissítés központi telepítése** oldal. Adja meg az alábbi táblázatban leírt tulajdonságok értékeit, majd kattintson a **Létrehozás**gombra:

| Tulajdonság | Description |
| --- | --- |
| Name (Név) |A frissítéstelepítést beazonosító egyedi név. |
|Operációs rendszer| Linux vagy Windows|
| Frissítendő csoportok |Azure-gépek esetén az előfizetés, az erőforráscsoportok, a helyszínek és a címkék kombinációja alapján hozzon létre egy lekérdezést az üzembe helyezéshez felvenni kívánt Azure-beli virtuális gépek dinamikus csoportjának létrehozásához. </br></br>Nem Azure-beli gépek esetén válasszon ki egy meglévő mentett keresést, és válasszon ki egy olyan csoportot, amely nem Azure-beli gépeket tartalmaz az üzembe helyezéshez. </br></br>További információ: [dinamikus csoportok](../../automation/automation-update-management.md#using-dynamic-groups)|
| Frissítendő gépek |Válasszon ki egy mentett keresést, importált csoportot, vagy válasszon gépet a legördülő listából, és válassza az egyes gépek lehetőséget. Ha a **Gépek** lehetőséget választotta, a gép állapota az **ÜGYNÖK KÉSZÜLTSÉGÉNEK FRISSÍTÉSE** oszlopban látható.</br> A számítógépcsoportok Azure Monitor-naplókban való létrehozásának különböző módszereiről a következő témakörben talál további információt: [számítógépcsoportok Azure monitor-naplókban](../../azure-monitor/platform/computer-groups.md) |
|Frissítési besorolások|Válassza ki az összes szükséges frissítési besorolást|
|Frissítések belefoglalása/kizárása|Ekkor megnyílik a **Belefoglalás/kizárás** oldal. A belefoglalandó vagy kizárandó frissítések külön lapokon jelennek meg. További információ a bevonások kezeléséről: a [beilleszkedési viselkedés](../../automation/automation-update-management.md#inclusion-behavior) |
|Ütemezési beállítások|Válassza ki a kezdéshez szükséges időt, és válasszon egyszer vagy ismétlődő lehetőséget az ismétlődéshez|
| Parancsfájlok előtti + parancsfájlok utáni|Válassza ki az üzembe helyezés előtt és után futtatandó parancsfájlokat|
| Karbantartási időszak |A frissítések számára beállított percek száma. Az érték nem lehet kevesebb, mint 30 perc, legfeljebb 6 óra |
| Vezérlő újraindítása| Meghatározza az újraindítások kezelését. Az elérhető lehetőségek:</br>Újraindítás szükség esetén (alapértelmezett beállítás)</br>Mindig induljon újra</br>Soha ne induljon újra</br>Csak újraindítás – frissítések nem lesznek telepítve|

A frissítési központi telepítések programozott módon is létrehozhatók. Ha meg szeretné tudni, hogyan hozhat létre frissítési központi telepítést a REST API, tekintse meg a [szoftverfrissítési konfigurációk – létrehozás](/rest/api/automation/softwareupdateconfigurations/create)című témakört. Létezik egy minta runbook is, amely a heti frissítés központi telepítésének létrehozására használható. További információ erről a runbook: [heti frissítési telepítés létrehozása egy vagy több virtuális géphez egy erőforráscsoport esetében](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

Ha befejezte az ütemezés konfigurálását, kattintson a **Létrehozás** gombra. Ezután visszalép az állapot-irányítópultra.
Ekkor az **Ütemezett** táblázatban már látható az Ön által létrehozott telepítésütemezés.

### <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, az **Update Management** képernyő **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota.
Ha éppen fut, az állapota **Folyamatban**. Ha sikeresen befejeződik, **Sikeres** állapotúra változik.
Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, az állapota **Részben sikertelen**.
Ha rákattint a befejezett frissítéstelepítésre, megjelenik az adott frissítéstelepítés irányítópultja.

![Adott telepítés frissítéstelepítési állapot-irányítópultja](./media/tutorial-monitoring/manageupdates-view-results.png)

A **Frissítés eredményei** csempe összesíti a frissítések teljes számát és az adott virtuális gépre vonatkozó telepítési eredményeket.
A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza, amely a következők egyike lehet:

* **Nem lett megkísérelve** – a frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem lett volna rá elég idő.
* **Sikeres** – a frissítés sikeres volt
* **Sikertelen** – a frissítés sikertelen volt

Kattintson a **Minden napló** csempére a telepítés által létrehozott összes naplóbejegyzés megtekintéséhez.

Kattintson a **Kimenet** csempére azon runbook feladatstreamjének megtekintéséhez, amely a frissítések telepítését kezeli a cél virtuális gépen.

Kattintson a **Hibák** csempére a telepítés közben felmerülő hibák részletes információinak megtekintéséhez.

## <a name="monitor-changes-and-inventory"></a>A módosítások és a leltár monitorozása

Összegyűjtheti a számítógépeken található szoftverek, fájlok, Linux-démonok, Windows-szolgáltatások és Windows-beállításkulcsok listáját, és leltárt készíthet belőlük. A gépek konfigurációjának nyomon követésével megtalálhatja a környezetben felmerülő működési problémákat, és alaposabban megismerheti a gépek állapotát.

### <a name="enable-change-and-inventory-management"></a>A változás- és leltárkezelés engedélyezése

A változás- és leltárkezelés engedélyezése a virtuális géphez:

1. A képernyő bal oldalán válassza a **Virtuális gépek** elemet.
2. Válasszon ki egy virtuális gépet a listából.
3. A virtuális gép képernyőjének **Műveletek** szakaszában kattintson az **Inventory** vagy a **Change tracking** elemre. Megnyílik **A Change Tracking and Inventory engedélyezése** képernyő.

Konfigurálja a használni kívánt helyet, Log Analytics munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés**gombra. Ha a mezők szürkén jelennek meg, az azt jelenti, hogy egy másik automatizálási megoldás már engedélyezve van a virtuális gépen, ezért az ahhoz tartozó munkaterületet és Automation-fiókot kell használnia. Bár a megoldások elkülönülve jelennek meg a menüben, ugyanarról a megoldásról van szó. Ha engedélyezi az egyiket, a virtuális gépen mindkét megoldás engedélyezve lesz.

![A változások és a leltár követésének engedélyezése](./media/tutorial-monitoring/manage-inventory-enable.png)

A megoldás engedélyezése után eltarthat egy ideig, amíg a virtuális gépen elkészül a leltár, és megjelennek az adatok.

### <a name="track-changes"></a>Változások követése

A virtuális gépen kattintson a **Változások követése** elemre a **MŰVELETEK** területen. A megnyíló **Change Tracking** lapon kattintson a **Beállítások szerkesztése** elemre. Jelölje be a követni kívánt beállításokat, majd kattintson a **+ Hozzáadás** gombra. A Windows esetén elérhető lehetőségek:

* Windows-beállításjegyzék
* Windows Files

A Change Tracking megoldásról további információt a [Virtuális gép módosításainak hibaelhárítása](../../automation/automation-tutorial-troubleshoot-changes.md) című cikkben talál.

### <a name="view-inventory"></a>Leltár megtekintése

A virtuális gépen kattintson a **Leltár** elemre a **MŰVELETEK** területen. A **Szoftver** lapon egy táblázat formátumú lista jeleníti meg a felderített szoftvereket. Az egyes szoftverrekordok részletes információi megtekinthetők a táblázatban. Ilyen információ például a szoftver neve, verziója, közzétevője és a legutóbbi frissítés ideje.

![Leltár megtekintése](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Tevékenységnaplók és változások monitorozása

A virtuális gép **Change Tracking** lapján válassza a **Tevékenységnapló-kapcsolat kezelése** lehetőséget. Ez a feladat megnyitja az **Azure-tevékenységnapló** lapot. A **Csatlakozás** gombra kattintva csatlakoztathatja a Change Tracking megoldást a virtuális gép Azure-tevékenységnaplójához.

A beállítás engedélyezése után lépjen a virtuális gép **Áttekintés** lapjára, majd kattintson a **Leállítás** gombra a virtuális gép leállításához. A megjelenő üzenetben kattintson az **Igen** gombra a virtuális gép leállításához. A felszabadított virtuális gépeket az **Indítás** gombra kattintva indíthatja újra.

A virtuális gép leállítása és elindítása egy-egy eseményt rögzít a tevékenységnaplóban. Lépjen vissza a **Change Tracking** oldalra. Az oldal alján válassza az **Események** lapot. Az események idővel megjelennek a diagramban és a táblázatban. Az egyes események kijelölésével megjelenítheti a hozzájuk tartozó részletes adatokat.

![Változások megtekintése a tevékenységnaplóban](./media/tutorial-monitoring/manage-activitylog-view-results.png)

A diagram az eltelt idő alatt bekövetkezett változásokat mutatja. A Tevékenységnapló-kapcsolat hozzáadása után a felül látható vonaldiagramon az Azure tevékenységnapló eseményei jelennek meg. A sávdiagramok minden egyes sora más-más követhető változástípust jelenít meg. Ezek a típusok lehetnek Linux-démonok, fájlok, Windows-beállításkulcsok, szoftverek és Windows-szolgáltatások. A Változások lapon jelennek meg a vizualizációban látható változások részletei a változás bekövetkezte szerinti fordított időrendben (vagyis a legutóbbi van legelöl).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban konfigurálta és ellenőrizte Change Tracking és Update Management a virtuális géphez. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Erőforráscsoport és virtuális gép létrehozása
> * Windows-frissítések kezelése
> * A módosítások és a leltár monitorozása

Folytassa a következő oktatóanyaggal, amely a virtuális gép monitorozását ismerteti.

> [!div class="nextstepaction"]
> [Virtuális gépek figyelése](tutorial-monitor.md)
