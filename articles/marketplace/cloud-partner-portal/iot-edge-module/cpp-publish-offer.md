---
title: Azure IoT Edge modul-ajánlat közzététele | Azure piactér
description: IoT Edge modul ajánlatának közzététele.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 2310d7a7cad16009bbb58469190a77eedb0619f8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813838"
---
# <a name="publish-iot-edge-module-offer"></a>IoT Edge-modulajánlat közzététele

 Miután létrehozta az új ajánlatot az **új ajánlat** oldalon található információk megadásával, közzéteheti az ajánlatot. A közzétételi folyamat elindításához válassza a **Közzététel** lehetőséget.

A következő ábra a közzétételi folyamat fő lépéseit mutatja be, amelyekkel az ajánlat "Go Live".

![A IoT Edge modul ajánlatának közzétételi lépései](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>A közzétételi lépések részletes leírása

A következő táblázat ismerteti az egyes közzétételi lépéseket, és az egyes lépések befejezésének időpontját (maximum).
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Közzétételi lépés**           | **Idő**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek ellenőrzése         | 15 perc   | Az ajánlatra vonatkozó információk és az ajánlat beállításainak ellenőrzése.                        |
| Tanúsítvány                  | 2 hét | Az ajánlatot az Azure minősítési csapat elemzi. Ez a lépés a vírusok, a kártevők, a biztonsági megfelelőség és a biztonsági problémák vizsgálatát végzi. Azt is ellenőrzi, hogy a IoT Edge modul ajánlata megfelel-e az összes jogosultsági feltételnek (lásd az [előfeltételeket](./cpp-prerequisites.md) és [a technikai eszközök előkészítését](./cpp-create-technical-assets.md)). A rendszer visszajelzést küld, ha problémát talál. |
| Csomagolás | 1 óra  | Az ajánlat technikai eszközei az ügyfelek általi használatra vannak csomagolva, és a vezető rendszerek konfigurálva vannak és konfigurálhatók. |
|  Közzétevő kijelentkezése             |  -        | A kiadó végleges felülvizsgálata és megerősítése az ajánlat életbe lépését megelőzően. Az ajánlatot a kiválasztott előfizetésekben is üzembe helyezheti (az ajánlati információk lépéseiben) annak ellenőrzéséhez, hogy az megfelel-e az összes követelménynek.  Válassza az **élő indítás** lehetőséget, hogy ajánlata a következő lépésre léphet. |
| Csomagolás                 | 1 óra | A véglegesített ajánlatot a piactér éles rendszereiben és régióiban replikálja a rendszer. | 
| Élő                           | 4 nap |Az ajánlat megjelent, replikálva van a szükséges régiókban, és elérhetővé válik a nyilvánosság számára. |

A közzétételi folyamat befejezésére és az ajánlat felszabadítására legfeljebb 10 munkanapot engedélyezhet. A közzétételi folyamat befejezése után a IoT Edge modul ajánlata megjelenik a [Microsoft Azure Marketplaceban](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>További lépések

- [Meglévő IoT Edge modul-ajánlat frissítése az Azure Marketplace-en](./cpp-update-existing-offer.md)
