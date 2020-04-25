---
title: Azure-tárolók rendszerkép-ajánlatának közzététele | Azure piactér
description: Azure Container-ajánlat közzététele.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 58e096a3b25b16e54cf2f18935dcf4a2d44cd767
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146214"
---
# <a name="publish-container-offer"></a>Tárolóajánlat közzététele

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli Container-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Kövesse az [Azure Container-ajánlat létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) az áttelepített ajánlatok kezeléséhez című témakör utasításait.

 Miután létrehozta az új ajánlatot az **új ajánlat** oldal használatával, közzéteheti az ajánlatot. A közzétételi folyamat elindításához válassza a **Közzététel** lehetőséget.

A következő ábra a közzétételi folyamat fő lépéseit mutatja be, amelyekkel az ajánlat "Go Live".

![A tároló ajánlat közzétételi lépései](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>A közzétételi lépések részletes leírása

Az alábbi táblázat az egyes közzétételi lépéseket ismerteti. Az egyes lépések befejezésének becsült ideje is meg van adva.


|  **Közzétételi lépés**           | **Time**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek ellenőrzése         | 15 perc   | Az ajánlatra vonatkozó információk és az ajánlat beállításainak ellenőrzése.                        |
| Tanúsítvány                  | 1 hét | Az ajánlatot az Azure minősítési csapat elemzi. A csapat vírusok, rosszindulatú programok, biztonsági megfelelőség és biztonsági problémák szempontjából ellenőrzi az ajánlatot. Ellenőrzi, hogy az ajánlat megfelel-e az összes jogosultsági feltételnek. További információ: [Előfeltételek](./cpp-prerequisites.md) és [a technikai eszközök előkészítése](./cpp-create-technical-assets.md). A visszajelzések megtalálhatók, ha probléma merül fel. |
| Csomagolás | 1 óra  | Az ajánlat technikai eszközei az ügyfelek általi használatra vannak csomagolva, és a vezető rendszerek konfigurálva vannak és konfigurálhatók. |
|  Közzétevő kijelentkezése             |  -        | A kiadó végleges felülvizsgálata és megerősítése az ajánlat életbe lépését megelőzően. Az ajánlatot a kiválasztott előfizetésekben is üzembe helyezheti (az ajánlati információk lépéseiben) annak ellenőrzéséhez, hogy az megfelel-e az összes követelménynek.  Válassza az **élő indítás** lehetőséget, hogy ajánlata a következő lépésre léphet. |
| Csomagolás                 | 1 óra | A kész ajánlatot a piactér éles rendszereiben és régióiban replikálja a rendszer. | 
| Élő                           | 4 nap |Az ajánlat megjelent, replikálva van a szükséges régiókban, és elérhetővé válik a nyilvánosság számára. |

A közzétételi folyamat befejezésére és az ajánlat felszabadítására legfeljebb 10 munkanapot engedélyezhet. A közzétételi folyamat befejezése után a tároló ajánlata megjelenik a [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>További lépések

[Meglévő Container-ajánlat frissítése az Azure Marketplace-en](./cpp-update-existing-offer.md)
