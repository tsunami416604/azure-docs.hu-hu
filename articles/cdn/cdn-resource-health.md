---
title: Az Azure CDN-erőforrások állapotának figyelése| Microsoft dokumentumok
description: Ismerje meg, hogyan figyelheti az Azure CDN-erőforrások állapotát az Azure Resource Health használatával.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6710f5e5b873f751ad21068acdc15d38574f8378
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593442"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Az Azure CDN-erőforrások állapotának figyelése
  
Az Azure CDN-erőforrás állapota az [Azure-erőforrások állapotának](../resource-health/resource-health-overview.md)egy része.  Az Azure-erőforrások állapotának használatával figyelheti a CDN-erőforrások állapotát, és a problémák elhárításához végrehajtható útmutatást kaphat.

>[!IMPORTANT] 
>Az Azure CDN-erőforrások állapota jelenleg csak a globális CDN-kézbesítési és API-képességek állapotát teszi ki.  Az Azure CDN-erőforrás állapota nem ellenőrzi az egyes CDN-végpontokat.
>
>Az Azure CDN-erőforrások állapotát figyelő jelek akár 15 percet is késhetnek.

## <a name="how-to-find-azure-cdn-resource-health"></a>Az Azure CDN-erőforrások állapotának megkeresése

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a CDN-profilját.

2. Kattintson a **Beállítások** gombra.

    ![Beállítások gomb](./media/cdn-resource-health/cdn-profile-settings.png)

3. A *Támogatás + hibaelhárítás*csoportban kattintson **az Erőforrás állapota**elemre.

    ![CDN-erőforrás állapota](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>A CDN-erőforrásokat a *Súgó + támogatás* panel Erőforrás *állapota* csempén is megtalálhatja.  Gyorsan eljut *Súgó + támogatás* kattintva a bekarikázott? **?** a portál jobb felső sarkában.
>
> ![Súgó és támogatás](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN-specifikus üzenetek

Az Azure CDN-erőforrások állapotával kapcsolatos állapotok az alábbiakban találhatók.

|Üzenet | Javasolt művelet |
|---|---|
|Előfordulhat, hogy egy vagy több CDN-végpontot leállított, eltávolított vagy helytelenül konfigurált | Előfordulhat, hogy egy vagy több CDN-végpontot leállított, eltávolított vagy helytelenül konfigurált.|
|Sajnáljuk, de a CDN-kezelési szolgáltatás jelenleg nem érhető el | Látogasson vissza ide az állapotfrissítésekért; Ha a probléma a várt megoldási idő után is fennáll, forduljon az ügyfélszolgálathoz.|
|Előfordulhat, hogy a CDN-végpontokra negatív hatással lesznek CDN-szolgáltatóink fennálló problémái | Látogasson vissza ide az állapotfrissítésekért; A Hibaelhárítás eszközzel megtudhatja, hogyan tesztelheti az eredeti és a CDN-végpontot; Ha a probléma a várt megoldási idő után is fennáll, forduljon az ügyfélszolgálathoz. |
|A CDN-végpontok konfigurációs módosításainak terjesztése során késedelmek tapasztalhatók | Látogasson vissza ide az állapotfrissítésekért; Ha a konfigurációs módosítások nem lesznek teljesen propagálva a várt időben, forduljon az ügyfélszolgálathoz.|
|Sajnáljuk, de a kiegészítő portál betöltése közben problémák merültek fel | Látogasson vissza ide az állapotfrissítésekért; Ha a probléma a várt megoldási idő után is fennáll, forduljon az ügyfélszolgálathoz.|
Sajnáljuk, de problémák merültek fel néhány CDN-szolgáltatónkkal kapcsolatban | Látogasson vissza ide az állapotfrissítésekért; Ha a probléma a várt megoldási idő után is fennáll, forduljon az ügyfélszolgálathoz. |

## <a name="next-steps"></a>További lépések

- [Az Azure-erőforrások állapotának áttekintése](../resource-health/resource-health-overview.md)
- [CdN-tömörítéssel kapcsolatos problémák elhárítása](./cdn-troubleshoot-compression.md)
- [404-es hibákelhárítás](./cdn-troubleshoot-endpoint.md)