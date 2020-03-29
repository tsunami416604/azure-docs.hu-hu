---
title: Az Azure CDN használati mintáinak elemzése | Microsoft dokumentumok
description: Ez a cikk az Azure CDN-termékekhez elérhető elemzési jelentések különböző típusait ismerteti.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: 238dea3c136daf13d3db7be41bed103a0cbf7636
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593684"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Az Azure CDN használati mintáinak elemzése

Miután engedélyezte a CDN-t az alkalmazáshoz, figyelheti a CDN-használatot, ellenőrizheti a kézbesítés állapotát, és elháríthatja a lehetséges problémákat. Az Azure CDN a következő módokon biztosítja ezeket a képességeket: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Alapvető elemzések az Azure diagnosztikai naplóin keresztül

A CDN-végpontokhoz alapvető elemzés érhető el az összes tarifacsomaghoz. Az Azure diagnosztikai naplók lehetővé teszik az alapvető elemzések exportálását az Azure storage, eseményközpontok vagy az Azure Monitor naplókba. Az Azure Monitor naplók olyan megoldást kínál, amely a felhasználó által konfigurálható és testreszabható grafikonok. Az Azure diagnosztikai naplóiról további információt az Azure diagnosztikai naplói című [témakörben talál.](cdn-azure-diagnostic-logs.md)

## <a name="verizon-core-reports"></a>Verizon alapjelentések

Azure CDN-felhasználóként a **Verizon Azure CDN Standard szolgáltatásával,** vagy **a Verizon-profilból származó Azure CDN Premium szolgáltatással,** megtekintheti a Verizon alapjelentéseit a Verizon kiegészítő portálon. A Verizon alapjelentései az Azure Portalon található **Kezelés** lehetőségen keresztül érhetők el, és számos grafikont és nézetet kínálnak. További információ: [Core Reports from Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Verizon egyéni jelentések

Azure CDN-felhasználóként a **Verizon Azure CDN Standard szolgáltatásával,** vagy **a Verizon-profilból származó Azure CDN Premium szolgáltatással,** megtekintheti a Verizon egyéni jelentéseit a Verizon kiegészítő portálon. A Verizon egyéni jelentései az Azure Portalon a **Kezelés** lehetőségen keresztül érhetők el. A Verizon egyéni jelentések lap mutatja a találatok száma vagy az átvitt adatok az egyes szélén CName tartozó Azure CDN-profil. Az adatok bármely időszakban http-válaszkód vagy gyorsítótár-állapot szerint csoportosíthatók. További információt a [Verizon egyéni jelentései című témakörben](cdn-verizon-custom-reports.md)talál.

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium a Verizon jelentéseiből

A **Verizon Azure CDN Premium szolgáltatásával**a következő jelentéseket is elérheti:
   * [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)
   * [Valós idejű statisztikák](cdn-real-time-stats.md)
   * [Határcsomópont teljesítménye](cdn-edge-performance.md)

