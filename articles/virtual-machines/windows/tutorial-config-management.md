---
title: Oktatóanyag – A Windows virtuális gépek konfigurációjának kezelése az Azure-ban
description: Ebben az oktatóanyagban megtudhatja, hogyan azonosíthatja a módosításokat és kezelheti a csomagfrissítéseket windowsos virtuális gépen
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
ms.openlocfilehash: d97323f1916ee46e6b1f8d4ca8723b950baca39c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238579"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Oktatóanyag: A módosítások figyelése és egy Windows-virtuális gép frissítése az Azure-ban

Az Azure [Change Tracking](../../automation/change-tracking.md) and Update [Management segítségével](../../automation/automation-update-management.md)könnyedén azonosíthatja a Windows virtuális gépei változásait az Azure-ban, és kezelheti az operációs rendszer frissítéseit ezekhez a virtuális gépekhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Windows-frissítések kezelése.
> * A változások és a készlet figyelése.

## <a name="open-azure-cloud-shell"></a>Az Azure Cloud Shell megnyitása

Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj, amely segítségével futtathatja a cikkben leírt lépéseket. Az Azure-fiókkal való használatra előre telepített és konfigurált általános Azure-eszközökkel rendelkezik.

Ha meg szeretne nyitni bármilyen kódblokkot a Cloud Shellben, válassza a **Próbálja ki** a kódblokk jobb felső sarkából lehetőséget.

A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/powershell](https://shell.azure.com/powershell)is megnyithatja a segítségével. Válassza a **Másolás** lehetőséget a kódblokkok másolásához, beillesztésükhöz a Cloud Shell lapra, és válassza az Enter kulcsot a kód futtatásához.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Ebben az oktatóanyagban az Azure-monitorozás és a frissítéskezelés konfigurálásához egy Azure-beli windowsos virtuális gép szükséges.

Először a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Ezután hozza létre a virtuális gép [new-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) A következő példa létrehoz `myVM` egy `East US` virtuális gép neve a helyen. Ha még nem léteznek, létrejön nek az erőforráscsoport `myResourceGroupMonitor` és a támogató hálózati erőforrások:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Az erőforrások és a virtuális gép létrehozása néhány percig tart.

## <a name="manage-windows-updates"></a>Windows-frissítések kezelése

Az Update Management segítségével kezelheti az Azure Windows virtuális gépei frissítéseit és javításait. Közvetlenül a virtuális gép, gyorsan:

- Az elérhető frissítések állapotának felmérése.
- A szükséges frissítések telepítésének ütemezése.
- Tekintse át a központi telepítési eredményeket, és ellenőrizze, hogy a frissítések sikeresen alkalmazva vannak-e a virtuális gépre.

Az árképzéssel kapcsolatos információkért lásd: [Automation díjszabás a frissítés kezelés.](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Az Update Management engedélyezése

A virtuális gép frissítéskezelésének engedélyezése:

1. Az ablak bal oldalán válassza a **Virtuális gépek**lehetőséget.
1. Válasszon egy virtuális gép a listából.
1. A Virtuálisgép ablak **Műveletek** ablaktábláján válassza a **Frissítéskezelés**lehetőséget.
1. Megnyílik **az Update Management engedélyezése** ablak.

Az ellenőrzés annak megállapítására történik, hogy engedélyezve van-e az Update Management a virtuális géphez. Az érvényesítés magában foglalja a Log Analytics-munkaterület, egy csatolt Automation-fiók, és arról, hogy a megoldás a munkaterületen.

A [Log Analytics-munkaterület](../../log-analytics/log-analytics-overview.md) segítségével adatokat gyűjthet, amelyeket olyan funkciók és szolgáltatások generálnak, mint például az Update Management. A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez.

További műveletek végrehajtásához a virtuális gépeken, amelyek frissítéseket igényelnek, az Azure Automation használatával futtassa a virtuális gépeken futókönyvek. Ilyen műveletek közé tartozik a frissítések letöltése vagy alkalmazása.

Az ellenőrzési folyamat azt is ellenőrzi, hogy a virtuális gép ki van-e építve a Microsoft Monitoring Agent (MMA) és az Automation Hybrid Runbook Worker. Az ügynök segítségével kommunikálhat a virtuális gép, és információt szerezni a frissítés állapotát.

Az **Update Management engedélyezése** ablakban válassza a Log Analytics munkaterület- és automatizálási fiókot, majd az **Engedélyezés lehetőséget.** A megoldás engedélyezése akár 15 percet is igénybe vesz.

A bevezetés során hiányzó következő előfeltételek bármelyike automatikusan hozzáadódik:

* [Log Analytics](../../log-analytics/log-analytics-overview.md) munkaterület
* [Automation](../../automation/automation-offering-get-started.md)
* Egy [hibrid runbook-dolgozó](../../automation/automation-hybrid-runbook-worker.md), amely engedélyezve van a virtuális gépen

A megoldás engedélyezése után megnyílik a **Frissítéskezelés** ablak. Konfigurálja a hely, a Log Analytics-munkaterület és az Automation-fiók használatát, majd válassza **az Engedélyezés lehetőséget.** Ha ezek a beállítások halványan jelennek meg, egy másik automatizálási megoldás engedélyezve van a virtuális gép, és hogy a megoldás munkaterületi és automation-fiók kell használni.

![Frissítéskezelési megoldás engedélyezése](./media/tutorial-monitoring/manageupdates-update-enable.png)

Az Update Management megoldás engedélyezése akár 15 percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot. A megoldás engedélyezése után a virtuális gép hiányzó frissítései az Azure Monitor-naplókba irányuló információk. Az adatok elemzéshez való hozzáférhetővé válása 30 perctől 6 óráig is eltarthat.

### <a name="view-an-update-assessment"></a>Frissítésfelmérés megtekintése

Az Update Management engedélyezése után megjelenik a **Frissítéskezelés** ablak. A frissítések kiértékelése befejezése után a **Hiányzó frissítések** lapon megjelenik a hiányzó frissítések listája.

 ![Frissítés állapotának megtekintése](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy olyan telepítést, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek. Kiválaszthatja, hogy mely frissítési típusokat szeretné felvenni a központi telepítésbe. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

A virtuális gép új központi telepítésének ütemezéséhez válassza a **Frissítési telepítés ütemezése** a **frissítéskezelési** ablak tetején lehetőséget. Az **Új frissítés központi telepítése** ablakban adja meg a következő információkat:

| Beállítás | Leírás |
| --- | --- |
| **Név** |Adjon meg egy egyedi nevet a frissítés központi telepítésének azonosításához. |
|**Operációs rendszer**| Válassza a **Linux** vagy a **Windows**lehetőséget.|
| **Frissítandó csoportok** |Az Azure-ban üzemeltetett virtuális gépek hez hozzon létre egy lekérdezést előfizetés, erőforráscsoportok, helyek és címkék kombinációjára. Ez a lekérdezés az Azure által üzemeltetett virtuális gépek dinamikus csoportját hoz létre az üzembe helyezéshez. </br></br>Az Azure-ban nem üzemeltetett virtuális gépek esetén válasszon ki egy meglévő mentett keresést. Ezzel a kereséssel kiválaszthatja ezeknek a virtuális gépeknek a központi telepítésbe felvenni kívánt csoportját. </br></br> További információ: [Dinamikus csoportok](../../automation/automation-update-management-groups.md).|
| **Frissíthető gépek** |Válassza a **Mentett keresés**, Az **Importált csoport**vagy a **Gépek**lehetőséget.<br/><br/>Ha a Gépek lehetőséget **választja,** a legördülő listából választhat ja az egyes gépeket. Az egyes gépek készenléte a táblázat **UPDATE AGENT READINESS** oszlopában látható.</br></br> A számítógépcsoportok létrehozásának különböző módszereiről az Azure Monitor naplóiban a [Számítógépcsoportok az Azure Monitor naplóiban című témakörben olvashat.](../../azure-monitor/platform/computer-groups.md) |
|**Besorolások frissítése**|Válassza ki az összes szükséges frissítési besorolást.|
|**Frissítések belefoglalása/kizárása**|Ezzel a beállítással megnyithatja a **Belefoglalás/kizárás** ablaktáblát. A beveendő és a kizárandó frissítések külön lapokon találhatók. A felvétel kezeléséről a Frissítés [telepítésének ütemezése](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment)című témakörben talál további információt. |
|**Ütemezési beállítások**|Válassza ki az indítási időt, és válassza az **Egyszer** vagy **az Ismétlődő**lehetőséget.|
| **Előszkriptek + Utószkriptek**|Válassza ki a központi telepítés előtt és után futtatni kívánt parancsfájlokat.|
| **Karbantartási ablak** | Adja meg a frissítésekhez beállított percek számát. Az érvényes értékek 30 és 360 perc között lehetnek. |
| **Újraindítás vezérlése**| Adja meg az újraindítások kezelésének módját. A rendelkezésre álló választási lehetőség a következő:<ul><li>**Újraindítás, ha szükséges**</li><li>**Mindig induljon újra**</li><li>**Soha ne induljon újra**</li><li>**Csak újraindítás**</li></ul>**Indítsa újra, ha szükséges,** az alapértelmezett beállítás. Ha a **Csak újraindítás**lehetőséget választja, a frissítések nincsenek telepítve.|

Miután befejezte az ütemezés konfigurálását, a **Létrehozás gombra** kattintva térjen vissza az állapot-irányítópultra. Az **Ütemezett** tábla a létrehozott telepítési ütemezést mutatja.

Programozott módon is létrehozhat frissítési központi telepítéseket. Ha tudni szeretné, hogyan hozhat létre frissítési központi telepítést a REST API-val, olvassa el a [Szoftverfrissítési konfigurációk – Létrehozás című témakört.](/rest/api/automation/softwareupdateconfigurations/create) Van egy minta runbook, amely segítségével hozzon létre egy heti frissítés központi telepítés. A runbookról további információ: [Heti frissítési központi telepítés létrehozása egy erőforráscsoport egy vagy több virtuális gépéhez.](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)

### <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Az ütemezett telepítés megkezdése után a központi telepítés állapotát a Frissítéskezelés ablak **Központi telepítések frissítése** lapján **láthatja.**

Ha a központi telepítés jelenleg fut, az állapota "Folyamatban" lesz. A sikeres befejezés után az állapot "Sikeres" állapotra változik. De ha a központi telepítés bármely frissítése sikertelen, az állapot "Részben sikertelen" lesz.

Válassza ki a befejezett frissítés központi telepítését az adott központi telepítés irányítópultjának megtekintéséhez.

![Adott telepítés frissítéstelepítési állapot-irányítópultja](./media/tutorial-monitoring/manageupdates-view-results.png)

A **Frissítés eredmények** csempe a virtuális gép frissítések és telepítési eredmények teljes számának összegzését jeleníti meg. A jobb oldali táblázat az egyes frissítések és a telepítési eredmények részletes bontását mutatja. Minden eredmény nek van egy értéke:

* **Nem történt kísérlet**: A frissítés nincs telepítve. Nem volt elég idő a meghatározott karbantartási időszak időtartama alapján.
* **Sikeres :** A frissítés sikerült.
* **Nem sikerült**: A frissítés nem sikerült.

A telepítés által létrehozott összes naplóbejegyzés megtekintéséhez válassza **Az összes napló** elemet.

Válassza ki a **kimeneti** csempe a feladatfolyam a runbook felelős a frissítés központi telepítésének a cél virtuális gépen.

Válassza a **Hibák** lehetőséget a telepítési hibák részletes adatainak megtekintéséhez.

## <a name="monitor-changes-and-inventory"></a>A módosítások és a leltár monitorozása

Összegyűjtheti és megtekintheti a számítógépen lévő szoftverek, fájlok, Linux démonok, Windows-szolgáltatások és Windows beállításkulcsok leltárát. A gépek konfigurációjának nyomon követése segít a működési problémák azonosításában a környezetben, és jobban megértheti a gépek állapotát.

### <a name="enable-change-and-inventory-management"></a>Változás- és készletkezelés engedélyezése

A virtuális gép módosításának és készletkezelésének engedélyezése:

1. Az ablak bal oldalán válassza a **Virtuális gépek**lehetőséget.
1. Válasszon egy virtuális gép a listából.
1. A Virtuálisgép ablak **műveletek** területén válassza a **Készlet** vagy a **Követés módosítása lehetőséget.**
1. Megnyílik **a Változáskövetés és készlet engedélyezése** ablaktábla.

Konfigurálja a helyet, a Log Analytics-munkaterületet és az Automation-fiókot, majd válassza **az Engedélyezés lehetőséget.** Ha a beállítások halványan jelennek meg, a virtuális gép hez már engedélyezve van egy automatizálási megoldás. Ebben az esetben a már engedélyezett munkaterületi és automation-fiókot kell használni.

Annak ellenére, hogy a megoldások külön jelennek meg a menüben, ugyanazok a megoldások. Ha engedélyezi az egyiket, a virtuális gépen mindkét megoldás engedélyezve lesz.

![A változások és a leltár követésének engedélyezése](./media/tutorial-monitoring/manage-inventory-enable.png)

Miután a megoldás engedélyezve lett, eltarthat egy ideig, amíg a virtuális gép készletgyűjtése, mielőtt az adatok megjelennek.

### <a name="track-changes"></a>Változások követése

A virtuális gépen az **MŰVELETEK**csoportban válassza a **Követés módosítása** lehetőséget, majd a **Beállítások szerkesztése**lehetőséget. Megnyílik **a Változáskövetés** ablaktábla. Jelölje be a követni kívánt beállításokat, majd válassza a **+ Hozzáadás** gombot.

A Windows rendszerben a következő beállítások érhetők el:

* Windows-beállításjegyzék
* Windows-fájlok

A változások követéséről a [Virtuálisgép módosításaielhárításcímű](../../automation/automation-tutorial-troubleshoot-changes.md)témakörben talál részletes információt.

### <a name="view-inventory"></a>Leltár megtekintése

A virtuális gépen válassza **a Készlet** lehetőséget az **MŰVELETEK**csoportban. A **Szoftver** lapon található egy táblázat, amely a talált szoftvert mutatja. Az egyes szoftverrekordok magas szintű részletei megjelennek a táblázatban. Ezek közé tartozik a szoftver neve, verziója, közzétevője és utolsó frissítési ideje.

![Leltár megtekintése](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Tevékenységnaplók és -módosítások figyelése

A virtuális gép **Változáskövetési** ablakában válassza **a Tevékenységnapló-kapcsolat kezelése lehetőséget** az **Azure-tevékenység naplóablakának** megnyitásához. Válassza **a Csatlakozás** lehetőséget, ha a változáskövetést a virtuális gép Azure-tevékenységnaplójához szeretné csatlakoztatni.

Miután a változáskövetés engedélyezve van, nyissa meg a virtuális gép **áttekintő** ablaktábláját, és válassza a **Leállítás** lehetőséget a virtuális gép leállításához. A megjelenő üzenetben kattintson az **Igen** gombra a virtuális gép leállításához. A virtuális gép felszabadítása után válassza az **Indítás** lehetőséget a virtuális gép újraindításához.

A virtuális gép leállítása és újraindítása naplózza az eseményt a tevékenységnaplójában. Lépjen vissza a **Követés módosítása** ablaktáblára, és válassza az **Események** lapot az ablaktábla alján. Egy idő után az események megjelennek a diagramon és a táblázatban. Az egyes események kiválasztásával megtekintheti az adott esemény részletes adatait.

![Változások megtekintése a tevékenységnaplóban](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Az előző diagram az idő múlásával bekövetkezett változásokat mutatja. Az Azure-tevékenységnapló-kapcsolat hozzáadása után a felső vonaldiagram megjeleníti az Azure-tevékenységnapló eseményeit.

Minden sávdiagram-sor más-más nyomon követhető változástípust jelöl. Ezek a típusok linuxos démonok, fájlok, Windows rendszerleíró kulcsok, szoftverek és Windows-szolgáltatások. A **Módosítás** lapon láthatók a módosítás részletei. A módosítások az egyes módosítások sorrendjében jelennek meg, és először a legutóbbi változás jelenik meg.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban konfigurálta és áttekintette a virtuális gép változáskövetésés frissítés-kezelése. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot és a virtuális gép.
> * Windows-frissítések kezelése.
> * A változások és a készlet figyelése.

Lépjen a következő oktatóanyagra a virtuális gép figyelésének megismeréséhez.

> [!div class="nextstepaction"]
> [Virtuális gépek figyelése](tutorial-monitor.md)
