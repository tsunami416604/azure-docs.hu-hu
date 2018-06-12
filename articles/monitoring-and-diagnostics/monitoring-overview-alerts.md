---
title: A Microsoft Azure és az Azure-figyelő klasszikus riasztások áttekintése
description: Értesítések lehetővé teszik a figyelheti az Azure-erőforrás metrikáit, eseményeket és naplókat, és értesíti, ha a megadott feltétel teljesül.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 89c3975a1212aa991e42e0cce4fe5521b53bd373
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263670"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Mik azok a klasszikus értesítések a Microsoft Azure-ban?

> [!NOTE]
> Ez a cikk ismerteti, hogyan régebbi klasszikus metrika riasztások létrehozásához. Az Azure figyelő most támogatja [újabb közel valós idejű metrika riasztások és egy új riasztások élmény](monitoring-overview-unified-alerts.md). 
>

Értesítések lehetővé teszik adatok konfigurálhatja a platformesemények létrehozási feltételeit és válnak értesítést kap, ha a feltételek egyeznek meg a legújabb figyelési adatok.


## <a name="alerts-on-azure-monitor-data"></a>Azure-figyelő-riasztások
A klasszikus riasztások érhető el – metrika riasztások és a tevékenység napló riasztások két típusa van.

* **Klasszikus metrika riasztások** -a riasztás váltja ki, ha a megadott metrika értékét a hozzárendelt küszöbérték keverve használ. A riasztás egy értesítést állít elő, ha a riasztás "aktív" (Ha a küszöb van átlépte, és a riasztási feltétel nem teljesül), valamint ha az "megoldódik" (a küszöbérték áthaladnak újra, és a feltétel nem teljesül). Az újabb metrika riasztásokhoz az alábbiakban olvasható.

* **Klasszikus tevékenységriasztásokat napló** -egy adatfolyam-továbbítási váltja ki, ha az tevékenységnapló jön létre, hogy megfelel a keresési feltételeknek a hozzárendelt napló-értesítés. Ezek a riasztások rendelkezik csak egy állapota "Aktív", mert a riasztás motor egyszerűen alkalmazza a szűrési feltételeket bármely új eseményre. Ezek a riasztások is használható lesz értesítést kap, egy új szolgáltatás állapota esemény bekövetkezésekor, vagy amikor egy felhasználó vagy alkalmazás egy műveletet végez az előfizetéshez, például "A virtuális gép törlése."

Diagnosztikai naplófájl adatok Azure figyelő keresztül érhető el javasoljuk, hogy az adatok útválasztás be (korábbi nevén OMS) szolgáltatáshoz, és a Log Analytics-lekérdezés riasztások használatával. Naplófájl Analytics jelenleg használja a [módszer új riasztások](monitoring-overview-unified-alerts.md) 

Az alábbi ábra az Azure figyelő és, fogalmilag, hogyan lehet adatok ki riasztást adatforrásokat foglalja össze.

![A riasztások ismertetése](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Besorolás (klasszikus) Azure figyelő riasztások
Azure klasszikus riasztások és azok funkciói a következők használja:
* **Riasztási** -definíciójának feltételek (egy vagy több szabály vagy -feltételeken), amelyek teljesülésekor lesz aktiválva.
* **Aktív** -klasszikus riasztás által megadott feltételek teljesülésekor állapotát.
* **Megoldott** -az állapot, amikor egy klasszikus riasztás által megadott feltételek korábban teljesítettnek után már nem teljesül.
* **Értesítési** - alapú ki a klasszikus váljon aktív riasztások végrehajtott műveletet.
* **A művelet** -egy adott hívás a fogadó (például e-mail címet vagy a webhook URL-CÍMÉT a könyvelési) értesítést küldeni. Értesítések általában elindítható több művelet.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Hogyan értesítést kap a klasszikus Azure-Monitor-riasztás?
Hagyományosan az Azure-riasztások különböző szolgáltatásokhoz használt saját beépített értesítési módszerek. 

Most már Azure figyelő kínál csoportosítási hívott újrafelhasználható értesítést *művelet csoportok*. Művelet csoportok adjon meg egy értesítési fogadókat, és bármikor riasztás aktiválva van a művelet csoport hivatkozó összes fogadók, hogy értesítést kapni. Ez lehetővé teszi, hogy sok riasztás objektumokhoz (például a visszafejtés listán) fogadók csoportosítása felhasználhatja. A művelet csoportok értesítési a webhook URL-CÍMÉT, e-mail címek, SMS-számok és számos, a többi mellett úgy támogatják.  További információkért lásd: [művelet csoportok](monitoring-action-groups.md). 

Régebbi klasszikus tevékenységnapló riasztások művelet csoportok használata.

Azonban a régebbi metrika riasztásokat nem művelet csoportok használata. Ehelyett a következő műveleteket is konfigurálhatja: 
* E-mail értesítéseket küldhet a szolgáltatás-rendszergazda társrendszergazdák, vagy további e-mail-címek.
* Hívja meg a webhook, amely lehetővé teszi további automation műveletek indítása.

Webhook lehetővé teszi a szervizelés, és automatizálási például használatával:
    - Azure Automation-runbook
    - Azure-függvény
    - Az Azure Logic Apps alkalmazást
    - egy külső szolgáltatás

## <a name="next-steps"></a>További lépések
A riasztási szabályok és a konfigurálásuk adatainak beolvasása:

* További információ [metrikák](monitoring-overview-metrics.md)
* Konfigurálása [klasszikus metrika riasztások Azure-portálon](insights-alerts-portal.md)
* Konfigurálása [metrika riasztások klasszikus PowerShell](insights-alerts-powershell.md)
* Konfigurálása [klasszikus metrika riasztások parancssori felület (CLI)](insights-alerts-command-line-interface.md)
* Konfigurálása [klasszikus metrika riasztások Azure figyelő REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* További információ [műveletnapló](monitoring-overview-activity-logs.md)
* Konfigurálása [napló Tevékenységriasztásokat Azure-portálon](monitoring-activity-log-alerts.md)
* Konfigurálása [Tevékenységriasztásokat napló erőforrás-kezelő használatával](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Tekintse át a [műveletnapló riasztási webhook séma](monitoring-activity-log-alerts-webhook.md)
* További információ [művelet csoportok](monitoring-action-groups.md)
* Konfigurálása [újabb riasztások](monitor-alerts-unified-usage.md)
