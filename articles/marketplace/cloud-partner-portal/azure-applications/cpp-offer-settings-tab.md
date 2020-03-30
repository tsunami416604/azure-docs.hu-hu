---
title: Az Azure alkalmazásajánlat beállításai | Azure Piactér
description: Konfigurálja az Azure-alkalmazásajánlat ajánlatbeállításait.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: d111471e909a091d8c887f13ad89d1da50cf72b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280116"
---
# <a name="azure-application-offer-settings-tab"></a>Az Azure alkalmazás ajánlatának beállításai lap

Ez a cikk ismerteti, hogyan konfigurálhatja az ajánlat beállításait egy Azure-alkalmazás.

Megnyílik **az Azure Applications > Új ajánlat** lap, amely az Ajánlat **beállításai** lapon jelenik meg. A mezőnévhez csatolt csillag (*) azt jelzi, hogy szükség van rá.

![Ajánlat azonosító űrlapja](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Identitás-beállítások ajánlata

Az **Ajánlat identitása**csoportban meg kell adnia az alábbi táblázatban leírt mezők adatait.  

|    Mező         |       Leírás                                                            |
|  ---------       |     ---------------                                                          |
| **Ajánlat azonosítója\***       | Az ajánlat egyedi azonosítója (közzétevői profilon belül). Ez az azonosító látható lesz a termék URL-címeiben és elemzési jelentésekben. Legfeljebb 50 karakter hosszú, és kisméretű alfanumerikus karaktereket és kötőjeleket (-) használhat. (Az azonosító nem végződhet kötőjellel.) **Megjegyzés:** Ez a mező nem módosítható, miután az ajánlat éles. <br> Ha például a Contoso közzétesz egy ajánlatot ajánlatazonosító-mintatárolóval, `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`hozzá van rendelve az Azure Marketplace **URL-címe.** |
| **Közzétevő azonosítója\***     | A szervezet egyedi azonosítója az Azure Marketplace-en. Minden ajánlatának a közzétevő azonosítójához kell kapcsolódnia. Ez az érték nem módosítható az ajánlat mentése után. |
| **név\***          | Az ajánlat megjelenítendő neve. Ez a név jelenik meg az Azure Marketplace-en és a Cloud Partner Portal. Legfeljebb 50 karakterből állhat. Javasoljuk, hogy felismerhető márkanevet használjon a termékéhez. Ne adja meg a szervezet nevét, kivéve, ha a termék forgalomba kerül. Ha ezt az ajánlatot más webhelyeken és kiadványokban forgalmazza, győződjön meg arról, hogy a név pontosan ugyanaz az összes kiadványban. |
|  |  |

Válassza a **Mentés** lehetőséget az ajánlatbeállítások mentéséhez.

## <a name="next-steps"></a>További lépések

Az [SK-k](./cpp-skus-tab.md) lapon konfigurálhatja az ajánlat sk-jeit.
