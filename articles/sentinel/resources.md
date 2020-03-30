---
title: Hasznos erőforrások az Azure Sentinel használata során| Microsoft dokumentumok
description: Ez a dokumentum hasznos erőforrások listáját tartalmazza az Azure Sentinel használata során.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585271"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Hasznos források az Azure Sentinel használatával való munkához



Ez a cikk azokat az erőforrásokat sorolja fel, amelyek segítségével további információkat kaphat az Azure Sentinel használatával kapcsolatos munkáról.

Azure Logic Apps-összekötők:<https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Könyvvizsgálat és jelentéstétel
Az Azure Sentinel naplói az [Azure-tevékenységnaplókban](../azure-monitor/platform/platform-logs-overview.md)találhatók.

A következő támogatott műveletek naplózhatók.

|Művelet neve|    Erőforrás típusa|
|----|----|
|Munkafüzet létrehozása vagy frissítése  |Microsoft.Insights/munkafüzetek|
|Munkafüzet törlése    |Microsoft.Insights/munkafüzetek|
|Munkafolyamat beállítása   |Microsoft.Logic/munkafolyamatok|
|Munkafolyamat törlése    |Microsoft.Logic/munkafolyamatok|
|Mentett keresés létrehozása    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Mentett keresés törlése    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Riasztási szabályok frissítése |Microsoft.SecurityInsights/alertRules|
|Riasztási szabályok törlése |Microsoft.SecurityInsights/alertRules|
|Riasztási szabály válaszműveletének frissítése |Microsoft.SecurityInsights/alertSzabályok/műveletek|
|Riasztási szabály válaszműveletei törlése |Microsoft.SecurityInsights/alertSzabályok/műveletek|
|Könyvjelzők frissítése   |Microsoft.SecurityInsights/könyvjelzők|
|Könyvjelzők törlése   |Microsoft.SecurityInsights/könyvjelzők|
|Esetek frissítése   |Microsoft.SecurityInsights/Cases|
|Esetvizsgálat frissítése  |Microsoft.SecurityInsights/Esetek/vizsgálatok|
|Esetmegjegyzések létrehozása   |Microsoft.SecurityInsights/Cases/comments|
|Adatösszekötők frissítése |Microsoft.SecurityInsights/dataConnectors|
|Adatösszekötők törlése |Microsoft.SecurityInsights/dataConnectors|
|Beállítások frissítése    |Microsoft.SecurityInsights/settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Naplózási és jelentéskészítési adatok megtekintése az Azure Sentinelben

Ezeket az adatokat megtekintheti az Azure-tevékenységnaplóból az Azure Sentinelbe való streamelésével, ahol ezután kutatásokat és elemzéseket végezhet rajta.

1. Csatlakoztassa az [Azure-tevékenység](connect-azure-activity.md) adatforrását. Ezt követően a naplózási események streamelése egy új táblába a **Naplók** képernyőn nevű AzureActivity.
2. Ezután a KQL használatával kérdezze le az adatokat, mint bármely más táblát.



## <a name="vendor-documentation"></a>Szállítói dokumentáció

| **Szállító**  | **Incidens használata az Azure Sentinelben** | **Hivatkozás**|
|----|----|----|
| GitHub| A közösségi lap eléréséhez használatos| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto között| A CEF konfigurálása| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight (PluralSight) | Kusto Query Language-képzés| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogok és fórumok

Tegye fel kérdéseit az Azure Sentinel [TechCommunity tárhelyén.](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel)

Tekintse meg az Azure Sentinel blogbejegyzéseit a [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) és a [Microsoft Azure szolgáltatásból.](https://azure.microsoft.com/blog/tag/azure-sentinel/)


## <a name="next-steps"></a>További lépések
Ebben a dokumentumban az Azure Sentinel használata során hasznos erőforrások listáját tartalmazza. Az Azure biztonságáról és megfelelőségéről további információkat talál a [Microsoft Azure biztonsági és megfelelőségi blogján.](https://blogs.msdn.com/b/azuresecurity/)
