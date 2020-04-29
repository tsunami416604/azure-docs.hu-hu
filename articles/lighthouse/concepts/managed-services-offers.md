---
title: Felügyelt szolgáltatások ajánlatai az Azure Marketplace-en
description: A felügyelt szolgáltatás lehetővé teszi, hogy a szolgáltatók erőforrás-kezelési ajánlatokat értékesítsenek az ügyfeleknek az Azure piactéren.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 13e1825ae6eb50b1b376e3bd3de908a545fbe023
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144897"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Felügyelt szolgáltatások ajánlatai az Azure Marketplace-en

Ez a cikk a **felügyelt szolgáltatások** ajánlatának típusát mutatja be az [Azure piactéren](https://azuremarketplace.microsoft.com). A felügyelt szolgáltatás lehetővé teszi, hogy erőforrás-kezelési szolgáltatásokat nyújtson az ügyfeleknek az Azure-beli [delegált erőforrás-kezelés](azure-delegated-resource-management.md)használatával. Ezeket az ajánlatokat az összes potenciális ügyfél számára elérhetővé teheti, vagy csak egy vagy több konkrét ügyfél számára. Mivel az ügyfeleket közvetlenül az ezekkel a felügyelt szolgáltatásokkal kapcsolatos költségekre számlázza, a Microsoft nem számít fel díjat.

## <a name="understand-managed-service-offers"></a>A felügyelt szolgáltatások ajánlatának ismertetése

A felügyelt szolgáltatás megkönnyíti az ügyfelek Azure-beli delegált erőforrás-kezeléshez való bevezetésének folyamatát. Amikor egy ügyfél ajánlatot vásárol az Azure Marketplace-en, meg tudják határozni, hogy mely előfizetéseket és/vagy erőforráscsoportokat kell előkészíteni.

Ezt követően a szervezetében lévő felhasználók az ajánlat létrehozásakor meghatározott hozzáférésnek megfelelően képesek lesznek dolgozni a szervezete bérlőn belüli erőforrásain. Ez egy olyan jegyzékfájlon keresztül történik, amely megadja az Azure AD-felhasználók,-csoportok és-szolgáltatások számára, amelyek az Azure-beli delegált erőforrás-kezelés használatával férhetnek hozzá az ügyfelek erőforrásaihoz, valamint a hozzáférési szintet meghatározó szerepkörökkel. Ha az engedélyeket egy Azure AD-csoporthoz rendeli hozzá, nem pedig egyéni felhasználói vagy alkalmazási fiókokat, hozzáadhat vagy eltávolíthat egyes felhasználókat a hozzáférési követelmények változásakor.

## <a name="public-and-private-offers"></a>Nyilvános és privát ajánlatok

A felügyelt szolgáltatások minden ajánlata egy vagy több csomagot tartalmaz. A csomagok lehetnek magán-vagy nyilvánosak is.

Ha korlátozni szeretné az ajánlatát bizonyos ügyfeleknek, közzétehet egy privát csomagot. Ha így tesz, a terv csak az Ön által megadott előfizetés-azonosítók esetében vásárolható meg. További információ: [privát ajánlatok](../../marketplace/private-offers.md).

A nyilvános csomagok lehetővé teszik a szolgáltatások új ügyfelekhez való előléptetését. Ezek általában megfelelőbbek, ha csak korlátozott hozzáférésre van szüksége az ügyfél bérlője számára. Ha kapcsolatot létesített egy ügyféllel, ha úgy dönt, hogy további hozzáférést biztosítanak a szervezetnek, akkor ezt akár egy új privát csomag közzétételével is megteheti, akár [Azure Resource Manager-sablonok használatával történő további hozzáféréshez](../how-to/onboard-customer.md).

Ha szükséges, a nyilvános és a saját csomagok is felhasználhatók ugyanabban az ajánlatban.

> [!IMPORTANT]
> Miután közzétett egy csomagot nyilvánosként, nem módosíthatja magánjellegűre. Egy privát csomag használatával szabályozhatja, hogy mely ügyfelek fogadhatják el az ajánlatot, és hogyan delegálhat erőforrásokat. Egy nyilvános csomaggal nem korlátozhatja a rendelkezésre állást bizonyos ügyfelek számára, vagy akár bizonyos számú ügyfél számára is (bár ha úgy dönt, hogy teljesen megszüntetheti a csomag értékesítését). A [delegáláshoz való hozzáférést csak akkor távolíthatja el,](../how-to/remove-delegation.md) ha az ügyfél csak akkor fogadja el az **ajánlatot, ha** az ajánlat közzétételekor a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörét](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) beállította a **szerepkör-definícióban** . Az ügyfelet is elérheti, és megkérheti, hogy [távolítsa el a hozzáférését](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Felügyelt szolgáltatás-ajánlatok közzététele

A felügyelt szolgáltatások ajánlatának közzétételével kapcsolatos további információkért lásd: [felügyelt szolgáltatások ajánlatának közzététele az Azure Marketplace](../how-to/publish-managed-services-offers.md)-en.

## <a name="next-steps"></a>További lépések

- Ismerje meg az Azure-beli [delegált erőforrás-kezelést](azure-delegated-resource-management.md) és a [több-bérlős felügyeleti tapasztalatokat](cross-tenant-management-experience.md).
- [Felügyelt szolgáltatások ajánlatának közzététele](../how-to/publish-managed-services-offers.md) az Azure Marketplace-en.
