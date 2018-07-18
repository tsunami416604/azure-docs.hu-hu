---
title: A Microsoft Azure és az Azure Monitor klasszikus riasztások áttekintése
description: Riasztások figyelése az Azure erőforrás-metrikák, naplók vagy eseményeket, és értesítést a megadott feltétel teljesülése esetén lehetővé teszik.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: a0abcbdaa7e998413efb717be6e0addc5607ec5c
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114011"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Mik azok a Microsoft Azure klasszikus riasztások?

> [!NOTE]
> Ez a cikk ismerteti, hogyan hozhat létre a régebbi és a klasszikus metrikariasztásokat. Az Azure Monitor most már támogatja a [újabb, a közel valós idejű metrika riasztásokat és a egy riasztások új kezelőfelülete](monitoring-overview-unified-alerts.md). 
>

Riasztások használatával állítható be adatokat, és értesítést kaphat, amikor a feltétel egyezik a legújabb figyelési adatokat.


## <a name="alerts-on-azure-monitor-data"></a>Az adatok az Azure Monitor riasztások
Klasszikus riasztások két típusa van elérhető: metrikákhoz kapcsolódó riasztások és a tevékenységnapló-riasztások.

* **Klasszikus metrikariasztásokat**: ezt a riasztást aktivál, ha egy adott mérőszám értéke átlép egy küszöbértéket, hozzárendelt. A riasztás egy értesítést állít elő, ha az informatikai a "aktiválva" (amikor áthaladnak a küszöbértéket, és a riasztási feltétel nem teljesül). Azt is riasztást "megoldott" (Ha újra áthaladnak a küszöbértéket, és a rendszer már nem teljesül). 

* **Klasszikus tevékenységnapló-riasztások**: A folyamatos átviteli riasztás aktivál egy tevékenységnapló eseményéhez jön létre, hogy megfelel a keresési feltételeknek Ön által hozzárendelt. Ezek a riasztások csak egy állapota "aktív", lehet, mert a a riasztási motor egyszerűen alkalmazza a szűrési feltételeket a bármilyen új eseményre. Ezek a riasztások értesíti, amint egy új Service Health incidens következik be, vagy amikor egy felhasználó vagy alkalmazás műveletet hajt végre egy az előfizetésében például a "virtuális gép törlése."

Szeretne kapni a diagnosztikai naplóadatokat érhető el, amely az Azure monitoron keresztül érhető el, továbbíthatják az adatokat a Log analyticsbe (korábbi nevén az Operations Management Suite), és a egy Log Analytics-lekérdezés riasztás használja. Log Analytics most használja a [módszer új riasztási](monitoring-overview-unified-alerts.md). 

A következő ábra összefoglalja az Azure monitorban adatforrásokat, és javasol, riasztja, hogy ki ezeket az adatokat.

![A riasztások ismertetése](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Az Azure Monitor riasztások (klasszikus) a besorolás
Az Azure klasszikus riasztások és azok funkcióit használja az alábbi kifejezéseket:
* **Riasztási**: feltételek (egy vagy több szabály vagy feltételek) a meghatározását, amely akkor aktiválódik, amikor teljesül.
* **Aktív**: az állapot akkor fordul elő, ha a klasszikus riasztások alapján meghatározott megadott feltétele teljesül.
* **Megoldott**: az állapot akkor fordul elő, ha a klasszikus riasztások alapján meghatározott feltételek már nem teljesül után korábban teljesítettnek.
* **Értesítési**: A művelet, amely alapján legyen végrehajtva, amikor egy klasszikus riasztás aktiválódik.
* **A művelet**: az adott hívás küldött értesítést (például egy e-mailt vagy egy bejegyzés egy webhook URL-címet) a fogadó. Értesítések általában is indíthat több művelet.

## <a name="how-do-i-receive-notifications-from-an-azure-monitor-classic-alert"></a>Hogyan kapom meg az értesítéseket a klasszikus Azure Monitor-riasztásokból
Hagyományosan az Azure-riasztások a különböző szolgáltatások használt saját beépített értesítésekkel módszerek. 

Most az Azure Monitor kínál újrafelhasználható értesítési csoportosítási nevű *Műveletcsoportok*. Műveletcsoportok adjon meg egy értesítési fogadók. Riasztás aktiválásakor a műveletcsoport hivatkozó, akkor minden fogadók, értesítést kap. Ez a funkció lehetővé teszi, hogy újra felhasználhatja a fogadók (például a telefonos mérnök lista) csoportja számos riasztási objektumokhoz. Műveletcsoportok különböző módszerek segítségével támogathatja az értesítéseket. Ezek a metódusok lehetnek egy webhook URL-címet, számos más művelet, e-mailek és SMS-üzenetek küldő könyvelési. További információkért lásd: [létrehozása és kezelése az Azure Portalon Műveletcsoportok](monitoring-action-groups.md). 

Régebbi klasszikus tevékenységnapló-riasztások Műveletcsoportok használata.

Azonban a régebbi metrikákhoz kapcsolódó riasztások Műveletcsoportok ne használjon. Ehelyett a következő műveleteket lehet konfigurálni: 
* E-mail értesítéseket küldhet a szolgáltatás-rendszergazda, a társadminisztrátorok, vagy további e-mail-címek.
* Egy webhookot, amely lehetővé teszi további automation műveletek elindításához hívja meg.

Webhookok engedélyezéséhez automation és a szervizeléshez, például a következő szolgáltatások használatával:
- Azure Automation-runbook
- Azure Functions
- Az Azure Logic App
- Egy külső szolgáltatás

## <a name="next-steps"></a>További lépések
Riasztási szabályok és azok konfigurálását a következő dokumentáció segítségével adatainak beolvasása:

* Tudjon meg többet [metrikák](monitoring-overview-metrics.md)
* Konfigurálása [klasszikus metrikariasztásokat az Azure portal használatával](insights-alerts-portal.md)
* Konfigurálása [PowerShell-lel a klasszikus metrikariasztásokat](insights-alerts-powershell.md)
* Konfigurálása [klasszikus metrikariasztásokat az Azure CLI-vel](insights-alerts-command-line-interface.md)
* Konfigurálása [klasszikus metrikariasztásokat az Azure Monitor REST API használatával](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Tudjon meg többet [tevékenységeket tartalmazó naplók](monitoring-overview-activity-logs.md)
* Konfigurálása [tevékenységnapló-riasztások az Azure portal használatával](monitoring-activity-log-alerts.md)
* Konfigurálása [tevékenységnapló-riasztások Resource Manager használatával](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Tekintse át a [tevékenység log riasztási webhook sémáról](monitoring-activity-log-alerts-webhook.md)
* Tudjon meg többet [Műveletcsoportok](monitoring-action-groups.md)
* Konfigurálása [újabb riasztások](monitor-alerts-unified-usage.md)
