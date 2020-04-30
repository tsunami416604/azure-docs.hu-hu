---
title: Virtuálisgép-ajánlat beállításai lap az Azure Marketplace Cloud Partner Portal
description: Az Azure Marketplace virtuálisgép-ajánlat létrehozásához használt ajánlati beállítások lapot ismerteti.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: abeadf5f3c0a19212a12256e06602e840c9d1fa9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146832"
---
# <a name="virtual-machine-offer-settings-tab"></a>Virtuálisgép-ajánlat beállításai lap

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuálisgép-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Kövesse az Azure-beli [virtuális gép létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) az áttelepített ajánlatok kezeléséhez című témakör utasításait.

Megnyílik a virtuális gépek **új ajánlat** lapja az **ajánlati beállítások**nevű első lapon.  

![Új ajánlat oldal a virtuális gépekhez](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Ajánlat beállításai mezők

Az **ajánlat beállításai** lapon a következő mezőket kell megadnia.  A mező neve mezőben a hozzáfűzött csillag (*) érték azt jelzi, hogy szükség van rá. 

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Ajánlat azonosítója\***   | Az ajánlathoz tartozó egyedi azonosító (közzétevői profilon belül). Ez az azonosító a termék URL-címeiben, Azure Resource Manager sablonokban és számlázási jelentésekben jelenik meg. Legfeljebb 50 karakter hosszúságú lehet, csak kisbetűkből és kötőjelből állhat (-), de kötőjel nem végződhet. Ez a mező nem módosítható, miután egy ajánlat élőben üzemel. <br> Ha például a contoso egy ajánlat **-azonosító minta-VM-** ajánlatot tesz közzé, az Azure Marketplace URL-címét `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` rendeli hozzá. |
| **Publisher\***  | A szervezet egyedi azonosítója az Azure piactéren. Az összes ajánlathoz társítva kell lennie a közzétevő AZONOSÍTÓjának. Ez az érték az ajánlat mentésekor nem módosítható. |
| **Name (Név)\***       | Az ajánlat megjelenítendő neve. Ez a név jelenik meg az Azure Marketplace-en és a Cloud Partner Portal. Legfeljebb 50 karakterből állhat. Itt találja a termék felismerhető márkájának nevét. Ne adja meg a szervezet nevét, kivéve, ha ez a piaca. Ha más webhelyeken és kiadványokban is ezt az ajánlatot látja, ügyeljen arra, hogy a név pontosan ugyanaz legyen az összes kiadványban. |
|   |   |
 
Kattintson a **Mentés** gombra, miután megadta az összes mezőt. 


## <a name="next-steps"></a>További lépések

A következő lapon adja hozzá a [SKU](./cpp-skus-tab.md) -ket az ajánlathoz.
