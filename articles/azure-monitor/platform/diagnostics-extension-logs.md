---
title: Blob-tárterület használata az IIS-hez és táblatárolás az Azure Monitor eseményeihez | Microsoft dokumentumok
description: Az Azure Monitor el tudja olvasni az Azure-szolgáltatások naplóit, amelyek a diagnosztikai adatokat táblatárolóba vagy a blobstorage-ba írt IIS-naplókba írják.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 44368ab90abd189c6a8a0792494828c87142eb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672395"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Adatok gyűjtése az Azure diagnosztikai bővítményéből az Azure Monitor-naplókhoz
Az Azure diagnostics bővítmény az Azure Monitor egy [ügynöke,](agents-overview.md) amely az Azure számítási erőforrások vendég operációs rendszeréből, beleértve a virtuális gépeket is, figyelési adatokat gyűjt. Ez a cikk ismerteti, hogyan gyűjti a diagnosztikai bővítmény által gyűjtött adatokat az Azure Storage az Azure Monitor naplók.

> [!NOTE]
> Az Azure Monitor loganalytics-ügynökáltalában az előnyben részesített módszer a vendég operációs rendszerből az Azure Monitor naplók adatok gyűjtésére. Az ügynökök részletes összehasonlítása [az Azure Monitor-ügynökök áttekintése című témakörben](agents-overview.md) olvashat.

## <a name="supported-data-types"></a>Támogatott adattípusok
Az Azure diagnosztikai bővítmény tárolja az adatokat egy Azure Storage-fiókban. Az Azure Monitor naplók gyűjteni ezeket az adatokat, meg kell a következő helyeken:

| Napló típusa | Erőforrás típusa | Hely |
| --- | --- | --- |
| IIS-naplók |Virtuális gépek <br> Webes szerepkörök <br> Dolgozói szerepkörök |wad-iis-logfiles (Blob Storage) |
| Rendszernapló |Virtuális gépek |LinuxsyslogVer2v0 (Táblatároló) |
| A Szolgáltatásháló működési eseményei |Szolgáltatásfabric-csomópontok |WADServiceFabricSystemEventTábla |
| Szolgáltatásfabric megbízható szereplő események |Szolgáltatásfabric-csomópontok |WADServiceFabricReliableActorEventTable tábla |
| Szolgáltatásháló megbízható szolgáltatásesemények |Szolgáltatásfabric-csomópontok |WADServiceFabricReliableServiceEventTábla |
| Windows eseménynaplók |Szolgáltatásfabric-csomópontok <br> Virtuális gépek <br> Webes szerepkörök <br> Dolgozói szerepkörök |WADWindowsEventLogsTable (táblatároló) |
| Windows ETW-naplók |Szolgáltatásfabric-csomópontok <br> Virtuális gépek <br> Webes szerepkörök <br> Dolgozói szerepkörök |WADETWEventTable (Táblatároló) |

## <a name="data-types-not-supported"></a>Az adattípusok nem támogatottak

- A vendég operációs rendszer teljesítményadatai
- IIS-naplók Azure-webhelyekről


## <a name="enable-azure-diagnostics-extension"></a>Azure diagnosztikai bővítmény engedélyezése
A diagnosztikai bővítmény telepítésével és konfigurálásával kapcsolatos részletekért [lásd: A Windows Azure diagnosztikai bővítménytelepítése és konfigurálása (WAD)](diagnostics-extension-windows-install.md) vagy a Linux diagnosztikai bővítmény használata a [metrikák és naplók figyeléséhez című témakört.](../../virtual-machines/extensions/diagnostics-linux.md) Ez alow adja meg a tárfiókot, és konfigurálja az adatok gyűjtését, hogy az Azure Monitor naplók.


## <a name="collect-logs-from-azure-storage"></a>Naplók gyűjtése az Azure Storage-ból
Az alábbi eljárással engedélyezheti a diagnosztikai bővítményadatok gyűjtését egy Azure Storage-fiókból:

1. Az Azure Portalon nyissa meg a **Log Analytics-munkaterületek et,** és válassza ki a munkaterületet.
1. Kattintson **a Storage-fiókok naplói parancsra** a menü **Munkaterület-adatforrások** szakaszában.
2. Kattintson a **Hozzáadás gombra.**
3. Válassza ki a **storage-fiókot,** amely tartalmazza az adatokat gyűjteni.
4. Jelölje ki az összegyűjteni kívánt **adattípust.**
5. A forrás értéke automatikusan kitöltődik az adattípus alapján.
6. A konfiguráció mentéséhez kattintson az **OK** gombra.
7. Ismételje meg a műveletet további adattípusok esetén.

Körülbelül 30 perc alatt láthatja a tárfiókból származó adatokat a Log Analytics-munkaterületen. Csak a konfiguráció alkalmazása után a tárolóba írt adatok jelennek meg. A munkaterület nem olvassa be a már meglévő adatokat a tárfiókból.

> [!NOTE]
> A portál nem ellenőrzi, hogy a forrás létezik-e a tárfiókban, vagy ha új adatok at írnak.



## <a name="next-steps"></a>További lépések

* [Naplók és metrikák gyűjtése az Azure-szolgáltatásokhoz](collect-azure-metrics-logs.md) a támogatott Azure-szolgáltatásokhoz.
* [Megoldások engedélyezése](../../azure-monitor/insights/solutions.md) az adatokba való betekintést nyújt.
* Az adatok [elemzéséhez használjon keresési lekérdezéseket.](../../azure-monitor/log-query/log-query-overview.md)
