---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79538830"
---
### <a name="the-data-model-of-the-schema"></a>A séma adatmodellje

|Mező|Leírás|
|----|----|
|**Riasztásneve**|Riasztás megjelenítendő neve|
|**Riasztástípusa**|egyedi riasztásazonosító|
|**Megbízhatósági szint**|(Nem kötelező) A riasztás megbízhatósági szintje (magas/alacsony)|
|**Megbízhatósági pontszám**|(Nem kötelező) A biztonsági riasztás számértéke|
|**Leírás**|A riasztás leírásszövege|
|**Displayname**|A riasztás megjelenítendő neve|
|**EndTime (Befejezési idő)**|A riasztás hatásának befejezési időpontja (a riasztáshoz hozzájáruló utolsó esemény időpontja)|
|**Entitások**|A riasztáshoz kapcsolódó entitások listája. Ez a lista különböző típusú entitások keverékét|
|**Bővített linkek**|(Nem kötelező) Egy táska a riasztáshoz kapcsolódó összes kapcsolathoz. Ez a táska képes tartani keveréke linkek különböző típusú|
|**Bővített tulajdonságok**|Egy zsák további területek, amelyek relevánsak a riasztás|
|**IsIncident (Véletlen)**|Azt határozza meg, hogy a riasztás egy incidens vagy egy rendszeres riasztás. Az incidens olyan biztonsági riasztás, amely több riasztást összesít egyetlen biztonsági incidensbe|
|**FeldolgozásEndTime**|AZ UTC időbélyege, amelyben a riasztás tavolta|
|**ProductComponentName**|(Nem kötelező) A riasztást létrehozó terméken belüli összetevő neve.|
|**Productname**|állandó ('Azure Security Center')|
|**Szolgáltatóneve**|Használatlan|
|**Szervizeléslépései**|Kézi műveletelemek a biztonsági fenyegetés elhárításához|
|**Erőforrásazonosító**|Az érintett erőforrás teljes azonosítója|
|**Súlyosság**|A riasztás súlyossága (magas/közepes/alacsony/tájékoztató)|
|**SourceComputerId**|egyedi GUID az érintett kiszolgálóhoz (ha a riasztás a kiszolgálón jön létre)|
|**SourceSystem**|Használatlan|
|**StartTime**|A riasztás becsapódási időpontja (a riasztáshoz hozzájáruló első esemény időpontja)|
|**SystemAlertId azonosító**|A biztonsági riasztási példány egyedi azonosítója|
|**TenantId**|annak az előfizetésnek a szülő Azure Active Directory-bérlőjének azonosítója, amely alatt a beolvasott erőforrás található|
|**TimeGenerated**|UTC időbélyeg, amelyen az értékelés történt (A Biztonsági központ bevizsgálati ideje) (megegyezik a DiscoveredTimeUTC-vel)|
|**Típus**|állandó ("SecurityAlert")|
|**Szállítónév**|A riasztást szolgáltató szállító neve (pl. "Microsoft")|
|**VendorOriginalId azonosító**|Használatlan|
|**WorkspaceResourceGroup**|abban az esetben, ha a riasztás olyan virtuális gépen, kiszolgálón, virtuálisgép-méretezési csoporton vagy App Service-példányon jön létre, amely egy munkaterületnek jelent, tartalmazza a munkaterületerőforrás-csoport nevét|
|**WorkspaceSubscriptionId azonosító**|abban az esetben, ha a riasztás olyan virtuális gépen, kiszolgálón, virtuálisgép-méretezési csoporton vagy App Service-példányon jön létre, amely egy munkaterületnek jelent, tartalmazza a munkaterület-előfizetésazonosítót|
|||
