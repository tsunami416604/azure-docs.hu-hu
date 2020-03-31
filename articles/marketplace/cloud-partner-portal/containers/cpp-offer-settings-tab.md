---
title: Ajánlati beállítások egy Azure Containers-lemezképhez | Azure Piactér
description: Konfigurálja az Azure-tároló ajánlatbeállításait.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: e7e56f3562965a0a49082024c905d90ed989ccdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280031"
---
# <a name="container-offer-settings-tab"></a>A Tárolóajánlat beállításai lap

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
