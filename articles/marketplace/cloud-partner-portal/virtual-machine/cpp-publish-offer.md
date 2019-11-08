---
title: Virtuálisgép-ajánlat közzététele az Azure Marketplace-en
description: Felsorolja azokat a lépéseket, amelyek szükségesek egy meglévő virtuálisgép-ajánlat közzétételéhez az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/17/2018
ms.author: pabutler
ms.openlocfilehash: 1b07f3f3edab47f8f75835dffd4cc3f89f17ab63
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824406"
---
# <a name="publish-a-virtual-machine-offer"></a>Virtuálisgép-ajánlat közzététele

 Az utolsó lépés, miután meghatározta az ajánlatot a portálon, és létrehozta a kapcsolódó technikai eszközöket, a közzétételhez küldje el az ajánlatot. A következő ábra a közzétételi folyamat fő lépéseit ábrázolja a "Go Live" kifejezésre:

![A virtuális gép ajánlatának közzétételi lépései](./media/publishvm_013.png)

A következő táblázat ismerteti ezeket a lépéseket, és a befejezésük maximális becsült idejét biztosítja:
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Közzétételi lépés**           | **Idő**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek ellenőrzése         | 15 perc   | Az ajánlatra vonatkozó információk és az ajánlat beállításainak ellenőrzése.                        |
| Tesztelési meghajtó ellenőrzése (nem kötelező) | 2 óra | Ha a tesztvezetés engedélyezését választotta, a Microsoft ellenőrzi a tesztelési meghajtó konfigurációját, a telepítést és a replikálást a kiválasztott régiókban. |
| Tanúsítvány                  | 3 nap | Az ajánlatot az Azure minősítési csapat elemzi. Ez a lépés a vírusok, a kártevők, a biztonsági megfelelőség és a biztonsági problémák vizsgálatát végzi. A rendszer visszajelzést küld, ha problémát talál. |
| Kiépítés                   | 4 nap   | A virtuálisgép-ajánlat a piactér éles rendszereiben van replikálva.               |
| Csomagolás és vezető generáció regisztrációja | \< 1 óra  | Az ajánlat technikai eszközei az ügyfelek általi használatra vannak csomagolva, és a vezető rendszerek konfigurálva vannak és konfigurálhatók. |
|  Közzétevő PM             |  -        | A kiadó végleges felülvizsgálata és megerősítése az ajánlat életbe lépését megelőzően. Az ajánlatot a kiválasztott előfizetésekben is üzembe helyezheti (az ajánlati információk lépéseiben) annak ellenőrzéséhez, hogy az megfelel-e az összes követelménynek.  |
| Kiépítés                   | 4 nap | A véglegesített virtuálisgép-ajánlat a piactér éles rendszereiben és régióiban replikálódik. | 
| Élő                           | 4 nap | A VM-ajánlat megjelent, replikálva van a szükséges régiókban, és elérhetővé válik a nyilvánosság számára. |
|  |  |

A folyamat befejezéséhez legfeljebb 16 napig engedélyezze a műveletet.  A közzétételi lépések elvégzése után a virtuálisgép-ajánlat megjelenik a [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). 

