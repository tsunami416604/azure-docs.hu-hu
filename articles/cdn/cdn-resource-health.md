---
title: Az Azure CDN-erőforrások állapotfigyelésének |} Microsoft Docs
description: Útmutató az Azure CDN-erőforrások az Azure Resource Health állapotának figyelésére.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 37fe208f5087f318e665e76825127854b4a11c98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842894"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Az Azure CDN-erőforrások állapotfigyelésének
  
Az Azure CDN Resource health része a [Azure-erőforrás állapotának](../resource-health/resource-health-overview.md).  Azure-erőforrás állapotának segítségével CDN erőforrások állapotának figyelésére és fogadására a problémák megoldásához alkalmazható útmutatást.

>[!IMPORTANT] 
>Jelenleg csak fiókok adminisztrátoránál, hogy a globális CDN kézbesítési és API-képességek az Azure CDN erőforrás állapotát.  Az Azure CDN-erőforrás állapota nem ellenőrzi az egyéni CDN-végpontokat.
>
>A jelek, amely Azure CDN erőforrás állapota lehet akár 15 percet késik a.

## <a name="how-to-find-azure-cdn-resource-health"></a>Hol találhatók az Azure CDN erőforrás állapota

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a CDN-profilt.

2. Kattintson a **beállítások** gombra.

    ![Beállítások gomb](./media/cdn-resource-health/cdn-profile-settings.png)

3. A *támogatási + hibaelhárítási*, kattintson a **erőforrás állapota**.

    ![CDN-erőforrás állapota](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Felsorolt CDN erőforrást is megkeresheti a *erőforrás állapota* csempére a *súgó + támogatás* panelen.  Is gyorsan elérheti *súgó + támogatás* a bekarikázott kattintva **?** a jobb felső sarokban, a portál.
>
> ![Súgó és támogatás](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Az Azure CDN-specifikus üzenetek

Azure CDN erőforrás állapota kapcsolódó állapotok alatt található.

|Üzenet | Javasolt művelet |
|---|---|
|Előfordulhat, hogy leállt, eltávolítva, vagy nincs megfelelően konfigurálva a CDN-végpontok közül legalább egyet | Előfordulhat, hogy leállt, eltávolítva, vagy nincs megfelelően konfigurálva a CDN-végpontok közül legalább egyet.|
|Sajnáljuk, a CDN-kezelési szolgáltatás jelenleg nem érhető el | Térjen vissza ide az állapotfrissítések; Ha a probléma továbbra is fennáll, miután a megoldás várható időpontjára, forduljon a támogatási szolgálathoz.|
|Sajnáljuk, a CDN-végpontokat negatív hatással lehet a folyamatban lévő problémák egy részét a CDN-szolgáltatók | Térjen vissza ide az állapotfrissítések; A hibaelhárító eszköz használatával megtudhatja, hogyan tesztelheti a forrás és a CDN-végpont; Ha a probléma továbbra is fennáll, miután a megoldás várható időpontjára, forduljon a támogatási szolgálathoz. |
|Sajnáljuk, a CDN-végpont konfigurációs módosítások tapasztal terjesztési késedelmeket | Térjen vissza ide az állapotfrissítések; Ha a konfigurációs módosításokat a rendszer nem propagálja teljes mértékben be a várt időn belül, forduljon a támogatási szolgálathoz.|
|Sajnáljuk, a kiegészítő portálon betöltése problémák léptek | Térjen vissza ide az állapotfrissítések; Ha a probléma továbbra is fennáll, miután a megoldás várható időpontjára, forduljon a támogatási szolgálathoz.|
Sajnáljuk, a CDN-szolgáltatók némelyike problémák léptek | Térjen vissza ide az állapotfrissítések; Ha a probléma továbbra is fennáll, miután a megoldás várható időpontjára, forduljon a támogatási szolgálathoz. |

## <a name="next-steps"></a>Következő lépések

- [Az Azure-erőforrás állapota – áttekintés](../resource-health/resource-health-overview.md)
- [CDN-tömörítés elhárítása](./cdn-troubleshoot-compression.md)
- [404-es hibák elhárítása](./cdn-troubleshoot-endpoint.md)