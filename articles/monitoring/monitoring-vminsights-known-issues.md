---
title: A virtuális gépek ismert problémák az Azure Monitor |} A Microsoft Docs
description: A virtuális gépek az Azure Monitor egy olyan megoldás, az Azure-ban, amely egyesíti az állapotának és teljesítményének figyelése az Azure virtuális gép operációs rendszerének, valamint alkalmazás-összetevők és az egyéb erőforrások függőségeinek automatikus felderítése, és feltérképezi a közötti kommunikációt őket. Ez a cikk ismerteti az ismert problémákat.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/03/2018
ms.author: magoedte
ms.openlocfilehash: 43000993c6a26ef8d44e941f5235ebad7aeee66f
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248060"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>A virtuális gépek az Azure Monitor szolgáltatással kapcsolatos ismert problémák

Az alábbi ismert problémák az Azure monitor az állapotfigyelő szolgáltatás a virtuális gépek:

- Ha egy Azure virtuális gép többé nem létezik, mert lett eltávolítva, vagy törölve, a virtuális gép listanézetben három hét napig ez fog megjelenni. Ezenkívül kattintva eltávolított vagy törölt virtuális gép állapotát szeretné indítsa el a **egészségügyi diagnosztikai** nézetet, majd vonatkozik betöltése hurkot, amely. A törölt virtuális gép nevét kiválasztva indul el egy panel üzenet figyelmezteti, hogy a virtuális gép törölve lett.
- Ebben a kiadásban nem lehet módosítani az adott időszakban és gyakoriságát tartalmazza állapotára vonatkozó feltételek. 
- Nem lehet letiltani a állapotára vonatkozó feltételek. 
- Az előkészítés, után időbe telhet, mielőtt adatok megjelennek az Azure Monitor -> virtuális gépek -> állapot vagy a VM-erőforrás paneljének -> Insights
- Egészségügyi diagnosztikai élmény frissítések gyorsabb, mint bármely más nézetben, így információkat késések tapasztalhat a nézetek közötti váltáskor  
- Összefoglaló megadott virtuális gép állapota az Azure monitorban riasztásokat csak a figyelt Azure virtuális gépeken észlelt állapotbeli problémák aktivált riasztások vonatkoznak.
- A **riasztáslistában** az egyetlen virtuális gép és az Azure Monitor látható az oldal riasztást küld, akiknek a figyelő állapota nézet a "aktivált" az elmúlt 30 nap van beállítva.  Amelyek nem konfigurálhatók. Egyszer kattintva az összegzés után azonban a **Riasztáslista** nézet lap indul el, mindkét a figyelő az állapot- és időtartományt szűrő értékét módosítani.
- Az a **riasztáslistában** nézet lapján, javasoljuk, hogy nem módosítják a **erőforrástípus**, **erőforrás**, és **Monitor Service** váló szűrők vannak konfigurálva a megoldás (a listanézetben látható néhány további mezőt, az Azure monitor képest -> riasztások lista nézet) jellemző.    
- A Linux rendszerű virtuális gépek **egészségügyi diagnosztikai** nézet rendelkezik a virtuális gép, a felhasználó által megadott virtuális gép neve helyett a teljes tartománynév.
- Virtuális gépek leállítása frissíteni fogja a állapotára vonatkozó feltételek némelyike kritikus állapotba, és mások nettó állapota kritikus állapotban lévő virtuális gép Kifogástalan állapotba.
- Riasztás súlyossága állapota nem módosítható, akkor is csak engedélyezhető vagy letiltható.  Emellett néhány súlyossági szint esetén csak frissítés állapotára vonatkozó feltételek állapota alapján.
- Egészségügyi feltétel példányok beállítások módosítása, vezet ugyanazon beállítás módosításának azonos típusú összes állapotfigyelő feltételek példányok között a virtuális gépen. Például ha a lemez küszöbértéket szabad terület egészségügyi feltétel példány megfelelő logikai lemez C: módosul, majd ezt a küszöbértéket érvényes lesz az összes többi logikai lemez felderítésének és figyelésének a virtuális gép ugyanazon a.   
- Küszöbértékek esetében a következő állapotára vonatkozó feltételek célzó Windows virtuális gép nem módosítható, a megfelelő állapotba óta már be van állítva **futó** vagy **elérhető**. A lekérdezéskor a [számítási feladatok a figyelő API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), az egészségügyi állapota látható az *ÖsszehasonlítóOperátor* értékét **LessThan** vagy **GreaterThan**együtt egy *küszöbérték* értékét **4** a szolgáltatás vagy az entitás ha:
   - DNS-ügyfél szolgáltatásának állapota – szolgáltatás nem fut 
   - DHCP-ügyfél szolgáltatásának állapota – szolgáltatás nem fut 
   - RPC szolgáltatás állapota – a szolgáltatás nem fut 
   - Windows tűzfal szolgáltatásának állapota – a szolgáltatás nem fut
   - Windows Eseménynapló szolgáltatás állapota – a szolgáltatás nem fut 
   - Kiszolgálói szolgáltatás állapota – a szolgáltatás nem fut 
   - Windows távoli felügyeleti szolgáltatásának állapota – a szolgáltatás nem fut 
   - Fájlrendszer hibája vagy -sérülés – logikai lemez nem érhető el

- Küszöbértékek számára a következő Linux állapotára vonatkozó feltételek nem módosítható, mert azok állapotát már be van állítva **igaz**.  Az állapotfigyelő állapota látható az *ÖsszehasonlítóOperátor* értékkel **LessThan** és *küszöbérték* értékét **1** a munkaterhelési történő lekérdezéskor Az entitás a környezettől függően figyelési API-val:
   - Logikai lemez – logikai lemez állapota nem online / érhető el
   - Lemez állapota – a lemez nem online / érhető el
   - Hálózati Adapter állapotát - hálózati adapter le van tiltva.  

- **Teljes CPU-kihasználtság** egészségügyi feltétel, a Windows jeleníti meg egy küszöbértéket **4-es nem egyenlő** a portálról, és a számítási feladatok figyelés API-ból lekérdezett, amennyiben 95 %-nál nagyobb CPU-kihasználtság és rendszer-várólistájának hossza nagyobb, mint 15. Ezen állapot feltétel ebben a kiadásban nem módosítható.  
- Például egy küszöbértéket, frissítse a konfigurációs módosítások érvénybe léptetéséhez, annak ellenére, hogy a portál vagy a számítási feladatok a figyelő API azonnali frissítéséhez előfordulhat, hogy akár 30 percet vesz igénybe.  
- Processzormag és logikai processzor szint állapotára vonatkozó feltételek nem érhető el a Windows, csak **teljes CPU-kihasználtság** Windows virtuális gépeken érhető el.  
- Riasztási szabályok meghatározva az egyes állapotfigyelő feltétel nem érhetőek el, az Azure Portalon. Csak azok a konfigurálható a [számítási feladatok a figyelő API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) engedélyezheti vagy tilthatja le a Szolgáltatásállapot-riasztási szabály.  
- Hozzárendelése egy [Azure Monitor műveletcsoport](../monitoring-and-diagnostics/monitoring-action-groups.md) Health riasztások nem lehetséges az Azure Portalról. Az értesítési beállítás API használatával konfigurálja a műveletcsoport aktiválását, amint egy szolgáltatásállapot-riasztás aktiválódik kell. Jelenleg Műveletcsoportok, virtuális gépek ellen lehet hozzárendelni, hogy az összes *állapotriasztások* aktivált ellen a virtuális gép által aktivált az ugyanazon művelet (ok) ban. És nincs külön művelet csoport minden szolgáltatásállapot-riasztási szabály, mint a hagyományos Azure-riasztások esetében. Emellett a csak egy e-mailben vagy SMS küldése értesítse konfigurált Műveletcsoportok health-riasztások előállítása esetén támogatottak. 

## <a name="next-steps"></a>További lépések
Felülvizsgálat [előkészítése az Azure Monitor-beli virtuális gépek](monitoring-vminsights-onboard.md) követelmények és módszerek ahhoz, hogy a virtuális gépek figyelése.