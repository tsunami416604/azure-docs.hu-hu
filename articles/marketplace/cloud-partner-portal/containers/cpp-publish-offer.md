---
title: Azure Containers rendszerkép-ajánlat közzététele | Azure Piactér
description: Azure-tárolóajánlat közzététele.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 8003bf1cb7905d5decf8a6267ea397f1b2c01222
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270162"
---
# <a name="publish-container-offer"></a>Tárolóajánlat közzététele

> [!IMPORTANT]
> 2020. április 13-tól megkezdjük az Azure Container-ajánlatok kezelését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [Azure Container-ajánlat létrehozása](https://aka.ms/CreateContainerOffer) című, az áttelepített ajánlatok kezeléséhez című útmutatóutasításait.

 Miután új ajánlatot hoz létre az **Új ajánlat** lapon, közzéteheti az ajánlatot. A **közzétételi** folyamat elindításához válassza a Közzététel lehetőséget.

Az alábbi ábra a közzétételi folyamat fő lépéseit mutatja be a "go live" ajánlathoz.

![A tárolóajánlat közzétételi lépései](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>A közzétételi lépések részletes leírása

Az alábbi táblázat az egyes közzétételi lépéseket ismerteti. Az egyes lépcsők befejezéséhez becsült idő is meg van adva.


|  **Közzétételi lépés**           | **Time**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek érvényesítése         | 15 min   | Az ajánlati információk és az ajánlatbeállítások érvényesítve vannak.                        |
| Tanúsítvány                  | 1 hét | Az ajánlatot az Azure certification team elemzi. A csapat vírusok, rosszindulatú programok, biztonsági megfelelőség és biztonsági problémák szempontjából ellenőrzi az ajánlatot. Ellenőrzi, hogy az ajánlat megfelel-e az összes jogosultsági feltételnek. További információt az [előfeltételek](./cpp-prerequisites.md) és a technikai eszközök előkészítése című [témakörben talál.](./cpp-create-technical-assets.md) A visszajelzés akkor érhető el, ha problémát talál. |
| Csomagolás | 1 óra  | Az ajánlat technikai eszközei taszanak az ügyfelek használatára, az érdeklődőrendszerek konfigurálása és beállítása. |
|  Kiadói kijelentkezés             |  -        | Végső megjelenítői áttekintés és megerősítés az ajánlat élesedése előtt. Az ajánlat üzembe helyezhető a kiválasztott előfizetések (az ajánlatinformációs lépésekben), annak ellenőrzéséhez, hogy megfelel-e az összes követelménynek.  Válassza **az Élő ugrás** lehetőséget, hogy az ajánlat a következő lépésre lépjen. |
| Csomagolás                 | 1 óra | A kész ajánlat replikálása a piactér i termelési rendszerek és régiók. | 
| Élő                           | 4 nap |Az ajánlat kiadásra kerül, replikálódik a szükséges régiókba, és elérhetővé válik a nyilvánosság számára. |

A közzétételi folyamat befejezéséhez legfeljebb 10 munkanapot kell hagyni, és az ajánlat megjelenik. A közzétételi folyamat befejezése után a tárolóajánlat megjelenik a [Microsoft Azure Piactéren.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)

## <a name="next-steps"></a>További lépések

[Meglévő tárolóajánlat frissítése az Azure Marketplace-en](./cpp-update-existing-offer.md)
