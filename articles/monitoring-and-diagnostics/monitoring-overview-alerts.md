---
title: "A Microsoft Azure és az Azure figyelő riasztások áttekintése |} Microsoft Docs"
description: "Értesítések lehetővé teszik a figyelheti az Azure-erőforrás metrikáit, eseményeket és naplókat, és értesíti, ha a megadott feltétel teljesül."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1f0182f27cfb8441a09abd2031b365a4ab4315a
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="what-are-alerts-in-microsoft-azure"></a>Mik azok a riasztások a Microsoft Azure-ban?
Ez a cikk ismerteti a Microsoft Azure, milyen riasztások különböző forrásokból vonatkozó riasztási, azok előnyeit, és használatuk használatába céljából. Kifejezetten Azure figyelő vonatkozik, de más szolgáltatások mutatók biztosít, valamint riasztásokat. Riasztások kínálnak, amely lehetővé teszi feltételek adatok konfigurálása és válnak értesítést kap, ha a feltételek egyeznek meg a legújabb figyelési adatok Azure-ban figyelésére szolgáló módszer.


## <a name="taxonomy-of-azure-alerts"></a>Az Azure riasztások besorolás
Azure használja a következő feltételek riasztások és azok funkciói:
* **Riasztási** -definíciójának feltételek (egy vagy több szabály vagy -feltételeken), amelyek teljesülésekor lesz aktiválva.
* **Aktív** -a riasztás által megadott feltételek teljesülésekor állapotát.
* **Megoldott** -az állapota, akkor a riasztás által megadott feltételek korábban teljesítettnek után már nem teljesül.
* **Értesítési** - alapú kijelentkezés váljon aktív riasztás végrehajtott műveletet.
* **A művelet** -egy adott hívás a fogadó (például e-mail címet vagy a webhook URL-CÍMÉT a könyvelési) értesítést küldeni. Értesítések általában elindítható több művelet.

    > [!NOTE]
    > Az Azure-ban riasztások fejlődéséhez részeként egy új, egységes felhasználói élmény ebben a nézetben. Az új riasztások (előzetes verzió) felületet használ egy másik besorolást. További információ [riasztások (előzetes verzió)](monitoring-overview-unified-alerts.md). 
    >

## <a name="alerts-in-different-azure-services"></a>Riasztások a különböző Azure-szolgáltatások
Riasztások több Azure-szolgáltatások figyelésének keresztül érhetők el. Információk hogyan és mikor érdemes használni ezeket a szolgáltatásokat [ebben a cikkben találhat](./monitoring-overview.md). Itt érhető el a riasztástípusok részletes információkat Azure között:


| Szolgáltatás | Riasztás típusa | Támogatott szolgáltatások | Leírás |
|---|---|---|---|
| Azure Monitor | [Metrika riasztások](./insights-alerts-portal.md) | [Az Azure-figyelő támogatott metrikák](./monitoring-supported-metrics.md) | Értesítést kapnak, ha minden platform szintű metrika megfelel-e egy adott feltétel (például a virtuális gép CPU % a nagyobb, mint 90 az elmúlt 5 perc). |
|Azure Monitor | [Közel valós idejű metrika riasztások (előzetes verzió)](./monitoring-near-real-time-metric-alerts.md)| [Támogatott erőforrások az Azure-figyelő](./monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for) | Gyorsabb, mint az metrika riasztások kap értesítést, ha egy vagy több platform szintekhez tartozó metrikákat a megadott feltételeknek eleget (például a virtuális gép CPU % 90-nél több és hálózati a nem 500 MB-nál nagyobb az elmúlt 5 percig). |
| Azure Monitor | [Tevékenység napló riasztások](./monitoring-activity-log-alerts.md) | Az összes elérhető az Azure Resource Manager erőforrástípus | Minden értesítést kaphat az új esemény a [Azure tevékenységnapló](./monitoring-overview-activity-logs.md) megegyezik a megadott feltételek (például a "Virtuális gép törlése" művelet esetén myProductionResourceGroup, vagy ha egy új szolgáltatás állapotesemény, amelynél "Active" a állapot akkor jelenik meg). |
| Application Insights | [Metrika riasztások](../application-insights/app-insights-alerts.md) | Bármely alkalmazás adatokat küldhet az Application Insights tagolva | Értesítést kapnak, ha bármely alkalmazás szintű metrika megfelel-e egy adott feltétel (például kiszolgáló válaszideje érték nagyobb, mint 2). |
| Application Insights | [Webalkalmazás-teszt riasztások](../application-insights/app-insights-monitor-web-app-availability.md) | Bármely szeretnék adatokat küldeni a Application Insights tagolva webhelyen | Értesítést kaphat, ha a rendelkezésre állás vagy a webhely válaszképesség nem éri el elvárásainak. |
| Log Analytics | [Napló Analytics riasztások](../log-analytics/log-analytics-alerts.md) | Bármely szolgáltatás szeretnék adatokat küldeni a Log Analyticshez való beállítása | Értesítést kaphat, ha metrika és/vagy esemény adatok Naplóelemzési keresési lekérdezés megfelel bizonyos feltételeknek. |

## <a name="alerts-on-azure-monitor-data"></a>Azure-figyelő-riasztások
Nincsenek elérhető Azure figyelő--metrika riasztások közel valós idejű metrika riasztások (előzetes verzió) és tevékenységnapló riasztások három típusú riasztások ki adatokat.

* **Metrika riasztások** -a riasztás váltja ki, ha a megadott metrika értékét a hozzárendelt küszöbérték keverve használ. A riasztás egy értesítést állít elő, ha a riasztás "aktív" (Ha a küszöb van átlépte, és a riasztási feltétel nem teljesül), valamint ha az "megoldódik" (a küszöbérték áthaladnak újra, és a feltétel nem teljesül). A figyelő az Azure által támogatott elérhető mérőszámok egyre bővülő listáját lásd: [támogatott Azure-figyelő a metrikák listája](monitoring-supported-metrics.md).
* **Majdnem valós idejű metrika riasztások (előzetes verzió)** – ezek a riasztások jelezhetik metrika riasztások hasonló, de néhány módon térnek el egymástól. Először is a nevet javasol, ezek a riasztások is elindíthatja a közel valós időben (olyan gyorsan, mint 1 perces). Akkor is támogatja, több (jelenleg két) mérőszámok figyelését.  A riasztás egy értesítést állít elő, ha a riasztás "aktív" (mindegyik metrikát a küszöbértékek túllépése esetén egy időben és a riasztási feltétel nem teljesül), valamint megoldásakor,"" (ha legalább egy metrikát újra keverve használ a küszöbérték és a következő feltételt: nincs már teljesül).

    > [!NOTE]
    > Közel valós idejű metrika riasztások jelenleg nyilvános előzetes verziójához. A funkció és a felhasználói élmény van változhat.
    >
    >

* **Napló tevékenységriasztásokat** -egy adatfolyam-továbbítási váltja ki, ha az tevékenységnapló jön létre, hogy megfelel a keresési feltételeknek a hozzárendelt napló-értesítés. Ezek a riasztások rendelkezik csak egy állapota "Aktív", mert a riasztás motor egyszerűen alkalmazza a szűrési feltételeket bármely új eseményre. Ezek a riasztások is használható lesz értesítést kap, egy új szolgáltatás állapota esemény bekövetkezésekor, vagy amikor egy felhasználó vagy alkalmazás egy műveletet végez az előfizetéshez, például "A virtuális gép törlése."

Diagnosztikai naplófájl adatok Azure figyelő keresztül érhető el javasoljuk, hogy az adatok útválasztási Log Analyticshez való és a Log Analytics-riasztások segítségével. Az alábbi ábra az Azure figyelő és, fogalmilag, hogyan lehet adatok ki riasztást adatforrásokat foglalja össze.

![A riasztások ismertetése](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-alert"></a>Hogyan jelenik meg egy értesítés az Azure a figyelő riasztást?
Hagyományosan az Azure-riasztások különböző szolgáltatásokhoz használt saját beépített értesítési módszerek. Következő lépésként Azure figyelő kínál nevű művelet csoportjainak újrafelhasználható értesítési csoportosítása. Művelet csoportok – bármely e-mail címek száma, (SMS) telefonszámainak vagy webhook URL-címek – értesítés fogadókat-készletet adjon meg, és bármikor riasztás aktiválva van a művelet csoport hivatkozó összes fogadók, hogy értesítést kapni. Ez lehetővé teszi, hogy sok riasztás objektumokhoz (például a visszafejtés listán) fogadók csoportosítása felhasználhatja. Jelenleg csak tevékenységnapló riasztások teszi a művelet csoportok használatát, de számos más Azure riasztástípusok dolgoznak művelet csoportok használatával is.

Művelet csoportok értesítési e-mail címeket és az SMS-számok mellett a webhook URL-CÍMÉT úgy támogatja. Ez lehetővé teszi a szervizelés, és automatizálási például használatával:
    - Azure Automation-runbook
    - Az Azure-függvény
    - Az Azure Logic Apps alkalmazást
    - egy külső szolgáltatás

Közel valós idejű metrika a riasztások (előzetes verzió), és tevékenységnapló riasztások művelet csoportok használata.

Metrika riasztások még nem használható művelet csoportok. Egyéni metrika riasztást értesítések állítható be:
* E-mail értesítéseket küldhet a szolgáltatás-rendszergazda társrendszergazdák, vagy további e-mail-címek.
* Hívja meg a webhook, amely lehetővé teszi további automation műveletek indítása.

## <a name="next-steps"></a>További lépések
A riasztási szabályok és a konfigurálásuk adatainak beolvasása:

* További információ [metrikák](monitoring-overview-metrics.md)
* Konfigurálása [metrika riasztások Azure-portálon](insights-alerts-portal.md)
* Konfigurálása [metrika PowerShell riasztást küld.](insights-alerts-powershell.md)
* Konfigurálása [metrika riasztások parancssori felület (CLI)](insights-alerts-command-line-interface.md)
* Konfigurálása [metrika riasztások Azure figyelő REST API-n](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* További információ [műveletnapló](monitoring-overview-activity-logs.md)
* Konfigurálása [napló Tevékenységriasztásokat Azure-portálon](monitoring-activity-log-alerts.md)
* Konfigurálása [Tevékenységriasztásokat napló erőforrás-kezelő használatával](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Tekintse át a [műveletnapló riasztási webhook séma](monitoring-activity-log-alerts-webhook.md)
* További információ [közel valós idejű metrika riasztások](monitoring-near-real-time-metric-alerts.md)
* További információ [szolgáltatáshoz értesítést](monitoring-service-notifications.md)
* További információ [művelet csoportok](monitoring-action-groups.md)
* Konfigurálása [figyelmeztetések segítségével riasztások (előzetes verzió)](monitor-alerts-unified-usage.md)
