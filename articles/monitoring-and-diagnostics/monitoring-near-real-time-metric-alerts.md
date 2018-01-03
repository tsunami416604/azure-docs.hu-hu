---
title: "Majdnem valós idejű metrika riasztások az Azure figyelő |} Microsoft Docs"
description: "Közel valós idejű metrika riasztások engedélyezése az Azure erőforrás-metrikák 1 perces gyakorisággal figyelheti."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: cd1002929ad749ac1742e914a9f2411f09ec91d5
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>Közel valós idejű metrika riasztások (előzetes verzió)
Az Azure figyelő mostantól támogatja a közel valós idejű metrika riasztások (előzetes verzió) nevű metrika riasztások új típusú. Ez a funkció jelenleg nyilvános előzetes verziójához.
Ezek a riasztások eltér a normál metrika riasztások néhány módszer az

- **Továbbfejlesztett késés** -közel valós idejű metrika riasztások figyelheti metrika értékek változásait, amint 1 perc.
- **Metrika feltételek teljesebb körű vezérlése** -közel valós idejű metrika riasztások engedélyezése a felhasználók szélesebb riasztási szabályok meghatározásához. Az értesítések mostantól támogatják az a maximális, minimális, átlagos, és az összes érték a metrikák a figyelést.
- **Egyszerre több metrikák figyelését** -közel valós idejű metrika riasztások több metrikák (jelenleg két) kezelhető egyetlen szabállyal képes figyelni. A figyelmeztetés lekérdezi Ha mindkét a metrikák a megadott ideig megsértik a a vonatkozó küszöbértéket.
- **Moduláris értesítési rendszer** - metrika valós idejű riasztások használata mellett [művelet csoportok](monitoring-action-groups.md). Ez a funkció lehetővé teszi, hogy a felhasználók hozhatnak létre műveletek moduláris módon, és használja fel őket a sok riasztási szabályok.

> [!NOTE]
> Közel valós idejű metrika riasztások funkció jelenleg nyilvános előzetes verziójához. A funkció és a felhasználói élmény van változhat.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Milyen erőforrásokat hozhat létre közel valós idejű metrika riasztások?
Közel valós idejű metrika riasztások által támogatott típusú erőforrások teljes listája:

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>A metrikák többdimenziósak közel valós idejű metrika riasztások
Közel valós idejű metrika riasztások támogatja a metrikák többdimenziósak riasztást küld. Dimenziók egy módszerre, amellyel szűrheti a metrika a megfelelő szintre. Közel valós idejű metrika riasztások a metrikák többdimenziósak támogatottak a következő típusú erőforrások

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (USA régiókban storage-fiókok csak támogatott)
* Microsoft.Storage/storageAccounts/services (USA régiókban storage-fiókok csak támogatott)


## <a name="create-a-near-real-time-metric-alert"></a>Riasztás létrehozása, a közel valós idejű metrika
Jelenleg közel valós idejű metrika riasztásokat csak hozhatók létre az Azure portálon keresztül. Közel valós idejű metrika riasztások PowerShell parancssori felület (CLI) és Azure figyelő REST API-n keresztül konfigurálása támogatása hamarosan elérhető.

A riasztási tapasztalat létrehozása közel valós idejű metrika riasztás át lett helyezve az új **Alerts(Preview)** tapasztalhat. Akkor is, ha az aktuális riasztások lapon látható **hozzáadása közel valós idejű metrika riasztás**, ekkor megnyílik az új felületre.

Létrehozhat egy közel valós idejű metrika riasztás ismertetett lépések [Itt](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="managing-near-real-time-metric-alerts"></a>Közel valós idejű metrika riasztások kezelése
Miután létrehozta a **közel valós idejű metrika riasztás**, ismertetett lépések kezelhető [Itt](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="next-steps"></a>További lépések

* [További tudnivalók a változásról riasztások (előzetes verzió)](monitoring-overview-unified-alerts.md)
* [További tudnivalók a napló riasztásait az Azure-riasztások (előzetes verzió)](monitor-alerts-unified-log.md)
* [További tudnivalók az Azure-ban riasztások](monitoring-overview-alerts.md)