---
title: "Összegyűjti és elemzi az Azure tevékenységi naplóit a Log Analyticshez |} Microsoft Docs"
description: "Az Azure tevékenységi naplóit megoldás segítségével elemezheti és az összes Azure-előfizetések az Azure tevékenységnapló keresést."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: magoedte
ms.openlocfilehash: c13890862c058701268c07d032d6d990c659287a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics"></a>Összegyűjti és elemzi az Azure tevékenység Log Analytics-naplók

![Az Azure tevékenységi naplóit szimbólum](./media/log-analytics-activity/activity-log-analytics.png)

A tevékenység Naplóelemzési megoldás segítségével elemezheti és keressen a [Azure tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) minden Azure-előfizetés között. Az Azure tevékenységnapló, amely az előfizetések erőforrásainak végrehajtott műveletek betekintést nyújt a naplót. A műveletnapló korábbi nevén *naplók* vagy *műveleti naplókat* mivel azt jelenti, hogy az előfizetések eseményeket.

A tevékenység-naplót használó, meghatározhatja a *mi*, *ki*, és *amikor* az esetleges írási műveleteket (PUT, POST, Törlés) arról, hogy az erőforrást az előfizetésében. A műveletek és egyéb kapcsolódó tulajdonságainak állapotának értelmezni is lehet. A műveletnapló nem tartalmazza (GET) olvasási műveletek és az erőforrásokhoz a hagyományos telepítési modellt használó műveletek.

Log Analyticshez való csatlakozáskor az Azure tevékenységi naplóit teheti:

- Az előre definiált nézetekkel tevékenység naplóinak elemzése
- Elemzése és a Keresés és a tevékenység naplókat a több Azure-előfizetéssel
- Több mint 90 nap tevékenységi naplóit tartsa<sup>1</sup>
- Tevékenységi naplóit okozták az egyéb Azure platform-és alkalmazásadatok
- Működési állapot összesítve tevékenységek
- Az Azure-szolgáltatások minden egyes történik tevékenységek tendenciák megtekintése
- Jelentés az összes Azure-erőforrások engedélyezési módosításai
- Az erőforrások érintő vagy szolgáltatáskimaradás esetén egészségügyi problémák azonosításához
- Napló kereséssel összefüggéseket felhasználói tevékenységet, automatikus méretezése műveletek, engedélyezési módosítások és a környezetéből más naplók vagy metrikák szolgáltatásának állapota

<sup>1</sup>alapértelmezés szerint a Naplóelemzési tartja az Azure tevékenységi naplóit 90 napra vonatkozó akkor is, ha az ingyenes szint van. Vagy, ha egy munkaterület megőrzési beállítása legalább 90 nappal lehet. Ha a munkaterületet 90 napnál hosszabb megőrzési, a tevékenységi naplóit a megőrzési időtartam, a munkaterület tartanak.

Naplóelemzési tevékenységi naplóit ingyenesen gyűjt, és a naplók ingyenesen 90 napig tárolja. Ha a naplófájlok 90 napnál hosszabb ideig tárolja, az adatok 90 napnál hosszabb megőrzési díjai adatok gyakorisága.

Ha a számítógép az ingyenes tarifacsomag, tevékenységi naplóit nem vonatkoznak a napi adatok felhasználásához.

## <a name="connected-sources"></a>Összekapcsolt források

A legtöbb más Naplóelemzési megoldásoktól eltérően nem adatgyűjtés tevékenységi naplóit ügynököket. A megoldás által használt összes adatok származnak, közvetlenül az Azure-ból.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| [Windows-ügynökök](log-analytics-windows-agent.md) | Nem | A megoldás a Windows-ügynökök nem gyűjt adatokat. |
| [Linux-ügynökök](log-analytics-linux-agents.md) | Nem | A megoldás a Linux-ügynökök nem gyűjt adatokat. |
| [SCOM felügyeleti csoport](log-analytics-om-agents.md) | Nem | A megoldás nem gyűjt adatokat az ügynökök a csatlakoztatott SCOM felügyeleti csoport. |
| [Azure Storage-fiók](log-analytics-azure-storage.md) | Nem | A megoldás az Azure storage nem gyűjt adatokat. |

## <a name="prerequisites"></a>Előfeltételek

- Azure műveletnapló adatai eléréséhez Azure-előfizetéssel kell rendelkeznie.

## <a name="configuration"></a>Konfiguráció

Hajtsa végre a következő lépésekkel állíthatja be a tevékenység Naplóelemzési megoldás a munkaterületek.

1. A tevékenység Naplóelemzési megoldást engedélyezése a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) vagy ismertetett folyamatot követve [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).
2. Állítsa be ide a Naplóelemzési munkaterület tevékenységi naplóit.
    1. Az Azure portálon, válassza ki a munkaterületet, és kattintson **Azure tevékenységnapló**.
    2. Az egyes előfizetésekhez kattintson az előfizetés nevét.  
        ![Előfizetés hozzáadása](./media/log-analytics-activity/add-subscription.png)
    3. Az a *SubscriptionName* panelen kattintson a **Connect**.  
        ![Csatlakozás az előfizetés](./media/log-analytics-activity/subscription-connect.png)

A megoldás az OMS-portálon való hozzáadásakor a következő csempe láthatja. Jelentkezzen be az Azure portál Azure-előfizetéssel kapcsolódni a munkaterületen.  
![értékelés végrehajtása](./media/log-analytics-activity/tile-performing-assessment.png)

## <a name="using-the-solution"></a>A megoldás használata

A tevékenység Naplóelemzési megoldás a munkaterülethez való hozzáadásakor a **Azure tevékenységi naplóit** csempe hozzáadódik az áttekintő irányítópulthoz. Ez a csempe az Azure-előfizetések, amely hozzáfér a megoldás Azure tevékenység rekordjainak számát jeleníti meg.

![Az Azure tevékenységi naplóit csempe](./media/log-analytics-activity/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Naplózza az Azure-tevékenység megtekintése

Kattintson a **Azure tevékenységi naplóit** csempére kattintva nyissa meg a **Azure tevékenységi naplóit** irányítópult. Az irányítópult a paneleket az alábbi táblázat tartalmazza. Minden egyes panel adott panelhez feltételeknek, a megadott hatókör és időtartomány legfeljebb 10 elemeket sorolja fel. A napló keresési, amely visszaadja az összes rekord kattintva futtathatja **láthatja az összes** alján a panelről, vagy kattintson a panel fejléc.

Tevékenység naplóadatokat csak akkor jelenik meg *után* a tevékenységi naplóit, hogy a megoldás lépjen, adatai nem tekinthetők meg addig konfigurálását.

| Panel | Leírás |
| --- | --- |
| Az Azure tevékenység naplóbejegyzések | Azure tevékenység naplóbejegyzés felső sáv diagramot ábrázol, amely a kijelölt időtartományban rekord összegek és a felső 10 tevékenység hívók listáját jeleníti meg. Kattintson a napló keresése futtatásához a sávdiagram <code>AzureActivity</code>. Egy hívó elemet ad vissza, a cikk összes tevékenység naplóbejegyzések napló keresés futtatásához kattintson. |
| Tevékenységi naplóit állapot szerint | A perecdiagram a kijelölt időtartományban Azure napló állapota látható. Is listáját jeleníti meg az első tíz állapot rekordok listáját. Kattintson a napló keresése futtatásához a diagram <code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code>. Kattintson egy állapot elemet ad vissza az összes tevékenység naplóbejegyzések állapot rekord napló keresés futtatásához. |
| Erőforrás tevékenységi naplóit | Tevékenységi naplóit erőforrások teljes számát jeleníti meg, és az első tíz erőforrások rekord száma az egyes erőforrások sorolja fel. Kattintson a napló keresése futtatásához teljes területre <code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code>, amely jelzi, hogy az összes Azure-erőforrások elérhetővé a megoldás. Kattintson egy erőforrást egy adott erőforráshoz tartozó összes tevékenység rekordot ad vissza napló keresés futtatásához. |
| Erőforrás-szolgáltató tevékenységi naplóit | Tevékenység létrehozott erőforrás-szolgáltató teljes számát naplózza, és felsorolja a tíz jeleníti meg. Kattintson a napló keresése futtatásához teljes területre <code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code>, amely jelzi, hogy az összes Azure-erőforrás-szolgáltatók. Kattintson a napló-keresés vissza az összes tevékenységet rögzíti a szolgáltató futtatásához egy erőforrás-szolgáltató. |

![Az Azure tevékenységi naplóit irányítópult](./media/log-analytics-activity/activity-log-dash.png)

## <a name="next-steps"></a>További lépések

- Hozzon létre egy [riasztás](log-analytics-alerts-creating.md) Ha egy adott tevékenységre történik.
- Használjon [naplófájl-keresési](log-analytics-log-searches.md) a tevékenységi naplóit a részletes információk megtekintéséhez.
