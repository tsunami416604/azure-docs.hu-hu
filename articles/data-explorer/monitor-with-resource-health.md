---
title: Az Azure Data Explorer figyelése az Erőforrás állapotával
description: Az Azure Resource Health használatával figyelheti az Azure Data Explorer erőforrásait.
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 9b8b1b40e972d7719ef2cf495ed12cb4ed3478fd
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479369"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>Az Azure Data Explorer figyelése az Erőforrás-állapot használatával (előzetes verzió)

[A Resource Health](/azure/service-health/resource-health-overview) for Azure Data Explorer tájékoztatja az Azure Data Explorer-erőforrás állapotáról, és végrehajtható javaslatokat nyújt a problémák elhárításához. Az erőforrások állapota 1–2 percenként frissül, és az erőforrások aktuális és korábbi állapotát jelenti. 

Az Erőforrás-állapot az Azure Data Explorer-erőforrás állapotát különböző állapot-ellenőrzések vizsgálatával határozza meg, például:
* Erőforrás rendelkezésre állása
* Lekérdezési hibák

## <a name="access-resource-health-reporting"></a>Erőforrás-állapot jelentéskészítés elérése

1. Az [Azure Portalon](https://portal.azure.com/)válassza **a Figyelő** lehetőséget a szolgáltatások listájából.
1. Válassza a **Szolgáltatás állapotának** > **állapota**lehetőséget.
1. Az **Előfizetés** legördülő menüben válassza ki az előfizetést. Az **Erőforrás típusa** legördülő menüben válassza az **Azure Data Explorer**lehetőséget.
1. Az eredményül kapott tábla a kiválasztott előfizetés összes erőforrását felsorolja. Minden erőforrás lesz egy állapot ikon, amely jelzi az erőforrás állapotát.
1. Válassza ki az erőforrást az [erőforrás állapotának](#resource-health-status) és ajánlásainak megtekintéséhez.

    ![Áttekintés](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>Erőforrás állapota

Az erőforrás állapota a következő állapotok egyikével jelenik meg, elérhető, nem érhető el és ismeretlen.

### <a name="available"></a>Elérhető

**A Rendelkezésre álló** állapot azt jelzi, hogy az Azure Data Explorer erőforrás kifogástalan állapotban van, és nincs probléma.

![Elérhető](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>Nem érhető el

A Nem **érhető el** állapot azt jelzi, hogy az Azure Data Explorer erőforrással kapcsolatban folyamatos probléma van, amely miatt nem érhető el a lekérdezések és a betöltés. Előfordulhat például, hogy az Azure Data Explorer-erőforrás csomópontjai váratlanul újraindultak. Ha az Azure Data Explorer-erőforrás hosszabb ideig ebben az állapotban marad, forduljon az [ügyfélszolgálathoz.]()

![Nem érhető el](media/monitor-with-resource-health/unavailable.png)

### <a name="unknown"></a>Ismeretlen

**Az Ismeretlen** állapot azt jelzi, hogy a **Resource Health** több mint 10 perce nem kapott információt erről az Azure Data Explorer-erőforrásról. Ez az állapot nem végleges jelzésaz Azure Data Explorer erőforrás állapotát, de fontos adatpont a hibaelhárítási folyamat. Ha az Azure Data Explorer-fürt a várt módon működik, az állapot néhány percen belül **elérhetőre** változik. Az **Ismeretlen** állapot azt sugallhatja, hogy a platform on-kon ként bekövetkező esemény hatással van az erőforrásra. 

> [!TIP]
> Az Azure Data Explorer fürterőforrás állapota **ismeretlen** lesz, ha leállított állapotban van.

![Ismeretlen](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>Történelmi információk

Az **Erőforrás állapota** ablaktáblán > **Állapotelőzmények**ablaktáblán legfeljebb négyhetes erőforrás-állapot-információhoz férhet hozzá. Válassza ki a nyíl további információt az egészségügyi esemény kapcsolatos problémák jelentett ezen az ablaktáblán. 

![Előzmények](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>További lépések

* [Erőforrás-állapot-riasztások konfigurálása](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [Oktatóanyag: Adatok betöltése és lekérdezésfigyelési adatok betöltése az Azure Data Explorerben](ingest-data-no-code.md)
* [Az Azure Data Explorer teljesítményének, állapotának és használatának figyelése mérőszámokkal](using-metrics.md)