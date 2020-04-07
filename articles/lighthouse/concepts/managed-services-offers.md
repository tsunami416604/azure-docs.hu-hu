---
title: Felügyelt szolgáltatási ajánlatok az Azure Piactéren
description: A felügyelt szolgáltatásajánlatok lehetővé teszik a szolgáltatók számára, hogy erőforrás-kezelési ajánlatokat értékesítsék az Azure Marketplace-en lévő ügyfelek számára.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a0670bd74971132dcf243736bdf882a00154a942
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672423"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Felügyelt szolgáltatási ajánlatok az Azure Piactéren

Ez a cikk a **felügyelt szolgáltatás** ajánlattípusát ismerteti az [Azure Marketplace-en.](https://azuremarketplace.microsoft.com) A felügyelt szolgáltatásajánlatok lehetővé teszik, hogy erőforrás-kezelési szolgáltatásokat kínáljon az ügyfeleknek az [Azure delegált erőforrás-kezelése](azure-delegated-resource-management.md)segítségével. Ezeket az ajánlatokat elérhetővé teheti az összes potenciális ügyfél számára, vagy csak egy vagy több konkrét ügyfél számára. Mivel az ügyfeleknek közvetlenül kell fizetnie a felügyelt szolgáltatásokkal kapcsolatos költségekért, a Microsoft nem számít fel díjat.

## <a name="understand-managed-service-offers"></a>A felügyelt szolgáltatási ajánlatok ismertetése

A felügyelt szolgáltatás egyszerűsíti az Azure delegált erőforrás-kezeléséhez való bevezetési ügyfelek folyamatát. Amikor egy ügyfél megvásárol egy ajánlatot az Azure Marketplace-en, megadhatja, hogy mely előfizetéseket és/vagy erőforráscsoportokat kell berakni.

Ezt követően a szervezet felhasználói a szervezet bérlőjéről dolgozhatnak ezeken az erőforrásokon, az ajánlat [felhőpartneri portálon](https://cloudpartner.azure.com/)történő létrehozásakor megadott hozzáférésnek megfelelően. Ez egy olyan jegyzékfájlon keresztül történik, amely meghatározza az Azure AD-felhasználókat, -csoportokat és egyszerű szolgáltatáscsoportokat, amelyek hozzáférhetnek az ügyfél-erőforrásokhoz az Azure delegált erőforrás-kezelése, valamint a hozzáférési szintjüket meghatározó szerepkörök használatával. Ha engedélyeket rendel egy Azure AD-csoporthoz, nem pedig egyéni felhasználói vagy alkalmazásfiókok sorozatához, hozzáadhat vagy eltávolíthat egyéni felhasználókat, amikor a hozzáférési követelmények megváltoznak.

## <a name="public-and-private-offers"></a>Nyilvános és privát ajánlatok

Minden felügyelt szolgáltatás ajánlat egy vagy több csomag. A tervek lehetnek privátak vagy nyilvánosak.

Ha az ajánlatot meghatározott ügyfelekre szeretné korlátozni, közzétehet egy privát csomagot. Ha így tesz, a csomag csak az Ön által megadott adott] előfizetési azonosítókhoz vásárolható meg. További információ: [Privát ajánlatok](../../marketplace/private-offers.md).

A nyilvános tervek lehetővé teszik, hogy szolgáltatásait új ügyfelek számára népszerűsítse. Ezek általában megfelelőbbek, ha csak korlátozott hozzáférésre van szüksége az ügyfél bérlőjéhez. Miután kapcsolatot létesített egy ügyféllel, ha úgy döntenek, hogy további hozzáférést biztosítanak a szervezetnek, ezt megteheti, ha csak az adott ügyfél számára tesz közzé egy új privát csomagot, vagy [az Azure Resource Manager-sablonok használatával további hozzáférést biztosít számukra.](../how-to/onboard-customer.md)

Ha szükséges, nyilvános és privát csomagokat is felvehet ugyanabba az ajánlatba.

> [!IMPORTANT]
> Ha egy tervet nyilvánosként tettek közzé, nem módosíthatja magánjellegűvé. Annak szabályozásához, hogy mely ügyfelek fogadhatják el az ajánlatot, és milyen erőforrásokat delegálhatnak, használjon privát csomagot. Nyilvános csomag esetén nem korlátozhatja a rendelkezésre állást bizonyos ügyfelekre, vagy akár bizonyos számú ügyfélre (bár teljesen leállíthatja a csomag értékesítését, ha úgy dönt). A [delegáláshoz való hozzáférést](../how-to/onboard-customer.md#remove-access-to-a-delegation) csak akkor távolíthatja el, ha az ügyfél csak akkor fogad el egy ajánlatot, ha az ajánlat közzétételekor a [Felügyelt szolgáltatások regisztrációs hozzárendelésének törlése szerepkör-törlési](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) **szerepkör-kóddal** rendelkező **engedélyezést** is tartalmaz. Azt is elérheti az ügyfél, és kérje meg őket, hogy [távolítsa el a hozzáférést.](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)

## <a name="publish-managed-service-offers"></a>Felügyelt szolgáltatási ajánlatok közzététele

A felügyelt szolgáltatások ajánlatának közzétételéről a [Felügyelt szolgáltatások ajánlatának közzététele az Azure Marketplace-en (Felügyelt szolgáltatások) szolgáltatás közzététele.Learn](../how-to/publish-managed-services-offers.md)how to publish a managed services offer, See Publish a Managed Services offer to Azure Marketplace . Az Azure Marketplace-en a Cloud Partner Portal használatával történő közzétételről az Azure Marketplace és az [AppSource közzétételi útmutatócímű témakörben talál általános](../../marketplace/marketplace-publishers-guide.md)információkat.

## <a name="next-steps"></a>További lépések

- Ismerje meg az [Azure delegált erőforrás-kezelési](azure-delegated-resource-management.md) és [több bérlőközötti felügyeleti tapasztalatait.](cross-tenant-management-experience.md)
- [Felügyelt szolgáltatások ajánlatai közzététele](../how-to/publish-managed-services-offers.md) az Azure Marketplace-en.
