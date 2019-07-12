---
title: Felügyelt szolgáltatásokat kínál az Azure Marketplace-en
description: A felügyelt szolgáltatások ajánlatok engedélyezése resource management ajánlatokat az ügyfelek számára az Azure Marketplace-en, hogy a szolgáltatók.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a6fcf5f1d0ac194d60f834fb8d26db019c538410
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809875"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Felügyelt szolgáltatásokat kínál az Azure Marketplace-en

Ez a cikk ismerteti az új **felügyelt szolgáltatásokat** ajánlattípus a [Azure Marketplace-en](https://azuremarketplace.microsoft.com). A felügyelt szolgáltatásokat kínál, hogy az ügyfelek számára az Azure delegált felhőerőforrás-kezelés resource management-szolgáltatások engedélyezése. Elérhetővé teheti az ajánlatokat az összes potenciális ügyfelek számára, vagy csak egy vagy több megadott ügyfelek számára. A Díjszámítás ügyfelek közvetlenül a felügyelt szolgáltatások kapcsolatos költségek, mivel nincsenek a Microsoft által felszámított díj nincs.

## <a name="understand-managed-services-offers"></a>Felügyelt szolgáltatások ajánlatok ismertetése

Felügyelt szolgáltatásokat kínál zökkenőmentessé bevezetése terén az Azure resource management delegált. Ha a vásárló megvásárolhatja az Azure Marketplace-ajánlat, ők is látni fogják a megadhatja, mely az előfizetések és/vagy erőforráscsoportok előkészítést úgy, hogy a szervezet felügyeleti feladatokat végezhetnek belül az ügyfél megadott a szervezet bérlőjében.

Ezt követően nem szükséges az ügyfél vagy a szolgáltató által az ügyfél előkészítés. Ennek az az oka, ha meghatározhatja az ajánlatot a [Cloud Partner Portalon](https://cloudpartner.azure.com/), hoz létre egy jegyzéket, amely meghatározza az Azure AD-felhasználók, csoportok, és a szolgáltatás elveket, amelyek az Azure-ügyfél erőforrásokhoz való hozzáférés delegált erőforráshoz felügyeleti. szerepkörök együtt, hogy azok hozzáférési szintjét határozza meg. Engedélyek hozzárendelése egy egyéni felhasználó vagy alkalmazás fiókok sorozatát helyett az Azure AD-csoportok hozzáadása, vagy távolítsa el az egyéni felhasználók számára, ha a hozzáférési követelmények változnak.

## <a name="public-and-private-offers"></a>Nyilvános és privát ajánlatok

Egyes felügyelt szolgáltatások ajánlatok egy vagy több csomag magában foglalja. Az alábbi díjcsomagok lehet nyilvános vagy privát.

Ha szeretné korlátozni az ajánlat az adott ügyfélnek, egy privát terv teheti közzé. Ha így tesz, a terv csak vásárolható meg az adott] előfizetés azonosítókat egészíti ki. További információ: [szóló ajánlatok](https://docs.microsoft.com/azure/marketplace/private-offers).

Nyilvános csomagok lehetővé teszik az új ügyfelek számára a szolgáltatások támogatása. Ezek a általában jobban megfelelő, ha csak az ügyfél bérlői korlátozott hozzáférést. Miután létrehozott egy kapcsolatot egy ügyféllel Ha úgy döntenek, hogy a szervezet további hozzáférést, akkor vagy csak az adott ügyfélhez tartozó, illetve a egy új privát csomag közzétételével [bevezetési működnek tovább az Azure-hozzáférés Resource Manager-sablonok](../how-to/onboard-customer.md).

Ne feledje, hogy egy csomag közzététele, nyilvános, után nem módosítható a. Ezenkívül nem lehet korlátozni egy nyilvános csomag rendelkezésre állási egyes ügyfelek számára, vagy akár egy bizonyos számú ügyfelek, bár állítsa le a csomag teljes értékesítés, ha úgy dönt, hogy ehhez.

Ha szükséges, megadhatja a nyilvános és privát csomagok ugyanazt az ajánlatban.

## <a name="publish-managed-service-offers"></a>Felügyeltszolgáltatás-ajánlatok közzététele

A felügyelt szolgáltatások ajánlat közzététele kapcsolatban lásd: [egy felügyelt szolgáltatásokat ajánlat közzététele az Azure piactéren](../how-to/publish-managed-services-offers.md). Használatával a Cloud Partner portálra Azure piactér közzétételre vonatkozó általános információ: [Azure Marketplace és AppSource közzétételi útmutató](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) és [kezelése az Azure és az AppSource piactérről ajánlatokat](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

## <a name="next-steps"></a>További lépések

- Ismerje meg [Azure erőforrás-kezelés delegált](azure-delegated-resource-management.md) és [több-bérlős felhasználói élmény](cross-tenant-management-experience.md).
- [Felügyelt szolgáltatások ajánlatok közzététele](../how-to/publish-managed-services-offers.md) az Azure Marketplace-en.
