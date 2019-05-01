---
title: Virtuálisgép-ajánlat beállítások lapján a Cloud Partner portálra, az Azure Marketplace-en
description: Ismerteti az Azure piactér-beli Virtuálisgép-ajánlat létrehozásakor használt ajánlat beállítások lapon.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: d361b6b8b08f9556cd57215ebdf82c1bf69d372d
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938154"
---
# <a name="virtual-machine-offer-settings-tab"></a>Virtuálisgép-ajánlat beállítások lap

A **új ajánlat** nevű az első lapon megnyílik a virtuális gépek **ajánlat beállítások**.  

![A virtuális gépek új ajánlat lap](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Az ajánlat beállításainak mezők

Az a **ajánlat beállítások** lapon meg kell adnia a következő mezőket.  A hozzáfűzött csillag (*) az a mező nevét jelzi, hogy szükséges. 

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Ajánlat azonosítója\***   | Az ajánlat egyedi azonosítóját (a közzétevő profil) belül. Ez az azonosító termék URL-címeket, az Azure Resource Manager-sablonok, látható lesz, és számlázási jelentések. Azt a maximális hossza legfeljebb 50 karakter hosszúságú, csak kisbetűs alfanumerikus karaktereket és kötőjeleket (-) áll lehet, de nem végződhet kötőjellel. Ez a mező nem módosítható, miután ajánlat élesíti. <br> Például, ha a Contoso tesz közzé egy ajánlatot az ajánlat azonosítója **minta-vm**, az Azure Marketplace-en URL-cím van hozzárendelve `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` . |
| **Publisher\***  | A szervezet egyedi azonosítója az Azure piactéren. Az ajánlatokat társítható a közzétevő-azonosítót. Ez az érték nem lehet módosítani, miután a rendszer menti az ajánlatot. |
| **Név\***       | Az ajánlat megjelenített neve. Az Azure piactéren, és a Cloud Partner portálra fog megjelenni. Legfeljebb 50 karakterből állhat. Itt útmutatást, hogy a termék felismerhető márkanév tartalmazza. Itt a szervezet neve ne tartalmazza, hacsak nem értékesített hogyan. Ha ez az ajánlat egyéb webhelyeken és kiadványok vannak marketing, győződjön meg arról, hogy a név pontosan azonos között az összes olyan. |
|   |   |
 
Kattintson a **mentése** Miután megadta az összes mezőt. 


## <a name="next-steps"></a>További lépések

A következő lapon hozzáadhat [termékváltozatok](./cpp-skus-tab.md) az ajánlatra.
