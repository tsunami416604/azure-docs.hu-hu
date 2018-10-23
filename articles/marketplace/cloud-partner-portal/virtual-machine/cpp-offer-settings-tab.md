---
title: Virtuálisgép-ajánlat beállítások lapján a Cloud Partner portálra, az Azure Marketplace-en |} A Microsoft Docs
description: Ismerteti az Azure piactér-beli Virtuálisgép-ajánlat létrehozásakor használt ajánlat beállítások lapon.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 43c830bea57a4c6f3b6c56552dbcacaccc75d54e
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639753"
---
# <a name="virtual-machine-offer-settings-tab"></a>Virtuálisgép-ajánlat beállítások lap

A **új ajánlat** nevű az első lapon megnyílik a virtuális gépek **ajánlat beállítások**.  A hozzáfűzött csillag (*) az a mező nevét jelzi, hogy szükséges. 

![A virtuális gépek új ajánlat lap](./media/publishvm_004.png)

Az a **ajánlat beállítások** lapon meg kell adnia a következő kötelező mezőket.

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Ajánlat azonosítója**       | Az ajánlat egyedi azonosítóját (a közzétevő profil) belül. Ez az azonosító termék URL-címeket, az Azure Resource Manager-sablonok, látható lesz, és számlázási jelentések. Azt a maximális hossza legfeljebb 50 karakter hosszúságú, csak kisbetűs alfanumerikus karaktereket és kötőjeleket (-) áll lehet, de nem végződhet kötőjellel. Ez a mező nem módosítható, miután ajánlat élesíti. <br> Például, ha a Contoso tesz közzé egy ajánlatot az ajánlat azonosítója **minta-vm**, az Azure Marketplace-en URL-cím van hozzárendelve `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` . |
| **Közzétevő**     | A szervezet egyedi azonosítója az Azure piactéren. Az ajánlatokat társítható a közzétevő-azonosítót. Ez az érték nem lehet módosítani, miután a rendszer menti az ajánlatot. |
| **Name (Név)**          | Az ajánlat megjelenített neve. Az Azure piactéren, és a Cloud Partner portálra fog megjelenni. Legfeljebb 50 karakterből állhat. Itt útmutatást, hogy a termék felismerhető márkanév tartalmazza. Itt a szervezet neve ne tartalmazza, hacsak nem értékesített hogyan. Ha ez az ajánlat egyéb webhelyeken és kiadványok vannak marketing, győződjön meg arról, hogy a név pontosan azonos között az összes olyan. |
|  |  |
 
Kattintson a **mentése** a folyamat mentéséhez. A következő lapon hozzáadhat [termékváltozatok](./cpp-skus-tab.md) az ajánlatra.
