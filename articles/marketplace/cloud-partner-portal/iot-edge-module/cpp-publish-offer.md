---
title: Publish Azure IoT Edge module offer | Azure Marketplace
description: Az IoT Edge-modul ajánlat közzétételének módjáról.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: c853bd3bad9f02f6824c26fb5d18e9e59d921fe8
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942042"
---
# <a name="publish-iot-edge-module-offer"></a>IoT Edge-modulajánlat közzététele

 Az adatokat azáltal, hogy az új ajánlat létrehozása után a **új ajánlat** lapon teheti közzé az ajánlatot. Válassza ki **közzététel** a közzétételi folyamat elindításához.

Az alábbi ábrán látható a fő lépések egy "az élő esemény indításra" ajánlat a közzétételi folyamat.

![Az IoT Edge-modul közzétételi lépéseket kínálnak.](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Közzétételi lépések részletes leírása

A következő táblázat ismerteti az egyes közzétételi lépésre, az egy becsült időtartam (maximális) egyes lépéseinek befejezéséhez.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Közzétételi lépés**           | **idő**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek ellenőrzése         | 15 perc   | Információt kínálnak, és kínálnak a rendszer érvényesíti a beállításokat.                        |
| Tanúsítvány                  | 2 hét | Az ajánlat az Azure hitelesítő csapata által elemzett. Ebben a lépésben elvégzi a vizsgálat a vírusok, kártevő szoftverek, biztonsági, megfelelőségi és biztonsági problémákat. Azt is ellenőrzi, hogy az IoT Edge-modul ajánlat megfelel-e az összes megfelelőségi feltételeket (lásd: [Előfeltételek](./cpp-prerequisites.md) és [a technikai eszközök előkészítése](./cpp-create-technical-assets.md)). Ha problémát talál, visszajelzés érkezett. |
| Csomagolás | 1 óra  | Ajánlat technikai eszközök ügyfél használatra vannak csomagolva, és az érdeklődő rendszerek vannak konfigurálva, és beállítása. |
|  Közzétevő jelentkezzen ki             |  -        | Végső közzétevő áttekintése és az ajánlat élesíti előtt megerősítést. Az ajánlat (az ajánlat információk lépések) a kijelölt előfizetésekben telepíthet, hogy megfelelnek-e a szükséges összes követelmény.  Válassza ki **élő** , az ajánlat helyezheti át a következő lépéssel. |
| Csomagolás                 | 1 óra | Az ajánlat véglegesített marketplace éles rendszerekre és régióban replikálja a rendszer. | 
| Élő                           | 4 nap |Az ajánlat, amely a, a szükséges régiók replikálja, és nyilvánosan elérhetővé. |

A közzétételi folyamat befejezéséhez legfeljebb 10 munkanapon teszi lehetővé, és akkor szabadul fel, az ajánlat. Miután elvégezte a közzétételi folyamat, az IoT Edge-modul ajánlat jelenik meg a [a Microsoft Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>További lépések

- [Egy meglévő Azure Marketplace-en az IoT Edge modul ajánlat frissítése](./cpp-update-existing-offer.md)
