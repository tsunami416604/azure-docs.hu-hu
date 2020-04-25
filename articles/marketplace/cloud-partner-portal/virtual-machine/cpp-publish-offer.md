---
title: Virtuálisgép-ajánlat közzététele az Azure Marketplace-en
description: Felsorolja azokat a lépéseket, amelyek szükségesek egy meglévő virtuálisgép-ajánlat közzétételéhez az Azure Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: dsindona
ms.openlocfilehash: f66ce0c15e976898d5022bf5705a82fe0969ec07
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147661"
---
# <a name="publish-a-virtual-machine-offer"></a>Virtuálisgép-ajánlat közzététele

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuálisgép-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Kövesse az Azure-beli [virtuális gép létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) az áttelepített ajánlatok kezeléséhez című témakör utasításait.

 Az utolsó lépés, miután meghatározta az ajánlatot a portálon, és létrehozta a kapcsolódó technikai eszközöket, a közzétételhez küldje el az ajánlatot. A következő ábra a közzétételi folyamat fő lépéseit ábrázolja a "Go Live" kifejezésre:

![A virtuális gép ajánlatának közzétételi lépései](./media/publishvm_013.png)

A következő táblázat ismerteti ezeket a lépéseket, és a befejezésük maximális becsült idejét biztosítja:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Közzétételi lépés**           | **Time**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek ellenőrzése         | 15 perc   | Az ajánlatra vonatkozó információk és az ajánlat beállításainak ellenőrzése.                        |
| Tesztelési meghajtó ellenőrzése (nem kötelező) | 2 óra | Ha a tesztvezetés engedélyezését választotta, a Microsoft ellenőrzi a tesztelési meghajtó konfigurációját, a telepítést és a replikálást a kiválasztott régiókban. |
| Tanúsítvány                  | 3 nap | Az ajánlatot az Azure minősítési csapat elemzi. Ez a lépés a vírusok, a kártevők, a biztonsági megfelelőség és a biztonsági problémák vizsgálatát végzi. A rendszer visszajelzést küld, ha problémát talál. |
| Kiépítés                   | 4 nap   | A virtuálisgép-ajánlat a piactér éles rendszereiben van replikálva.               |
| Csomagolás és vezető generáció regisztrációja | \<1 óra  | Az ajánlat technikai eszközei az ügyfelek általi használatra vannak csomagolva, és a vezető rendszerek konfigurálva vannak és konfigurálhatók. |
|  Közzétevő PM             |  -        | A kiadó végleges felülvizsgálata és megerősítése az ajánlat életbe lépését megelőzően. Az ajánlatot a kiválasztott előfizetésekben is üzembe helyezheti (az ajánlati információk lépéseiben) annak ellenőrzéséhez, hogy az megfelel-e az összes követelménynek.  |
| Kiépítés                   | 4 nap | A véglegesített virtuálisgép-ajánlat a piactér éles rendszereiben és régióiban replikálódik. | 
| Élő                           | 4 nap | A VM-ajánlat megjelent, replikálva van a szükséges régiókban, és elérhetővé válik a nyilvánosság számára. |
|  |  |

A folyamat befejezéséhez legfeljebb 16 napig engedélyezze a műveletet.  A közzétételi lépések elvégzése után a virtuálisgép-ajánlat megjelenik a [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

