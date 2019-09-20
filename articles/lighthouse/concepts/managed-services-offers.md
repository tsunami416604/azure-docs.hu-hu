---
title: Felügyelt szolgáltatási ajánlatok az Azure Marketplace-en
description: A felügyelt szolgáltatások lehetővé teszik a szolgáltatók számára, hogy erőforrás-kezelési ajánlatokat adjanak az ügyfeleknek az Azure piactéren.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 09/19/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 0a6371a1020182650d83d5e7ec9bb2378b44b557
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155258"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Felügyelt szolgáltatási ajánlatok az Azure Marketplace-en

Ez a cikk az új **felügyelt szolgáltatások** ajánlat típusát mutatja be az [Azure Marketplace](https://azuremarketplace.microsoft.com)-en. A felügyelt szolgáltatások lehetővé teszik, hogy erőforrás-kezelési szolgáltatásokat nyújtson az ügyfeleknek az Azure-beli delegált erőforrás-kezeléssel. Ezeket az ajánlatokat az összes potenciális ügyfél számára elérhetővé teheti, vagy csak egy vagy több konkrét ügyfél számára. Mivel az ügyfeleket közvetlenül az ezekkel a felügyelt szolgáltatásokkal kapcsolatos költségekre számlázza, a Microsoft nem számít fel díjat.

## <a name="understand-managed-services-offers"></a>A felügyelt szolgáltatások ajánlatának ismertetése

A felügyelt szolgáltatások egyszerűbbé teszik az Azure-beli delegált erőforrás-kezelésre vonatkozó ügyfelek bevezetésének folyamatát. Amikor egy ügyfél ajánlatot vásárol az Azure Marketplace-en, meg tudják határozni, hogy mely előfizetéseket és/vagy erőforráscsoportokat kell előkészíteni. Vegye figyelembe, hogy az előfizetést először a **Microsoft. ManagedServices** erőforrás-szolgáltató manuális regisztrálásával kell engedélyezni a bevezetéshez.

Ezt követően a szervezetében lévő felhasználók az ajánlatnak a [Cloud Partner Portalban](https://cloudpartner.azure.com/)való létrehozásakor meghatározott hozzáférés alapján elvégezhetik az adott erőforrásokhoz tartozó adminisztrációs feladatokat. Ez egy olyan jegyzékfájlon keresztül történik, amely megadja az Azure AD-felhasználók,-csoportok és-szolgáltatások számára, amelyek az Azure-beli delegált erőforrás-kezelés használatával férhetnek hozzá az ügyfelek erőforrásaihoz, valamint a hozzáférési szintet meghatározó szerepkörökkel. Ha az engedélyeket egy Azure AD-csoporthoz rendeli hozzá, nem pedig egyéni felhasználói vagy alkalmazási fiókokat, hozzáadhat vagy eltávolíthat egyes felhasználókat a hozzáférési követelmények változásakor.

## <a name="public-and-private-offers"></a>Nyilvános és privát ajánlatok

A felügyelt szolgáltatások minden ajánlata egy vagy több csomagot tartalmaz. A csomagok lehetnek magán-vagy nyilvánosak.

Ha korlátozni szeretné az ajánlatát bizonyos ügyfeleknek, közzétehet egy privát csomagot. Ha így tesz, a terv csak az Ön által megadott előfizetés-azonosítók esetében vásárolható meg. További információ: [privát ajánlatok](https://docs.microsoft.com/azure/marketplace/private-offers).

A nyilvános csomagok lehetővé teszik a szolgáltatások új ügyfelekhez való előléptetését. Ezek általában megfelelőbbek, ha csak korlátozott hozzáférésre van szüksége az ügyfél bérlője számára. Ha kapcsolatot létesített egy ügyféllel, ha úgy dönt, hogy további hozzáférést biztosítanak a szervezetnek, akkor ezt akár egy új privát csomag közzétételével is megteheti, akár az [Azure-erőforrás használatával való további hozzáférés érdekében. Manager-sablonok](../how-to/onboard-customer.md).

Ne feledje, hogy a csomag nyilvánosként való közzététele után nem módosítható magánjellegűre. Emellett nem korlátozhatja a nyilvános csomag elérhetőségét bizonyos ügyfelek számára, vagy akár bizonyos számú ügyfelet is, de a csomag teljes értékesítését megszüntetheti, ha ezt választja.

Ha szükséges, a nyilvános és a saját csomagok is felhasználhatók ugyanabban az ajánlatban.

## <a name="publish-managed-service-offers"></a>Felügyelt szolgáltatás-ajánlatok közzététele

A felügyelt szolgáltatások ajánlatának közzétételével kapcsolatos további információkért lásd: [felügyelt szolgáltatások ajánlatának közzététele az Azure Marketplace](../how-to/publish-managed-services-offers.md)-en. Az Azure Marketplace-en a Cloud Partner Portal használatával történő közzétételsel kapcsolatos általános információkért lásd: az [Azure Marketplace és a AppSource közzétételi útmutatója](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) , valamint az [Azure és a AppSource Marketplace-ajánlatok kezelése](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

## <a name="next-steps"></a>További lépések

- Ismerje meg az Azure-beli [delegált erőforrás-kezelést](azure-delegated-resource-management.md) és a [több-bérlős felügyeleti tapasztalatokat](cross-tenant-management-experience.md).
- [Felügyelt szolgáltatások ajánlatának közzététele](../how-to/publish-managed-services-offers.md) az Azure Marketplace-en.
