---
title: Oktatóanyag – Windows virtuális gépek figyelése az Azure-ban
description: Ebben az oktatóanyagban megtudhatja, hogyan figyelheti a teljesítményt, és hogyan fedezheti fel a Windows virtuális gépeken futó alkalmazás-összetevőket.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 9e807927a10399b02c2c89359c2ffffaf87ba57b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74112488"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Oktatóanyag: Windows virtuális gép figyelése az Azure-ban

Az Azure-figyelés ügynökök segítségével gyűjti a rendszerindítási és teljesítményadatokat az Azure virtuális gépekről, tárolja ezeket az adatokat az Azure storage-ban, és elérhetővé teszi azokat a portálon, az Azure PowerShell-modulon és az Azure CLI-n keresztül. A speciális figyelés továbbfejlesztett monitoraz Azure Monitor virtuális gépek hez teljesítménymutatók gyűjtésével, a virtuális gépre telepített alkalmazás-összetevők felderítésével, valamint teljesítménydiagramokat és függőségi térképeket tartalmaz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendszerindítási diagnosztika engedélyezése a virtuális gépen
> * Rendszerindítási diagnosztika megtekintése
> * Virtuálisgép-gazda metrikáinak megtekintése
> * Az Azure-figyelő engedélyezése virtuális gépekhez
> * Virtuális gép teljesítménymutatóinak megtekintése
> * Riasztás létrehozása

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/powershell](https://shell.azure.com/powershell)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Ebben az oktatóanyagban az Azure-monitorozás és a frissítéskezelés konfigurálásához egy Azure-beli windowsos virtuális gép szükséges. Először a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozza létre a virtuális gép [new-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre az *USA keleti régiója* helyen. Ha még nem létezik, létrejön a *myResourceGroupMonitorMonitor* nevű erőforráscsoport a támogató hálózati erőforrásokkal együtt:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Az erőforrások és a virtuális gép létrehozása néhány percig tart.

## <a name="view-boot-diagnostics"></a>Rendszerindítási diagnosztika megtekintése

A Windows rendszerű virtuális gépek rendszerindítása után a rendszerindítási diagnosztikai ügynök rögzíti a képernyőkimenetet, amelyet hibaelhárítási célokra lehet felhasználni. Ez a képesség alapértelmezés szerint engedélyezve van. A rögzített képernyőképek egy Azure storage-fiókban tárolódnak, amely alapértelmezés szerint is létre jön.

A rendszerindítási diagnosztika adatait a [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata) paranccsal kérheti le. A következő példában a rendszerindítási diagnosztika a *c:\* meghajtó gyökérkönyvtárába töltődik le.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Gazdagép metrikáinak megtekintése

A Windows rendszerű virtuális gép egy dedikált gazdagéppel kommunikál az Azure-ban. A rendszer automatikusan összegyűjti a gazdagép metrikáit, amelyek megtekinthetők az Azure Portalon.

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroupMonitor**, majd a **myVM** elemet az erőforrások listájából.
2. A virtuális gazdagép teljesítményének megtekintéséhez kattintson a **Metrikák** elemre a virtuális gép paneljén, majd válassza ki valamelyik gazdagépmetrikát a **Rendelkezésre álló metrikák** területen.

    ![Gazdagép metrikáinak megtekintése](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Speciális figyelés engedélyezése

Az Azure-beli virtuális gép figyelésének engedélyezése az Azure Monitor szolgáltatással:

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroupMonitor**, majd a **myVM** elemet az erőforrások listájából.

2. A virtuális gép lapján a **Figyelés** csoportban válassza **az Insights (előzetes verzió)** lehetőséget.

3. Az **Elemzések (előnézet)** lapon válassza a **Kipróbálás most**lehetőséget.

    ![Virtuális gépek Azure-figyelőjének engedélyezése](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. Az **Azure Monitor Insights bevezetési** lapon, ha egy meglévő Log Analytics-munkaterület ugyanabban az előfizetésben, válassza ki a legördülő listában.  

    A lista előre kiválasztja az alapértelmezett munkaterületet és helyet, ahol a virtuális gép telepítve van az előfizetésben. 

    >[!NOTE]
    >Új Log Analytics-munkaterület létrehozásához a virtuális gép figyelési adatainak tárolására, olvassa [el a Log Analytics-munkaterület létrehozása című témakört.](../../azure-monitor/learn/quick-create-workspace.md) A Log Analytics-munkaterületnek a támogatott régiók egyikéhez kell [tartoznia.](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics)

Miután engedélyezte a figyelést, előfordulhat, hogy néhány percet kell várnia, mielőtt megtekintheti a virtuális gép teljesítménymutatóit.

![Az Azure Monitor engedélyezése virtuális gépek számára a központi telepítés feldolgozásának figyelése](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Virtuális gép teljesítménymutatóinak megtekintése

Az Azure Monitor virtuális gépekhez tartalmaz egy teljesítménydiagramok, amelyek célja több fő teljesítménymutatók (KPI-k) segítségével határozza meg, hogy milyen jól teljesít a virtuális gép. A virtuális gépről való hozzáféréshez hajtsa végre a következő lépéseket.

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroupMonitor**, majd a **myVM** elemet az erőforrások listájából.

2. A virtuális gép lapján a **Figyelés** csoportban válassza **az Insights (előzetes verzió)** lehetőséget.

3. Válassza a **Teljesítmény** lapot.

Ez az oldal nem csak a teljesítménykihasználási diagramokat tartalmazza, hanem egy táblázatot is, amely minden egyes felfedezett logikai lemezhez, kapacitásához, kihasználtságához és az egyes mértékekkel átlaghoz viszonyítva jeleníti meg.

## <a name="create-alerts"></a>Riasztások létrehozása

Létrehozhat megadott teljesítménymetrikákon alapuló riasztásokat. A riasztások segítségével értesülhet például arról, ha az átlagos processzorhasználat meghalad egy bizonyos küszöbértéket vagy a rendelkezésre álló szabad lemezterület egy adott érték alá csökken. A riasztások megjeleníthetők az Azure Portalon vagy elküldhetők e-mailben. A létrehozott riasztásokra adott válaszként aktiválhatók Azure Automation-runbookok vagy Azure Logic Apps-alkalmazások.

A következő példában az átlagos processzorhasználat alapján hozunk létre riasztást.

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroupMonitor**, majd a **myVM** elemet az erőforrások listájából.

2. Kattintson a **Riasztási szabályok** elemre a virtuális gép paneljén, majd a **Metrikariasztás hozzáadása** lehetőségre a riasztási panel felső részén.

3. Adjon meg egy **nevet** a riasztás számára, például *myAlertRule*

4. Ha szeretne riasztást aktiválni, amikor a processzorhasználat 5 percig meghaladja az 1,0 értéket, hagyja változatlanul az összes többi alapértelmezett beállítást.

5. E-mail-értesítés küldéséhez jelölje be az *E-mail küldése a tulajdonosoknak, közreműködőknek és olvasóknak* jelölőnégyzetet. Az alapértelmezett művelet az értesítés megjelenítése a portálon.

6. Kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban konfigurálta és megtekintette a virtuális gép teljesítményét. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Erőforráscsoport és virtuális gép létrehozása
> * Rendszerindítási diagnosztika engedélyezése a virtuális gépen
> * Rendszerindítási diagnosztika megtekintése
> * Gazdagép metrikáinak megtekintése
> * Az Azure-figyelő engedélyezése virtuális gépekhez
> * Virtuálisgép-metrikák megtekintése
> * Riasztás létrehozása

Folytassa a következő oktatóanyaggal, amely az Azure Security Center használatát ismerteti.

> [!div class="nextstepaction"]
> [A virtuális gépek biztonságának kezelése](../../security/fundamentals/overview.md)
