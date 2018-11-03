---
title: Tárolók az Azure lemezkép-ajánlat közzététele |} A Microsoft Docs
description: Az Azure container ajánlat közzétételének módját.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 7533d1a133c9c474bc39f0f64c5f1a8183ab30f8
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979751"
---
# <a name="publish-container-offer"></a>Tároló-ajánlat közzététele

 Miután létrehozott egy új ajánlat az a **új ajánlat** lapon teheti közzé az ajánlatot. Válassza ki **közzététel** a közzétételi folyamat elindításához.

Az alábbi ábrán látható a fő lépések egy "az élő esemény indításra" ajánlat a közzétételi folyamat.

![A tároló az ajánlat közzétételi lépéseket](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Közzétételi lépések részletes leírása

A következő táblázat ismerteti az egyes közzétételi lépések. Az egyes lépések befejezés becsült idejét is kapnak.


|  **Közzétételi lépés**           | **idő**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek ellenőrzése         | 15 perc   | Információt kínálnak, és kínálnak a rendszer érvényesíti a beállításokat.                        |
| Tanúsítvány                  | 1 hét | Az ajánlat az Azure hitelesítő csapata által elemzett. Az ajánlat a vírusok, kártevő szoftverek, biztonsági, megfelelőségi és biztonsági problémákat vizsgálja. Az ajánlat be van jelölve, hogy megfelel-e az összes megfelelőségi feltételeket megtekintéséhez. További információkért lásd: [Előfeltételek](./cpp-prerequisites.md) és [a technikai eszközök előkészítése](./cpp-create-technical-assets.md). Visszajelzés a megadott, ha problémát talál. |
| Csomagolás | 1 óra  | Ajánlat technikai eszközök ügyfél használatra vannak csomagolva, és az érdeklődő rendszerek vannak konfigurálva, és beállítása. |
|  Közzétevő jóváhagyás             |  -        | Végső közzétevő áttekintése és az ajánlat élesíti előtt megerősítést. Az ajánlat (az ajánlat információk lépések) a kijelölt előfizetésekben telepíthet, hogy megfelelnek-e a szükséges összes követelmény.  Válassza ki **élő** , az ajánlat helyezheti át a következő lépéssel. |
| Csomagolás                 | 1 óra | A Befejezett ajánlat marketplace éles rendszerekre és régióban replikálja a rendszer. | 
| Élő                           | 4 nap |Az ajánlat, amely a, a szükséges régiók replikálja, és nyilvánosan elérhetővé. |

A közzétételi folyamat befejezéséhez legfeljebb 10 munkanapon teszi lehetővé, és akkor szabadul fel, az ajánlat. Miután elvégezte a közzétételi folyamat, a tároló ajánlat jelenik meg a [a Microsoft Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>További lépések

[Azure Marketplace-en meglévő tároló ajánlat frissítése](./cpp-update-existing-offer.md)
