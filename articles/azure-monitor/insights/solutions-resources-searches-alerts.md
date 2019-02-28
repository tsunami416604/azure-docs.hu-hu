---
title: Mentett keresések felügyeleti megoldások |} A Microsoft Docs
description: Felügyeleti megoldások általában tartalmaznak a mentett keresések, a Log Analytics megoldás által összegyűjtött adatok elemzéséhez. Ez a cikk ismerteti, hogyan adhat meg a Log Analytics Resource Manager-sablon a mentett keresések, így azok felvehetők az eszközkezelési megoldások.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64b79d613463ac2097a89a8e3ca3870b885a3332
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985257"
---
# <a name="adding-log-analytics-saved-searches-to-management-solution-preview"></a>Hozzáadása a Log Analytics (előzetes verzió) megoldás a mentett keresések

> [!IMPORTANT]
> Ez a cikk korábbi verzióját a Resource Manager-sablonnal riasztások létrehozásának részleteit tartalmazza. Ez az információ van kívüli dátuma most [kiterjesztettük a Log Analytics-riasztásokkal, az Azure monitornak](../platform/alerts-extend.md). Naplóriasztás létrehozása Resource Manager-sablonnal a részletekért lásd: [kezelése riasztások Azure Resource-sablonnal](../platform/alerts-log.md#managing-log-alerts-using-azure-resource-template).

> [!NOTE]
> Ez az előzetes dokumentum, jelenleg előzetes verzióban elérhető kezelési megoldások létrehozásához. Semmilyen sémát, az alábbiakban a változhat.

Ez a cikk bemutatja, hogyan adhat meg a mentett kereséseket a Log Analytics egy [Resource Management-sablonnal](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) így is szerepelni a [felügyeleti megoldások](solutions-creating.md).

> [!NOTE]
> Ebben a cikkben a minták használata, paraméterek és változók, kötelező vagy közös felügyeleti megoldások és az itt ismertetett [tervezés és felépítés felügyeleti megoldás az Azure-ban](solutions-creating.md)

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy már megismerkedett az [felügyeleti megoldás létrehozása](solutions-creating.md) és szerkezete egy [Resource Manager-sablon](../../azure-resource-manager/resource-group-authoring-templates.md) és a megoldásfájlt.


## <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
Összes erőforrás a Log Analyticsben tárolják a [munkaterület](../../azure-monitor/platform/manage-access.md). Leírtak szerint [Log Analytics-munkaterületet és Automation-fiók](solutions.md#log-analytics-workspace-and-automation-account), a munkaterület nem található meg a felügyeleti megoldás, de a megoldás telepítése előtt léteznie kell. Ha nem érhető el, akkor a megoldás telepítése sikertelen lesz.

A munkaterület neve van be minden egyes Log Analytics-erőforrás nevét. Ezt a megoldást a **munkaterület** paraméter SavedSearch erőforrás a következő példához hasonlóan.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API-verzió
Egy Resource Manager-sablonban definiált összes Log Analytics erőforrás rendelkezik egy tulajdonság **apiVersion** , amely meghatározza, hogy használja az erőforrás API-verzió.

Az alábbi táblázat az ebben a példában használt erőforrás API-verzió.

| Erőforrás típusa | API-verzió | Lekérdezés |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Esemény &#124; ahol Error == "Error"  |


## <a name="saved-searches"></a>Mentett keresések
Például [mentett keresések](../../azure-monitor/log-query/log-query-overview.md) a megoldás a felhasználók a megoldás által összegyűjtött adatokat lekérdezni. Mentett keresések meg fog jelenni **mentett keresések** az Azure Portalon. Mentett keresés is az egyes riasztások szükség.

[A log Analytics-beli mentett keresés](../../azure-monitor/log-query/log-query-overview.md) erőforrás rendelkezik egy típusú `Microsoft.OperationalInsights/workspaces/savedSearches` és az alábbi struktúrával rendelkeznek. Ez magában foglalja közös változók és paraméterek, így másolhatja, és ez a kódrészlet illessze be a megoldásfájlt, és módosítsa a paraméterek nevei.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Mentett keresés minden egyes tulajdonsága a következő táblázatban leírt.

| Tulajdonság | Leírás |
|:--- |:--- |
| category | A kategória a mentett keresésnek.  Minden mentett keresések megoldásban gyakran oszt ki egyetlen kategória, így együtt vannak csoportosítva a konzolon. |
| DisplayName | A mentett keresésnek, a portálon megjelenítendő neve. |
| lekérdezés | A lekérdezés futtatásához. |

> [!NOTE]
> Szükség lehet escape-karaktereket használja a lekérdezésben, ha JSON-fájlként is értelmezhető karaktereket tartalmaz. Például, ha a lekérdezés a(z) **AzureActivity |} OperationName:"Microsoft.Compute/virtualMachines/write"**, ez a megoldás fájlt kell megírni **AzureActivity |} OperationName: /\"Microsoft.Compute/virtualMachines/write\"**.


## <a name="next-steps"></a>További lépések
* [Nézetek hozzáadása](solutions-resources-views.md) a felügyeleti megoldáshoz.
* [Adja hozzá az Automation-runbookok és egyéb erőforrások](solutions-resources-automation.md) a felügyeleti megoldáshoz.
