---
title: Tárolók az Azure lemezkép-ajánlat közzététele |} Az Azure Marketplace-en
description: Az Azure container ajánlat közzétételének módját.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 9433673e464beb2df74eb4f49851e960d2e7f99c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942637"
---
# <a name="publish-container-offer"></a>Tárolóajánlat közzététele

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
