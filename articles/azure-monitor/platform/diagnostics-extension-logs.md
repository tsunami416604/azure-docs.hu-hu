---
title: BLOB Storage használata IIS-hez és Table Storage a Azure Monitor eseményeihez | Microsoft Docs
description: A Azure Monitor beolvashatja az Azure-szolgáltatások naplóit, amelyek diagnosztikát írnak a Table Storage vagy a blob Storage-ba írt IIS-naplók számára.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 7edf778dc2429cc1054d1550a513d7461f8e7928
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472556"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Adatok gyűjtése az Azure Diagnostics bővítményből Azure Monitor naplókba
Az Azure Diagnostics bővítmény olyan [Azure monitor ügynöke](agents-overview.md) , amely az Azure-beli számítási erőforrások, például a virtuális gépek vendég operációs rendszerének figyelési adatait gyűjti. Ez a cikk azt ismerteti, hogyan gyűjtheti össze a diagnosztikai bővítmény által gyűjtött adatokat az Azure Storage-ból Azure Monitor naplókba.

> [!NOTE]
> A Azure Monitor Log Analytics ügynöke általában az előnyben részesített módszer, amellyel adatokat gyűjthet a vendég operációs rendszerből Azure Monitor naplókba. Az ügynökök részletes összehasonlítását lásd: [a Azure monitor ügynökök áttekintése](agents-overview.md) .

## <a name="supported-data-types"></a>Támogatott adattípusok
Az Azure Diagnostics bővítmény egy Azure Storage-fiókban tárolja az adatait. Az adatok gyűjtéséhez Azure Monitor naplók esetében a következő helyen kell lennie:

| Napló típusa | Erőforrás típusa | Hely |
| --- | --- | --- |
| IIS-naplók |Virtuális gépek <br> Webes szerepkörök <br> Feldolgozói szerepkörök |WAD-az iis-naplófájlok (Blob Storage) |
| Rendszernapló |Virtuális gépek |LinuxsyslogVer2v0 (a Table Storage) |
| Service Fabric-működési események |A Service Fabric-csomópont |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actors-események |A Service Fabric-csomópont |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Services-események |A Service Fabric-csomópont |WADServiceFabricReliableServiceEventTable |
| Windows-eseménynaplók |A Service Fabric-csomópont <br> Virtuális gépek <br> Webes szerepkörök <br> Feldolgozói szerepkörök |WADWindowsEventLogsTable (a Table Storage) |
| Windows ETW-naplók |A Service Fabric-csomópont <br> Virtuális gépek <br> Webes szerepkörök <br> Feldolgozói szerepkörök |WADETWEventTable (a Table Storage) |

## <a name="data-types-not-supported"></a>Az adattípusok nem támogatottak

- A vendég operációs rendszer teljesítményadatait
- IIS-naplók az Azure websitesból


## <a name="enable-azure-diagnostics-extension"></a>Az Azure Diagnostics bővítmény engedélyezése
A diagnosztikai bővítmény telepítésének és konfigurálásának részleteiért lásd: a [Windows Azure Diagnostics bővítmény (wad) telepítése és konfigurálása](diagnostics-extension-windows-install.md) , illetve a [Linux diagnosztikai bővítmény használata](../../virtual-machines/extensions/diagnostics-linux.md) . Ezzel a képernyőébresztés megadhatja a Storage-fiókot, és konfigurálhatja azokat az adatgyűjtést, amelyeket továbbítani kíván Azure Monitor naplókba.


## <a name="collect-logs-from-azure-storage"></a>Naplók gyűjtése az Azure Storage-ból
Az alábbi eljárással engedélyezheti a diagnosztikai bővítmények adatainak gyűjtését egy Azure Storage-fiókból:

1. A Azure Portal lépjen a **log Analytics munkaterületek** elemre, és válassza ki a munkaterületet.
1. A menü **munkaterület-adatforrások** szakaszában kattintson a **Storage-fiókok naplói** elemre.
2. Kattintson a **Hozzáadás**gombra.
3. Válassza ki azt a **Storage-fiókot** , amely a gyűjteni kívánt adatokat tartalmazza.
4. Válassza ki a gyűjteni kívánt **adattípust** .
5. A forrás értékét a rendszer automatikusan kitölti az adattípus alapján.
6. A konfiguráció mentéséhez kattintson **az OK** gombra.
7. További adattípusok esetén ismételje meg a műveletet.

Körülbelül 30 percen belül megtekintheti a Storage-fiók adatait a Log Analytics munkaterületen. A konfiguráció alkalmazása után tárolási írt adatok csak megjelennek. A munkaterület nem olvassa be a meglévő, a Storage-fiókból származó adatait.

> [!NOTE]
> A portál nem ellenőrzi, hogy a forrás létezik-e a Storage-fiókban, vagy új adatírás történik.



## <a name="next-steps"></a>Következő lépések

* [Naplók és mérőszámok gyűjtése az Azure-szolgáltatásokhoz](collect-azure-metrics-logs.md) a támogatott Azure-szolgáltatásokhoz.
* [Lehetővé teszi](../../azure-monitor/insights/solutions.md) , hogy a megoldások betekintést nyújtsanak az adataiba.
* Az adatelemzéshez [használjon keresési lekérdezéseket](../../azure-monitor/log-query/log-query-overview.md) .
