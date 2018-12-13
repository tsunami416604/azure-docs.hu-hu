---
title: A virtuális gépek (előzetes verzió) ismert problémái az Azure Monitor |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Monitor szolgáltatással kapcsolatos ismert problémák a virtuális gépek esetében az Azure-ban, amely ötvözi az állapotának és teljesítményének figyeléséhez, az Azure virtuális gép operációs rendszerének megoldást. A virtuális gépek az Azure Monitor is automatikusan felderíti az alkalmazás-összetevők és egyéb erőforrások használatával, és feltérképezi a köztük folyó kommunikációt.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: b2a60ddee58a7a9c37582085132ee8a1df767cb4
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190356"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>A virtuális gépek (előzetes verzió) az Azure Monitor szolgáltatással kapcsolatos ismert problémák

Ez a cikk ismerteti az Azure Monitor szolgáltatással kapcsolatos ismert problémák a virtuális gépek esetében az Azure-ban, amely ötvözi az állapotának és teljesítményének figyeléséhez, az Azure virtuális gép operációs rendszerének megoldást. 

Az alábbi ismert problémák az állapotfigyelő szolgáltatással:

- Az állapotfigyelő szolgáltatás engedélyezve van a Log Analytics-munkaterülethez kapcsolódó összes virtuális gépet. Ez tehát még a Ha a művelet kezdődik, és az egyetlen virtuális gép befejeződik.
- Miután tiltsa le a figyelést egy virtuális Gépet a támogatott módszerek használatával, és próbálja ismét üzembe helyezné, üzembe kell helyeznie, ugyanazon a munkaterületen. Ha egy új munkaterületet, és próbálja meg használni az állapot megtekintéséhez, hogy a virtuális gép, rendellenes viselkedés jeleníthet meg.
- Egy Azure virtuális gép eltávolítása vagy törlése, ha a három hét napig virtuális gépek listanézetében jelenik meg. Ezenkívül egy eltávolított vagy törölt virtuális gép állapotát kattintva megnyílik a **egészségügyi diagnosztikai** megtekintheti, és ezután kezdeményezi a betöltés hurkot. A törölt virtuális gép nevét kiválasztva megnyílik egy ablaktábla és a egy üzenet, amely megállapítja, hogy a virtuális gép törölve lett.
- Ebben a kiadásban nem lehet módosítani az adott időszakban és gyakoriságát tartalmazza állapotára vonatkozó feltételek. 
- Nem lehet letiltani a állapotára vonatkozó feltételek. 
- Az üzembe helyezést követően időbe telhet, mielőtt az adatok megjelennek a **Azure Monitor** > **virtuális gépek** > **egészségügyi** ablaktáblán vagy a  **VM-erőforrás** > **Insights** ablaktáblán.
- A Health diagnosztikai gyorsabb, mint bármely más nézetben élmény frissítéseket. Az információk késésével nézetek közötti váltáskor. 
- A riasztások összefoglaló mobilalkalmazásoknak része az Azure Monitor szolgáltatással virtuális gép számára, hogy az észlelt állapotbeli problémák eredménye figyelt Azure virtuális gépek csak a riasztásokat jeleníti meg.
- A **riasztáslistában** az egyetlen virtuális gép és az Azure Monitor ablaktáblán jelennek meg értesítések, amelynek a figyelési feltétel értéke *aktivált* az elmúlt 30 napban. A riasztások nem konfigurálható. Után azonban a **Riasztáslista** panel megnyílik, kattintson az összefoglalás mindkét a figyelő az állapot- és időtartományt szűrő értékét módosítani.
- Az a **riasztáslistában** ablaktáblán, javasoljuk, hogy nem módosítja a **erőforrástípus**, **erőforrás**, és **Monitor Service** szűrők. Ezek már konfigurálva adott a megoldáshoz. Ez a lista nézet megjeleníti a további mezők, mint a **az Azure monitor** > **riasztások** does listanézet.   
- A Linux rendszerű virtuális gépek a **egészségügyi diagnosztikai** a nézet jeleníti meg a virtuális gép, a felhasználó által megadott virtuális gép neve helyett a teljes tartománynév.
- Virtuális gépek leállítása frissítések állapotára vonatkozó feltételek az egyes *kritikus* és mások *kifogástalan*. A hálózati virtuális gép állapota látható *kritikus*.
- Riasztás súlyossága állapota nem módosítható. Azt is csak engedélyezhető vagy letiltható. Emellett néhány súlyossági szinten pedig frissülnek állapotára vonatkozó feltételek állapota alapján.
- Egészségügyi feltétel példányok beállítás módosítása esetén módosítják állapotfigyelő feltételek típus valamennyi példánya az ugyanazon a virtuális gépen. Például ha a küszöb a lemez szabad tárolóhely állapotának feltétel példány megfelelő logikai lemez C: módosítanak, ezt a küszöbértéket vonatkozik az összes többi logikai lemez, amely felderítésének és figyelésének a virtuális gép ugyanazon a.  
- Küszöbértékek állapotára vonatkozó feltételek, amelyek Windows virtuális gép cél nem módosítható, mert a saját állapotokat vannak beállítva, hogy *futó* vagy *elérhető*. Az állapot a lekérdezésénél a [számítási feladatok a figyelő API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), megjeleníti a *ÖsszehasonlítóOperátor* értékét **LessThan** vagy **GreaterThan** együtt egy *küszöbérték* értékét **4** a szolgáltatás vagy az entitás ha:
   - DNS-ügyfél szolgáltatásának állapota – szolgáltatás nem fut. 
   - DHCP-ügyfél szolgáltatásának állapota – szolgáltatás nem fut. 
   - RPC szolgáltatás állapota – szolgáltatás nem fut. 
   - Windows tűzfal szolgáltatásának állapota – szolgáltatás nem fut.
   - Windows Eseménynapló szolgáltatás állapota – szolgáltatás nem fut. 
   - Kiszolgálói szolgáltatás állapota – szolgáltatás nem fut. 
   - Windows távoli felügyeleti szolgáltatásának állapota – a szolgáltatás nem fut. 
   - Fájlrendszer hibája vagy -sérülés – logikai lemez nem érhető el.

- A következő Linux állapotára vonatkozó feltételek küszöbértékek nem módosítható, azok állapotát már be van állítva mert *igaz*. Állapotát jeleníti meg a *ÖsszehasonlítóOperátor* értékkel **LessThan** és *küszöbérték* értékét **1** a lekérdezéskor a Számítási feladatok figyelés API az entitás a környezettől függően:
   - Logikai lemez állapota – a logikai lemez nem online / érhető el
   - Lemez állapota – a lemez nem online / érhető el
   - Hálózati Adapter állapotát - hálózati adapter le van tiltva.  

- A *teljes CPU-kihasználtság* egészségügyi feltétel, a Windows jeleníti meg egy küszöbértéket **4-es nem egyenlő** a portál és a számítási feladatok figyelés API-t. A küszöbérték elérése mikor *teljes CPU-kihasználtság* nagyobb, mint 95 %-os és a rendszer várólista hossza nagyobb, mint 15. Ezen állapot feltétel ebben a kiadásban nem módosítható. 
- Konfigurációs módosítások, például frissítése egy küszöbértéket, akár 30 percet igénybe vehet, még akkor is, ha a portálon vagy a számítási feladatok a figyelő API előfordulhat, hogy azokat azonnal frissítse. 
- Az egyes processzor és a logikai processzor szint állapotára vonatkozó feltételek a Windows nem érhetők el. Windows virtuális gépek csak a teljes CPU-kihasználtság érhető el. 
- Riasztási szabályok esetében minden egyes állapotfigyelő feltétel megadott nem jelennek meg az Azure Portalon. Engedélyezheti vagy letilthatja a Szolgáltatásállapot-riasztás kizárólag a szabály a [számítási feladatok a figyelő API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager). 
- Nem lehet hozzárendelni egy [Azure Monitor műveletcsoport](../../azure-monitor/platform/action-groups.md) health-riasztások az Azure Portalon. Az értesítési beállítás API segítségével konfigurálhatja a műveletcsoport aktiválását, amint egy szolgáltatásállapot-riasztás aktiválódik. Jelenleg a virtuális gépek elleni a Műveletcsoportok rendelhet, hogy az összes *állapotriasztások* ugyanazt a Műveletcsoportok ellen a virtuális gép eseményindító aktiválva. Ellentétben a hagyományos Azure-riasztások, és nincs külön művelet minden szolgáltatásállapot-riasztási szabály a csoport. Emellett a csak adja meg az e-mailben vagy SMS-értesítések konfigurált Műveletcsoportok health-riasztások előállítása esetén támogatottak. 

## <a name="next-steps"></a>További lépések
A követelmények és engedélyezésének a virtuális gépek figyelése módszerekkel, tekintse át [-beli virtuális gépek üzembe helyezése az Azure Monitor](vminsights-onboard.md).
