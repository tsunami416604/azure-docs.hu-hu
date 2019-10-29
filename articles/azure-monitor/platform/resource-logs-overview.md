---
title: Az Azure Resource logs áttekintése | Microsoft Docs
description: Ismerje meg az Azure-erőforrás-naplók támogatott szolgáltatásait és esemény-sémáját.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: b953f9b5e5fd8c853746caad3047986786bd1317
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989625"
---
# <a name="azure-resource-logs-overview"></a>Azure-erőforrások naplói – áttekintés
Az Azure-erőforrás-naplók olyan [platform-naplók](platform-logs-overview.md) , amelyek az Azure-erőforrások által kibocsátott belső műveleteket írják le. Minden erőforrás-napló közös legfelső szintű sémát használ, és az egyes szolgáltatások rugalmasságával egyedi tulajdonságokat bocsát ki a saját eseményeihez.

> [!NOTE]
> Az erőforrás-naplók korábban diagnosztikai naplóként ismertek.

## <a name="collecting-resource-logs"></a>Erőforrás-naplók összegyűjtése
Az erőforrás-naplókat a támogatott Azure-erőforrások automatikusan létrehozzák, de nem gyűjtik őket, hacsak nem [diagnosztikai beállítással](diagnostic-settings.md)konfigurálják őket. Hozzon létre egy diagnosztikai beállítást az egyes Azure-erőforrások számára a naplók a következő célhelyekre való továbbításához:

| Cél | Alkalmazási helyzet |
|:---|:---|:---|
| [Log Analytics-munkaterület](resource-logs-collect-workspace.md) | Elemezze a naplókat más figyelési adattal, és használja ki Azure Monitor funkciókat, például a naplózási lekérdezéseket és a naplózási riasztásokat. |
| [Azure Storage](resource-logs-collect-storage.md) | Archiválja a naplókat a naplózáshoz vagy a biztonsági mentéshez. |
| [Event hub](resource-logs-stream-event-hubs.md) | A naplók továbbítása harmadik féltől származó naplózási és telemetria rendszerekre.  |

## <a name="compute-resources"></a>Számítási erőforrások
Az erőforrás-naplók eltérnek a vendég operációs rendszer szintű naplóitól az Azure számítási erőforrásaiban. A számítási erőforrásokhoz szükséges, hogy az ügynökök naplókat és mérőszámokat gyűjtsenek a vendég operációs rendszerből, beleértve az eseménynaplókat, a syslog-t és a teljesítményszámlálókat. A [diagnosztikai bővítmény](agents-overview.md#azure-diagnostic-extension) használatával átirányíthatja az Azure-beli virtuális gépek naplófájljait és a [log Analytics-ügynököt](agents-overview.md#log-analytics-agent) a naplók és metrikák összegyűjtésére az Azure-ban, más felhőben és a helyszínen lévő virtuális gépekről egy log Analytics-munkaterületre. A részletekért tekintse [meg a figyelési adatok forrásait Azure monitor](data-sources.md) .

## <a name="resource-logs-schema"></a>Erőforrás-naplók sémája
További információ az erőforrás-naplók sémáról és a kategóriákról: [erőforrás-napló sémája](diagnostic-logs-schema.md). 

## <a name="next-steps"></a>Következő lépések

* [További információ az Azure figyeléséhez használható egyéb Azure platform-naplókról](platform-logs-overview.md) .
