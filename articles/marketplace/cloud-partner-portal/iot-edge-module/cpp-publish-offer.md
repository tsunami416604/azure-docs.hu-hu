---
title: Az Azure IoT Edge-modul ajánlat közzététele |} A Microsoft Docs
description: Az IoT Edge-modul ajánlat közzétételének módjáról.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: dfa512a26334567301812ecefd8c5673b0ea4094
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955900"
---
# <a name="publish-iot-edge-module-offer"></a>IoT Edge-modul ajánlat közzététele

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
