---
title: Ajánlati beállítások egy Azure Containers-lemezképhez | Azure Piactér
description: Konfigurálja az Azure-tároló ajánlatbeállításait.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: dfdc588ffe3ca8c0f21e4111b6f4c66de162299d
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270179"
---
# <a name="container-offer-settings-tab"></a>A Tárolóajánlat beállításai lap

> [!IMPORTANT]
> 2020. április 13-tól megkezdjük az Azure Container-ajánlatok kezelését a Partnerközpontba. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [Azure Container-ajánlat létrehozása](https://aka.ms/CreateContainerOffer) című, az áttelepített ajánlatok kezeléséhez című útmutatóutasításait.

Megnyílik **a Tárolók > új ajánlat** lap, amelyaz Ajánlat **beállításai** lapon jelenik meg. 

![Ajánlat identitás](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>Identitás-beállítások ajánlata

Az **Ajánlat identitása**csoportban meg kell adnia az alábbi táblázatban leírt mezők adatait. A mezőnévhez csatolt csillag (*) azt jelzi, hogy szükség van rá. 

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Ajánlat azonosítója\***       | Az ajánlat egyedi azonosítója (közzétevői profilon belül). Ez az azonosító látható lesz a termék URL-címeiben és elemzési jelentésekben. Legfeljebb 50 karakter hosszú, és kisméretű alfanumerikus karaktereket és kötőjeleket (-) használhat. (Az azonosító nem végződhet kötőjellel.) **Megjegyzés:** Ez a mező nem módosítható, miután az ajánlat éles. <br> Ha például a Contoso közzétesz egy ajánlatot ajánlatazonosító-mintatárolóval, `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`hozzá van rendelve az Azure Marketplace **URL-címe.** |
| **Közzétevő azonosítója\***     | A szervezet egyedi azonosítója az Azure Marketplace-en. Minden ajánlatának a közzétevő azonosítójához kell kapcsolódnia. Ez az érték nem módosítható az ajánlat mentése után. |
| **név\***          | Az ajánlat megjelenítendő neve. Ez a név jelenik meg az Azure Marketplace-en és a Cloud Partner Portal. Legfeljebb 50 karakterből állhat. Javasoljuk, hogy felismerhető márkanevet használjon a termékéhez. Ne adja meg a szervezet nevét, kivéve, ha a termék forgalomba kerül. Ha ezt az ajánlatot más webhelyeken és kiadványokban forgalmazza, győződjön meg arról, hogy a név pontosan ugyanaz az összes kiadványban. |
|  |  |

Válassza a **Mentés** lehetőséget az ajánlatbeállítások mentéséhez.


## <a name="next-steps"></a>További lépések

Az [SK-k](./cpp-skus-tab.md) lapon konfigurálhatja az ajánlat sk-jeit.
