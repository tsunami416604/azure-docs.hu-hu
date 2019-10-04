---
title: Az Azure CDN-erőforrások állapotfigyelésének |} A Microsoft Docs
description: Útmutató az Azure Resource Health az Azure CDN-erőforrások állapotának monitorozásához.
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
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593442"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Az Azure CDN-erőforrások állapotának figyelése
  
Az Azure CDN a Resource health része a [az Azure resource health](../resource-health/resource-health-overview.md).  Az Azure resource health segítségével CDN-erőforrások állapotának monitorozásához és a gyakorlatban hasznosítható segítséget nyújt a problémák elhárításához.

>[!IMPORTANT] 
>Az Azure CDN a resource health jelenleg csak a globális CDN tartalomkézbesítési és az API-funkciók fiókok.  Az Azure CDN erőforrás állapota nem ellenőrzi az egyes CDN-végpontok.
>
>Az azt jelzi, hogy az Azure CDN a resource health-hírcsatorna lehet akár 15 percet késik.

## <a name="how-to-find-azure-cdn-resource-health"></a>Az Azure CDN a resource health megkeresése

1. Az a [az Azure portal](https://portal.azure.com), tallózással keresse meg a CDN-profilra.

2. Kattintson a **beállítások** gombra.

    ![Beállítások gomb](./media/cdn-resource-health/cdn-profile-settings.png)

3. A *támogatás + hibaelhárítás*, kattintson a **a Resource health**.

    ![CDN erőforrás állapota](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Megjelenik a CDN-erőforrások is megkeresheti a *a Resource health* csempéje a *súgó + támogatás* panelen.  Gyors bevezetés a *súgó + támogatás* kattintva a bekarikázott **?** a jobb felső sarokban a portál.
>
> ![Súgó és támogatás](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Az Azure CDN-specifikus üzenetek

Állapotok kapcsolódnak az Azure CDN a resource health alább találja.

|Message | Javasolt művelet |
|---|---|
|Előfordulhat, hogy egy vagy több CDN-végpontot leállított, eltávolított vagy helytelenül konfigurált | Előfordulhat, hogy egy vagy több CDN-végpontot leállított, eltávolított vagy helytelenül konfigurált.|
|Sajnáljuk, de a CDN-kezelési szolgáltatás jelenleg nem érhető el | Látogasson vissza később állapotának frissítése; Ha a probléma tartósan fennáll, után a megoldás várható időpontjára, forduljon az ügyfélszolgálathoz.|
|Előfordulhat, hogy a CDN-végpontokra negatív hatással lesznek CDN-szolgáltatóink fennálló problémái | Látogasson vissza később állapotának frissítése; A hibaelhárító eszköz használatával megtudhatja, hogyan tesztelheti a forrást, és a CDN-végpontot; Ha a probléma tartósan fennáll, után a megoldás várható időpontjára, forduljon az ügyfélszolgálathoz. |
|A CDN-végpontok konfigurációs módosításainak terjesztése során késedelmek tapasztalhatók | Látogasson vissza később állapotának frissítése; Ha a konfigurációs módosítások teljes nem továbbítódnak a várt időn belül, forduljon az ügyfélszolgálathoz.|
|Sajnáljuk, de a kiegészítő portál betöltése közben problémák merültek fel | Látogasson vissza később állapotának frissítése; Ha a probléma tartósan fennáll, után a megoldás várható időpontjára, forduljon az ügyfélszolgálathoz.|
Sajnáljuk, de problémák merültek fel néhány CDN-szolgáltatónkkal kapcsolatban | Látogasson vissza később állapotának frissítése; Ha a probléma tartósan fennáll, után a megoldás várható időpontjára, forduljon az ügyfélszolgálathoz. |

## <a name="next-steps"></a>További lépések

- [Az Azure resource health – áttekintés](../resource-health/resource-health-overview.md)
- [CDN tömörítési hibáinak elhárítása](./cdn-troubleshoot-compression.md)
- [404-es hibákkal kapcsolatos problémák elhárítása](./cdn-troubleshoot-endpoint.md)