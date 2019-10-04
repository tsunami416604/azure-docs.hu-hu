---
title: Az Azure resource management - delegált Azure világítótoronyig
description: A felügyelt szolgáltatások ajánlatok engedélyezése resource management ajánlatokat az ügyfelek számára az Azure Marketplace-en, hogy a szolgáltatók.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: cec6453cdf339e82420a1b12af6c8e60526fdc03
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809966"
---
# <a name="azure-delegated-resource-management"></a>Delegált Azure resource Managerrel

Delegált Azure resource Managerrel az egyik Azure világítótoronyig főbb összetevőit. Az Azure delegált felhőerőforrás-kezelés szolgáltatók leegyszerűsítheti az engagement és bevezetési ügyfélélményt, miközben villámgyorsan és precíziós ipari méretekben delegált erőforrások tartásával.

## <a name="what-is-azure-delegated-resource-management"></a>Mi az Azure delegált erőforrás-kezelés?

Delegált Azure erőforrás-kezelés lehetővé teszi, hogy a logikai vetülete egyetlen bérlő egy másik bérlőben alakzatot erőforrásokat. Ez lehetővé teszi, hogy a műveletek végrehajtása a másik Azure ad-ben egy Azure Active Directory (Azure AD) bérlő az engedéllyel rendelkező felhasználók bérlők tartozó ügyfeleiknek. Szolgáltatók jelentkezzen be a saját Azure AD-bérlő és a munkát delegált ügyfél-előfizetések és erőforráscsoportok engedéllyel rendelkeznek. Ez lehetővé teszi őket, ügyfeleik nevében műveletek végrehajtása nélkül jelentkezzen be minden egyes ügyfél-bérlő.

> [!NOTE]
> Delegált Azure resource Managerrel is használható, amely rendelkezik a saját egyszerűsítése érdekében több-bérlős felügyeleti több Azure AD-bérlőt egy vállalaton belül.

Az Azure delegált felhőerőforrás-kezelés engedéllyel rendelkező felhasználók az adott ügyfél bérlői fiókkal kellene vagy folyamatban van az ügyfél-bérlő társtulajdonos nélkül dolgozhat közvetlenül az ügyfél-előfizetés környezetében. Is [megtekintése és kezelése az összes delegált ügyfél-előfizetés az új **ügyfeleim** oldal](../how-to/view-manage-customers.md) az Azure Portalon.

A [több-bérlős környezetet](cross-tenant-management-experience.md) segítséget nyújt az Azure felügyeleti szolgáltatásaival hatékonyabban dolgozhatnak, például az Azure Policy, az Azure Security Center és egyéb. A tevékenységnapló-, ami a bérlők a service provider és az ügyfél az összes szolgáltatás szolgáltató tevékenység van követi. Ez azt jelenti, hogy az ügyfél és a service provider könnyen azonosítható a felhasználó társítva a módosításokat.

Amikor, készítse elő az Azure-bA egy ügyfél meghatalmazott erőforrás-kezelés rendelkeznek hozzáféréssel az új **szolgáltatók** oldal az Azure Portalon, ahol a következőkre [erősítse meg, és az ajánlatok, a szolgáltatók, kezelése és erőforrások delegált](../how-to/view-manage-service-providers.md). Ha az ügyfél minden eddiginél szeretné vonni a hozzáférést a szolgáltató, ezért itt megtehetik bármikor.

Is [a felügyelt szolgáltatások új ajánlattípusra közzététele az Azure piactéren](../how-to/publish-managed-services-offers.md) egyszerűen az Azure-bA ügyfelek delegált erőforrás-kezelést. Lehetőségként [az előkészítési folyamat befejezéséhez az Azure Resource Manager-sablonok üzembe helyezésével](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Hogyan a Azure erőforrás-kezelés delegált működik

Magas szinten a következő módját az Azure delegált erőforrás-kezelés működését:

1. Szolgáltatóként azonosítsa a hozzáférés (szerepkörök), amelyeket a csoportok, az egyszerű szolgáltatások vagy felhasználók kell az ügyfél Azure-erőforrások kezeléséhez. A hozzáféréssel definíció tartalmaz az ajánlat meghatározott a szükséges hozzáféréssel és a szolgáltató Bérlőazonosító **principalId** a bérlőről hozzárendelt identitások [beépített  **roleDefinition** értékek](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (közreműködő, virtuális gép közreműködő, olvasó, stb.).
2. Azt adja meg ezt a hozzáférést és készítse elő az ügyfél az Azure-bA delegálta a erőforrás-kezelés a két módszer egyikével:
   - [Azure Marketplace-en felügyelt szolgáltatások ajánlat közzétételének](../how-to/publish-managed-services-offers.md) (privát vagy nyilvános), amely az ügyfél elfogadja.
   - [Az Azure Resource Manager-sablon üzembe helyezésére az ügyfél bérlőn](../how-to/onboard-customer.md) egy vagy több meghatározott előfizetések vagy erőforráscsoportok
3. Miután az ügyfél már előkészítve, jogosult felhasználók jelentkezzen be a service provider bérlő, és az adott ügyfél hatókörben, a hozzáférés meghatározott alapuló felügyeleti feladatokat hajthat végre.

## <a name="support-for-azure-delegated-resource-management"></a>Az Azure támogatási delegált erőforrás-kezelés

Ha kapcsolatos segítségre van szüksége az Azure resource management meghatalmazott, akkor is nyisson meg egy támogatási kérést az Azure Portalon. A **Problématípus**, válassza a **technikai**. Válasszon ki egy előfizetést, majd válassza ki **delegált erőforrás-kezelés** (alatt **figyelés + felügyelet**).

## <a name="next-steps"></a>További lépések

- Ismerje meg [több-bérlős felhasználói élmény](cross-tenant-management-experience.md).
- Ismerje meg [felügyelt szolgáltatásokat kínál az Azure Marketplace-en](managed-services-offers.md).