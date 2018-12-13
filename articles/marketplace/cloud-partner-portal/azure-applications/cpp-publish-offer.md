---
title: Az Azure application ajánlat közzététele |} A Microsoft Docs
description: A folyamat és az Azure Marketplace-en az Azure application ajánlat közzétételének lépéseit ismerteti.
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
ms.date: 12/05/2018
ms.author: pbutlerm
ms.openlocfilehash: fff751d531864faee7bd234ddcf543ae2992a617
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196952"
---
# <a name="publish-azure-application-offer"></a>Az Azure application ajánlat közzététele

Az adatokat azáltal, hogy az új ajánlat létrehozása után a **új ajánlat** lapon teheti közzé az ajánlatot. Válassza ki **közzététel** a közzétételi folyamat elindításához.

Az alábbi ábrán látható a fő lépések egy "az élő esemény indításra" ajánlat a közzétételi folyamat.

  ![Az ajánlat közzétételi lépéseket](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Közzétételi lépések részletes leírása

A következő táblázat ismerteti az egyes közzétételi lépésre, az egy becsült időtartam (maximális) egyes lépéseinek befejezéséhez.
A következő táblázat ismerteti az egyes közzétételi lépések. Az egyes lépések befejezés becsült idejét is kapnak.


|  **Közzétételi lépés**           | **idő**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek ellenőrzése         | 15 perc   | Információt kínálnak, és kínálnak a rendszer érvényesíti a beállításokat.                        |
| Tanúsítvány                  | 1 hét | Az ajánlat az Azure hitelesítő csapata által elemzett. Az ajánlat a vírusok, kártevő szoftverek, biztonsági, megfelelőségi és biztonsági problémákat vizsgálja. Az ajánlat be van jelölve, hogy megfelel-e az összes megfelelőségi feltételeket megtekintéséhez. További információkért lásd: [Előfeltételek](./cpp-prerequisites.md). Visszajelzés a megadott, ha problémát talál. |
| Csomagolás | 1 óra  | Ajánlat technikai eszközök ügyfél használatra vannak csomagolva, és az érdeklődő rendszerek vannak konfigurálva, és beállítása. |
|  Közzétevő jóváhagyás             |  -        | Végső közzétevő áttekintése és az ajánlat élesíti előtt megerősítést. Az ajánlat (az ajánlat információk lépések) a kijelölt előfizetésekben telepíthet, hogy megfelelnek-e a szükséges összes követelmény.  Válassza ki **élő** , az ajánlat helyezheti át a következő lépéssel. |
| Csomagolás                 | 1 óra | A Befejezett ajánlat marketplace éles rendszerekre és régióban replikálja a rendszer. | 
| Élő                           | 4 nap |Az ajánlat, amely a, a szükséges régiók replikálja, és nyilvánosan elérhetővé. |

A közzétételi folyamat befejezéséhez legfeljebb 10 munkanapon teszi lehetővé, és akkor szabadul fel, az ajánlat. Miután elvégezte a közzétételi folyamat, az ajánlat jelenik meg a [a Microsoft Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/).

## <a name="next-steps"></a>További lépések

[Létező ajánlat frissítése](./cpp-update-existing-offer.md)