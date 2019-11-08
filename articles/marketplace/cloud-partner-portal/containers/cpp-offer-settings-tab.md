---
title: Az Azure containers-képek ajánlati beállításai | Azure piactér
description: Adja meg az ajánlat beállításait egy Azure-tárolóhoz.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 8742f4eb6ce8f3e1b7c4b0449c2f19d8a254e5dc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823185"
---
# <a name="container-offer-settings-tab"></a>Tároló-ajánlat beállításai lap

Megnyílik a **tárolók > új ajánlat** lap, amely a fókuszt az **ajánlat beállításai** lapon jelenik meg. 

![Ajánlat identitása](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>Az ajánlat identitásának beállításai

Az **ajánlat identitása**területen meg kell adnia az alábbi táblázatban ismertetett mezőkre vonatkozó információkat. A mezőhöz hozzáfűzni kívánt csillag (*) azt jelzi, hogy a név megadása kötelező. 

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Ajánlat azonosítója\***       | Az ajánlathoz tartozó egyedi azonosító (közzétevői profilon belül). Ez az azonosító a termék URL-címeiben és az információkat tartalmazó jelentésekben jelenik meg. Legfeljebb 50 karakterből állhat, és használhat kisbetűket alfanumerikus karaktereket és kötőjeleket (-). (Az azonosító nem végződhet kötőjeltel.) **Megjegyzés:** Ez a mező nem módosítható, miután egy ajánlat élőben üzemel. <br> Ha például a contoso ajánlat-azonosító **minta-tárolóval**tesz közzé ajánlatot, az Azure Marketplace URL-címét `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`rendeli hozzá. |
| **Közzétevő azonosítója\***     | A szervezet egyedi azonosítója az Azure piactéren. Az összes ajánlathoz társítva kell lennie a közzétevő AZONOSÍTÓjának. Ez az érték nem módosítható az ajánlat mentése után. |
| **Név\***          | Az ajánlat megjelenítendő neve. Ez a név jelenik meg az Azure Marketplace-en és a Cloud Partner Portal. Legfeljebb 50 karakterből állhat. Javasoljuk, hogy a termék felismerhető márkáját használja. Ne adja meg a szervezet nevét, kivéve, ha ez a termék forgalmazása. Ha más webhelyeken és kiadványokban is ezt az ajánlatot látja, ügyeljen arra, hogy a név pontosan ugyanaz legyen az összes kiadványban. |
|  |  |

Válassza a **Mentés** lehetőséget az ajánlat beállításainak mentéséhez.


## <a name="next-steps"></a>További lépések

Az [SKUs](./cpp-skus-tab.md) lapon konfigurálhatja az ajánlathoz tartozó SKU-ket.
