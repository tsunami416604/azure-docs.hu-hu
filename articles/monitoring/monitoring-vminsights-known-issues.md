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
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 819c3e74355cf80c7a998abb8b02b10c9e077059
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47062768"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>A virtuális gépek az Azure Monitor szolgáltatással kapcsolatos ismert problémák

Az alábbi ismert problémák az Azure monitor az állapotfigyelő szolgáltatás a virtuális gépek:

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
- Küszöbértékek bizonyos Windows állapotára vonatkozó feltételek, például a DNS-ügyfél szolgáltatás állapota nem módosítható, a megfelelő állapotba már zárolva van mivel a **futó**, **elérhető** a szolgáltatás vagy az entitás állapota a környezet függően.  Ehelyett a érték szám 4 képviseli, át lesz alakítva a tényleges megjelenítendő karakterlánc egy későbbi kiadásban.  
- Egyes Linux állapotára vonatkozó feltételek küszöbértékek nem módosítható, például a logikai lemez állapota, még a beállítás a nem megfelelő állapot eseményindítás.  Ezek azt jelzik, hogy valami online vagy offline állapotban van, vagy be- vagy kikapcsolása és szerepelnek az és ugyanaz az érték 1 vagy 0 megjelenítésével jelzi.
- Frissítése az erőforrás biztonságicsoport-szűrőt bármely erőforráscsoport használata során az ipari méretekben az Azure monitor -> virtuális gépek -> állapot telepítendő, előre kiválasztott előfizetésben és erőforráscsoportban csoport bármely lista nézet ->, okoz a lista nézet megjelenítéséhez **nincs eredmény**.  Lépjen vissza az Azure Monitor a virtuális gépek -> Állapot lapon -> és válassza ki a kívánt előfizetést és erőforráscsoportot, és navigáljon arra a listanézet.

## <a name="next-steps"></a>További lépések
Felülvizsgálat [előkészítése az Azure Monitor-beli virtuális gépek](monitoring-vminsights-onboard.md) követelmények és módszerek ahhoz, hogy a virtuális gépek figyelése.