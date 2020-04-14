---
title: Virtuálisgép-ajánlat közzététele az Azure Piactéren
description: Egy meglévő virtuálisgép-ajánlat azure piactéren való közzétételéhez szükséges lépések.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: bb875a5c4ab1b898b64fe22140414e5d5b7830b8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273783"
---
# <a name="publish-a-virtual-machine-offer"></a>Virtuálisgép-ajánlat közzététele

> [!IMPORTANT]
> 2020. április 13-tól megkezdjük az Azure virtuálisgép-ajánlatok kezelését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [Azure virtuális gép ajánlatlétrehozása](https://aka.ms/CreateAzureVMoffer) című, az áttelepített ajánlatok kezelésével című útmutatóutasításait.

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

