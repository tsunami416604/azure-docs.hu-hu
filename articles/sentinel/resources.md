---
title: Hasznos erőforrások az Azure Sentinel használata esetén | Microsoft Docs
description: Ez a dokumentum az Azure Sentinel használatakor hasznos erőforrások listáját tartalmazza.
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
ms.openlocfilehash: 2465cf74dbf4fbf074aed8f8e4097a236e628425
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660644"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Az Azure Sentinel használatának hasznos erőforrásai



Ez a cikk azokat az erőforrásokat sorolja fel, amelyek segítségével további információkhoz juthat az Azure Sentinel használatáról.

Azure Logic Apps összekötők: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Naplózás és jelentéskészítés
Az Azure Sentinel naplózási naplóit az [Azure-tevékenységek naplójában](../azure-monitor/platform/platform-logs-overview.md)tartják karban.

A következő támogatott műveletek naplózhatók.

|Művelet neve|    Erőforrás típusa|
|----|----|
|Munkafüzet létrehozása vagy frissítése  |Microsoft. bepillantások/munkafüzetek|
|Munkafüzet törlése    |Microsoft. bepillantások/munkafüzetek|
|Munkafolyamat beállítása   |Microsoft. Logic/munkafolyamatok|
|Munkafolyamat törlése    |Microsoft. Logic/munkafolyamatok|
|Mentett keresés létrehozása    |Microsoft. OperationalInsights/munkaterületek/savedSearches|
|Mentett keresés törlése    |Microsoft. OperationalInsights/munkaterületek/savedSearches|
|Riasztási szabályok frissítése |Microsoft. SecurityInsights/alertRules|
|Riasztási szabályok törlése |Microsoft. SecurityInsights/alertRules|
|Riasztási szabály válaszára vonatkozó műveletek frissítése |Microsoft. SecurityInsights/alertRules/műveletek|
|Riasztási szabály válaszára vonatkozó műveletek törlése |Microsoft. SecurityInsights/alertRules/műveletek|
|Könyvjelzők frissítése   |Microsoft. SecurityInsights/könyvjelzők|
|Könyvjelzők törlése   |Microsoft. SecurityInsights/könyvjelzők|
|Esetek frissítése   |Microsoft. SecurityInsights/esetek|
|Frissítési eset vizsgálata  |Microsoft. SecurityInsights/esetek/vizsgálatok|
|Eseti megjegyzések létrehozása   |Microsoft. SecurityInsights/esetek/megjegyzések|
|Adatösszekötők frissítése |Microsoft. SecurityInsights/dataConnectors|
|Adatösszekötők törlése |Microsoft. SecurityInsights/dataConnectors|
|Beállítások frissítése    |Microsoft. SecurityInsights/beállítások|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Naplózási és jelentéskészítési jelentések megtekintése az Azure Sentinel szolgáltatásban

Ezeket az adatok megtekinthetők úgy, hogy az Azure-tevékenység naplójában közvetítik az Azure Sentinel-ba, ahol a kutatást és elemzést végezheti el.

1. Az [Azure-tevékenység](connect-azure-activity.md) adatforrásának összekapcsolása. Ennek elvégzése után a naplózási események egy új táblába kerülnek a **naplók** képernyő AzureActivity nevű képernyőjén.

1. Ezt követően az KQL használatával kérdezheti le az adatlekérdezéseket, mint bármely más táblát.

    Ha például meg szeretné tudni, hogy ki volt az utolsó felhasználó egy adott elemzési szabály szerkesztéséhez, használja a következő lekérdezést (a helyére a `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` szabályt, amelyet meg szeretne keresni):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```



## <a name="vendor-documentation"></a>Szállítói dokumentáció

| **Szállító**  | **Incidens használata az Azure Sentinelben** | **Hivatkozás**|
|----|----|----|
| GitHub| A közösségi lap elérésére szolgál| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| CEF konfigurálása| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kusto Query Language-képzés| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogok és fórumok

Tegye fel kérdéseit az Azure Sentinel [TechCommunity-területére](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) .

Tekintse meg az Azure Sentinel blogbejegyzéseit a [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) és a [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Következő lépések
Ebben a dokumentumban az Azure Sentinel használatakor hasznos erőforrások listáját olvashatja. Az Azure biztonságával és megfelelőségével kapcsolatos további információkat a [Microsoft Azure biztonsági és megfelelőségi blogjában](/archive/blogs/azuresecurity/)talál.