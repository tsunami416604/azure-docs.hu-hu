---
title: A Virtuálisgép-ajánlat beállításai lap az Azure Piactér felhőpartneri portálján
description: Ismerteti az Ajánlat beállításai lapot, amely et az Azure Marketplace virtuális gép ajánlatának létrehozásához használ.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: e5353797aaa7d40e1b6a95685b79cb3fe99f5ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277650"
---
# <a name="virtual-machine-offer-settings-tab"></a>Virtuális gép ajánlatbeállításai lap

A virtuális gépek **új ajánlat** lapja az **Ajánlati beállítások**nevű első lapon nyílik meg.  

![Új ajánlat lap virtuális gépekhez](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Ajánlatbeállításai mezők

Az **Ajánlat beállításai** lapon a következő mezőket kell megadnia.  A mezőnévhez csatolt csillag (*) azt jelzi, hogy szükség van rá. 

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Ajánlat azonosítója\***   | Az ajánlat egyedi azonosítója (közzétevői profilon belül). Ez az azonosító látható lesz a termék URL-címek, az Azure Resource Manager-sablonok és a számlázási jelentésekben. Legfeljebb 50 karakter hosszú, csak kisalfanumerikus karakterekből és kötőjelekből (-) állhat, de nem végződhet kötőjellel. Ez a mező nem módosítható, miután az ajánlat éles. <br> Ha például a Contoso közzétesz egy ajánlatot ajánlatazonosító-minta-vm-mel, hozzá van rendelve az Azure Marketplace **URL-címe.** `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` |
| **Publisher\***  | A szervezet egyedi azonosítója az Azure Marketplace-en. Minden ajánlatának a közzétevő azonosítójához kell kapcsolódnia. Ez az érték nem módosítható az ajánlat mentése után. |
| **név\***       | Az ajánlat megjelenítendő neve. Ez a név jelenik meg az Azure Marketplace-en és a Cloud Partner Portalon. Legfeljebb 50 karakterből állhat. Itt az útmutató az, hogy egy felismerhető márkanevet tartalmazzon a termékhez. Ne adja meg a szervezet nevét itt, kivéve, ha ez hogyan forgalmazta. Ha ezt az ajánlatot más webhelyeken és kiadványokban forgalmazza, győződjön meg arról, hogy a név pontosan ugyanaz az összes kiadványban. |
|   |   |
 
Kattintson a **Mentés gombra,** miután az összes mezőt megadta. 


## <a name="next-steps"></a>További lépések

A következő lapon [sk-eket](./cpp-skus-tab.md) ad hozzá az ajánlathoz.
