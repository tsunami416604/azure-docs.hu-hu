---
title: Azure-tárolók rendszerkép-ajánlatának közzététele | Azure piactér
description: Azure Container-ajánlat közzététele.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: cf422e3d77fa0722fe10a52073cf89ab2a76dcc0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823175"
---
# <a name="publish-container-offer"></a>Tárolóajánlat közzététele

 Miután létrehozta az új ajánlatot az **új ajánlat** oldal használatával, közzéteheti az ajánlatot. A közzétételi folyamat elindításához válassza a **Közzététel** lehetőséget.

A következő ábra a közzétételi folyamat fő lépéseit mutatja be, amelyekkel az ajánlat "Go Live".

![A tároló ajánlat közzétételi lépései](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>A közzétételi lépések részletes leírása

Az alábbi táblázat az egyes közzétételi lépéseket ismerteti. Az egyes lépések befejezésének becsült ideje is meg van adva.


|  **Közzétételi lépés**           | **Idő**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek ellenőrzése         | 15 perc   | Az ajánlatra vonatkozó információk és az ajánlat beállításainak ellenőrzése.                        |
| Tanúsítvány                  | 1 hét | Az ajánlatot az Azure minősítési csapat elemzi. Az ajánlat vírusok, kártevő szoftverek, biztonsági megfelelőségi és biztonsági problémák vizsgálatára szolgál. A rendszer ellenőrzi, hogy az ajánlat megfelel-e az összes jogosultsági feltételnek. További információ: [Előfeltételek](./cpp-prerequisites.md) és [a technikai eszközök előkészítése](./cpp-create-technical-assets.md). A visszajelzések megtalálhatók, ha probléma merül fel. |
| Csomagolás | 1 óra  | Az ajánlat technikai eszközei az ügyfelek általi használatra vannak csomagolva, és a vezető rendszerek konfigurálva vannak és konfigurálhatók. |
|  Közzétevő kijelentkezése             |  -        | A kiadó végleges felülvizsgálata és megerősítése az ajánlat életbe lépését megelőzően. Az ajánlatot a kiválasztott előfizetésekben is üzembe helyezheti (az ajánlati információk lépéseiben) annak ellenőrzéséhez, hogy az megfelel-e az összes követelménynek.  Válassza az **élő indítás** lehetőséget, hogy ajánlata a következő lépésre léphet. |
| Csomagolás                 | 1 óra | A kész ajánlatot a piactér éles rendszereiben és régióiban replikálja a rendszer. | 
| Élő                           | 4 nap |Az ajánlat megjelent, replikálva van a szükséges régiókban, és elérhetővé válik a nyilvánosság számára. |

A közzétételi folyamat befejezésére és az ajánlat felszabadítására legfeljebb 10 munkanapot engedélyezhet. A közzétételi folyamat befejezése után a tároló ajánlata megjelenik a [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>További lépések

[Meglévő Container-ajánlat frissítése az Azure Marketplace-en](./cpp-update-existing-offer.md)
