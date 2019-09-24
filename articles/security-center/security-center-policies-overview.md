---
title: Azure Security Center beállítások | Microsoft Docs
description: Azure Security Center beállítások konfigurálása.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/03/2018
ms.author: memildin
ms.openlocfilehash: 4a7254d4ac67ee7d1bf203baf5741638dbc8f3dd
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201608"
---
# <a name="security-center-settings"></a>A Security Center beállításai
Ez a cikk áttekintést nyújt a Security Center beállításairól.

A következő beállítások érhetők el a biztonsági házirendben:

- **Adatgyűjtés**: Meghatározza az ügynök üzembe helyezési [](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) és adatgyűjtési beállításait.
- **Biztonsági házirend**: Meghatározza, hogy mely vezérlők Security Center figyeli és javasolja. Security Centerban szerkesztheti a [biztonsági házirendet](tutorial-security-policy.md) . A [Azure Policy](tutorial-security-policy.md) használatával új definíciókat is létrehozhat, további házirendeket határozhat meg, és szabályzatokat rendelhet hozzá a felügyeleti csoportokhoz. 
- **E-mail-értesítések**: Meghatározza a biztonsági névjegyeket és az [e-mailek értesítési](security-center-provide-security-contact-details.md) beállításait.
- **Árképzési szintek**: Meghatározza az ingyenes vagy a normál [díjszabási beállítást](security-center-pricing.md). A kiválasztott csomag határozza meg, hogy a Security Center mely szolgáltatásai érhetők el a hatókörbe eső erőforrásokhoz. Megadhatja az előfizetések és a munkaterületek szintjét is.

> [!NOTE]
> Ezen előfizetések mindegyikét beállíthatja. A munkaterületek esetében csak az adatgyűjtés és az árképzési szintet állíthatja be.
>


## <a name="who-can-edit-security-policies"></a>Kik módosíthatják a biztonsági házirendeket?
A Security Center szerepköralapú Access Control (RBAC) használ, amely beépített szerepköröket biztosít a felhasználókhoz, csoportokhoz és szolgáltatásokhoz az Azure-ban. Security Center megnyitásakor a felhasználók csak azokat az adatokat látják, amelyekhez hozzáféréssel rendelkeznek. Ez azt jelenti, hogy a felhasználók a *tulajdonos*, *közreműködő*vagy *olvasó* szerepkörhöz vannak rendelve ahhoz az előfizetéshez, amelyhez az erőforrás tartozik. Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

- **Biztonsági olvasó**: Megtekintheti a Security Centerra vonatkozó jogosultságokat, amelyekben javaslatok, riasztások, szabályzatok és állapotok szerepelnek, de nem módosíthatók.
- **Biztonsági rendszergazda**: Ugyanazok a megtekintési jogosultságok, mint a *biztonsági olvasó*, továbbá frissíthetik a biztonsági házirendet, és eltekinthetik az ajánlásokat és a riasztásokat.


## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta Azure Security Center biztonsági szabályzatait. Ha többet szeretne megtudni a Azure Security Centerről, tekintse meg a következő cikkeket:

* [Biztonsági szabályzatok beállítása a Azure Security Centerban](tutorial-security-policy.md): Megtudhatja, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetésekhez.
* [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md): Megtudhatja, hogyan segíti a Security Center javaslatok az Azure-erőforrások védelmében.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md): Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Biztonsági riasztások kezelése és válaszadás a Azure Security Centerban](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Partneri megoldások monitorozása Azure Security Centerekkel](security-center-partner-solutions.md): A partnermegoldások állapotának figyelését ismertető útmutató.
* [Azure Security Center adatbiztonság](security-center-data-security.md): Megtudhatja, hogyan kezeli és védi a Security Center az információkat.
* [Azure Security Center gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Azure biztonsági blog](https://blogs.msdn.com/b/azuresecurity/): Szerezze be a legújabb Azure biztonsági híreket és információkat.
