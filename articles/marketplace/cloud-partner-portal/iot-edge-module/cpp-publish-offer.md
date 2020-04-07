---
title: Az Azure IoT Edge modulajánlat közzététele | Azure Piactér
description: IoT Edge-modulajánlat közzététele.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 2220352f817b488b3992b88920428063edde006e
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745013"
---
# <a name="publish-iot-edge-module-offer"></a>IoT Edge-modulajánlat közzététele

>[!Important]
>2020. március 30-tól megkezdjük az IoT Edge modulajánlatok kezelését a Partner Centerbe. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [IoT Edge-modulajánlat létrehozása](https://aka.ms/AzureCreateIoT) az áttelepített ajánlatok kezeléséhez kövesse az utasításokat.

 Miután létrehozott egy új ajánlatot az **Új ajánlat** lapon található információk megadásával, közzéteheti az ajánlatot. A **közzétételi** folyamat elindításához válassza a Közzététel lehetőséget.

Az alábbi ábra a közzétételi folyamat fő lépéseit mutatja be a "go live" ajánlathoz.

![Az IoT Edge modulajánlat közzétételi lépései](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>A közzétételi lépések részletes leírása

Az alábbi táblázat az egyes közzétételi lépéseket ismerteti, az egyes lépésekkel való befejezéshez időbecsléssel (maximum).
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Közzétételi lépés**           | **Time**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek érvényesítése         | 15 min   | Az ajánlati információk és az ajánlatbeállítások érvényesítve vannak.                        |
| Tanúsítvány                  | 2 hét | Az ajánlatot az Azure certification team elemzi. Ez a lépés vírusokat, rosszindulatú programokat, biztonsági megfelelőséget és biztonsági problémákat keres. Azt is ellenőrzi, hogy ez az IoT Edge modul ajánlat megfelel-e az összes jogosultsági feltételnek (lásd [az előfeltételeket](./cpp-prerequisites.md) és [a technikai eszközök előkészítését).](./cpp-create-technical-assets.md) A probléma esetén visszajelzést kap. |
| Csomagolás | 1 óra  | Az ajánlat technikai eszközei taszanak az ügyfelek használatára, az érdeklődőrendszerek konfigurálása és beállítása. |
|  Kiadói kijelentkezés             |  -        | Végső megjelenítői áttekintés és megerősítés az ajánlat élesedése előtt. Az ajánlat üzembe helyezhető a kiválasztott előfizetések (az ajánlatinformációs lépésekben), annak ellenőrzéséhez, hogy megfelel-e az összes követelménynek.  Válassza **az Élő ugrás** lehetőséget, hogy az ajánlat a következő lépésre lépjen. |
| Csomagolás                 | 1 óra | A véglegesített ajánlat replikálása a piactér termelési rendszereiben és régióiban történik. | 
| Élő                           | 4 nap |Az ajánlat kiadásra kerül, replikálódik a szükséges régiókba, és elérhetővé válik a nyilvánosság számára. |

A közzétételi folyamat befejezéséhez legfeljebb 10 munkanapot kell hagyni, és az ajánlat megjelenik. A közzétételi folyamat befejezése után az IoT Edge modulajánlata megjelenik a [Microsoft Azure Piactéren.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)

## <a name="next-steps"></a>További lépések

- [Meglévő IoT Edge-modulajánlat frissítése az Azure Marketplace-en](./cpp-update-existing-offer.md)
