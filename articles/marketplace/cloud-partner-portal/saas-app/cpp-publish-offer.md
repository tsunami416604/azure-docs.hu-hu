---
title: Azure SaaS-alkalmazás ajánlat közzététele |} Az Azure Marketplace-en
description: Közzétételi folyamat, és a lépései egy SaaS-alkalmazás nyújtanak az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: pbutlerm
ms.openlocfilehash: 5f4c38ed6ee19beacc67e29d094a20f5576668d6
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833460"
---
# <a name="publish-a-saas-application-offer"></a>Egy SaaS-alkalmazás ajánlat közzététele

Az adatokat azáltal, hogy az új ajánlat létrehozása után a **új ajánlat** lapon teheti közzé az ajánlatot. Válassza ki **közzététel** a közzétételi folyamat elindításához.

> [!IMPORTANT] 
> SaaS-ajánlat funkció az áttelepítés alatt a [Microsoft Partner Centeren](https://partner.microsoft.com/dashboard/directory).  Minden új gyártó kell használnia a Partner Center új SaaS-ajánlatok létrehozására és kezelésére a meglévő ajánlatok.  Az SaaS-ajánlatok kiadók migrálása folyamatban van batchwise a Cloud Partner portálra, a Partner Center.  A Cloud Partner portálra azt jelzik, ha meghatározott meglévő ajánlatok áttelepítette állapotüzeneteket jelenít meg.

Az alábbi ábrán látható, a magas szintű lépéseket egy új SaaS-alkalmazás ajánlat közzétételéhez.

![Az ajánlat közzétételi lépéseket](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Közzétételi lépések részletes leírása

A következő táblázat ismerteti az egyes közzétételi lépésre, az egy becsült időtartam (maximális) egyes lépéseinek befejezéséhez.

|     **Lépés**       |     **idő**      |  **Leírás**  |
|  ---------------   |  ---------------  |  ---------------  |
|         Tanúsítás           |       2 hét            |          Az ajánlat az Azure hitelesítő csapata által elemzett. Ebben a lépésben elvégzi a vizsgálat a vírusok, kártevő szoftverek, biztonsági, megfelelőségi és biztonsági problémákat. Azt is ellenőrzi, hogy ez az ajánlat megfelel-e az összes megfelelőségi feltételeket (lásd: [Előfeltételek](./cpp-prerequisites.md)). Ha problémát talál, visszajelzés érkezett.         |
|           Csomagolás         |       1 óra            |       Ajánlat technikai eszközök ügyfél használatra vannak csomagolva, és az érdeklődő rendszerek vannak konfigurálva, és beállítása.            |
|        Közzétevő jelentkezzen ki            |         -          |        Végső közzétevő áttekintése és az ajánlat élesíti előtt megerősítést. Az ajánlat (az ajánlat információk lépések) a kijelölt előfizetésekben telepíthet, hogy megfelelnek-e a szükséges összes követelmény. Válassza ki **élő** , az ajánlat helyezheti át a következő lépéssel.           |
|        Csomagolás            |        1 óra           |        Az ajánlat véglegesített marketplace éles rendszerekre és régióban replikálja a rendszer.           |
|        Élő            |       4 nap            |         Az ajánlat, amely a, a szükséges régiók replikálja, és nyilvánosan elérhetővé.          |

A közzétételi folyamat befejezéséhez legfeljebb 10 munkanapon teszi lehetővé, és akkor szabadul fel, az ajánlat. Miután elvégezte a közzétételi folyamat, az SaaS-ajánlat jelenik meg a [a Microsoft Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>További lépések

[Egy létező ajánlat frissítése](./cpp-update-existing-offer.md)
