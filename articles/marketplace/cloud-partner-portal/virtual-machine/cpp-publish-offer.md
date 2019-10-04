---
title: Egy virtuálisgép-ajánlat közzététele az Azure Marketplace-en
description: Listák közzététele egy meglévő virtuális gép szükséges lépéseket az Azure Marketplace-en az alakzatot kínálnak.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 08/17/2018
ms.author: pabutler
ms.openlocfilehash: 6796c2871cf8a5928beed2ab557cefdfe8ecaae9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938601"
---
# <a name="publish-a-virtual-machine-offer"></a>Egy virtuálisgép-ajánlat közzététele

 Az utolsó lépés az ajánlat meghatározott a portálon és a kapcsolódó technikai eszközök létrehozása után, hogy küldje el az ajánlatot a közzétételre. A következő ábra szemlélteti a közzétételi folyamat "az élő esemény indításra" a fő lépést:

![Virtuális gép közzétételi lépéseket kínálnak.](./media/publishvm_013.png)

Az alábbi táblázat ismerteti ezeket a lépéseket, és biztosít megszakíthat egy becsült maximális időtartam:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Közzétételi lépés**           | **idő**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek ellenőrzése         | 15 perc   | Információt kínálnak, és kínálnak a rendszer érvényesíti a beállításokat.                        |
| Tesztelje a meghajtó érvényesítési (nem kötelező) | 2 óra | Ha be van jelölve a Test Drive engedélyezése, a Microsoft ellenőrzi, a Test Drive-konfiguráció, a központi telepítés és a kiválasztott régiók közötti replikáció. |
| Tanúsítvány                  | 3 nap | Az ajánlat az Azure hitelesítő csapata által elemzett. Ebben a lépésben elvégzi a vizsgálat a vírusok, kártevő szoftverek, biztonsági, megfelelőségi és biztonsági problémákat. Ha problémát talál, visszajelzés érkezett. |
| Kiépítés                   | 4 nap   | Virtuálisgép-ajánlat a Marketplace-en éles rendszerek replikálja a rendszer.               |
| Formátumokat támogató csomagolási és Érdeklődők generálása regisztráció | \< 1 óra  | Ajánlat technikai eszközök ügyfél használatra vannak csomagolva, és az érdeklődő rendszerek vannak konfigurálva, és beállítása. |
|  Közzétevő jóváhagyás             |  -        | Végső közzétevő áttekintése és az ajánlat élesíti előtt megerősítést. Az ajánlat (az ajánlat információk lépések) a kijelölt előfizetésekben telepíthet, hogy megfelelnek-e a szükséges összes követelmény.  |
| Kiépítés                   | 4 nap | Véglegesített Virtuálisgép-ajánlat marketplace éles rendszerekre és régióban replikálja a rendszer. | 
| Élő                           | 4 nap | Virtuálisgép-ajánlat, amely a, a szükséges régiók replikálja, és nyilvánosan elérhetővé. |
|  |  |

Lehetővé teszi a folyamat befejezéséhez legfeljebb 16 napon.  Nyissa meg a fenti közzétételi lépéseket, miután a Virtuálisgép-ajánlat jelenik meg a [a Microsoft Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/). 

