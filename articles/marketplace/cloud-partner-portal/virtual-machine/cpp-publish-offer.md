---
title: Virtuálisgép-ajánlat közzététele az Azure Piactéren
description: Egy meglévő virtuálisgép-ajánlat azure piactéren való közzétételéhez szükséges lépések.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: c35f721131b997dcfdb0f23a91a39329168b757c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277344"
---
# <a name="publish-a-virtual-machine-offer"></a>Virtuálisgép-ajánlat közzététele

 Az utolsó lépés, miután definiálta az ajánlatot a portálon, és létrehozta a kapcsolódó technikai eszközöket, az ajánlat közzététele. Az alábbi ábra a közzétételi folyamat főbb lépéseit ábrázolja a "go live" számára:

![Közzétételi lépések a virtuálisgép-ajánlathoz](./media/publishvm_013.png)

Az alábbi táblázat ismerteti ezeket a lépéseket, és a befejezésük maximális időbecslését tartalmazza:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Közzétételi lépés**           | **Time**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek érvényesítése         | 15 min   | Az ajánlati információk és az ajánlatbeállítások érvényesítve vannak.                        |
| Tesztvezetés érvényesítése (nem kötelező) | 2 óra | Ha a Test Drive engedélyezését választotta, a Microsoft ellenőrzi a Test Drive konfigurációját, a telepítést és a replikációt a kiválasztott régiókon keresztül. |
| Tanúsítvány                  | 3 nap | Az ajánlatot az Azure certification team elemzi. Ez a lépés vírusokat, rosszindulatú programokat, biztonsági megfelelőséget és biztonsági problémákat keres. A probléma esetén visszajelzést kap. |
| Kiépítés                   | 4 nap   | Virtuális gép ajánlat replikálódik a piactér éles rendszerekben.               |
| Csomagolás és ólomtermelés regisztrációja | \<1 óra  | Az ajánlat technikai eszközei taszanak az ügyfelek használatára, az érdeklődőrendszerek konfigurálása és beállítása. |
|  Kiadói kijelentkezés             |  -        | Végső megjelenítői áttekintés és megerősítés az ajánlat élesedése előtt. Az ajánlat üzembe helyezhető a kiválasztott előfizetések (az ajánlatinformációs lépésekben), annak ellenőrzéséhez, hogy megfelel-e az összes követelménynek.  |
| Kiépítés                   | 4 nap | A véglegesített virtuális gép ajánlata a piactér éles rendszereiben és régióiban replikálódik. | 
| Élő                           | 4 nap | Virtuális gép ajánlat a kiadott, replikálódik a szükséges régiókban, és a nyilvánosság számára elérhetővé. |
|  |  |

A folyamat befejezéséhez legfeljebb 16 napot kell hagyni.  Miután végigment ezeken a közzétételi lépéseken, a virtuális gép ajánlata megjelenik a [Microsoft Azure Piactéren.](https://azuremarketplace.microsoft.com/marketplace/) 

