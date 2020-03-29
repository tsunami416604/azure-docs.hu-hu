---
title: Az Azure által delegált erőforrás-kezelés
description: A felügyelt szolgáltatások lehetővé teszik a szolgáltatók számára, hogy erőforrás-kezelési ajánlatokat értékesítsék az Azure Marketplace-en lévő ügyfelek számára.
ms.date: 01/30/2020
ms.topic: conceptual
ms.openlocfilehash: 15814b1ca3b1b78de521033836e3614d18fd0c71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904316"
---
# <a name="azure-delegated-resource-management"></a>Az Azure által delegált erőforrás-kezelés

Az Azure delegált erőforrás-kezelése az Azure Lighthouse egyik legfontosabb összetevője. Az Azure delegált erőforrás-kezelésével a szolgáltatók egyszerűsíthetik az ügyfelek elkötelezettségét és a bevezetési élményt, miközben a delegált erőforrásokat agilis és precizitással kezelik.

## <a name="what-is-azure-delegated-resource-management"></a>Mi az Azure delegált erőforrás-kezelése?

Az Azure delegált erőforrás-kezelés lehetővé teszi az erőforrások logikai kivetítése az egyik bérlőről egy másik bérlőre. Ez lehetővé teszi, hogy egy Azure Active Directory (Azure AD) bérlő jogosult felhasználói felügyeleti műveleteket hajtsanak végre az ügyfeleikhez tartozó különböző Azure AD-bérlők között. A szolgáltatók bejelentkezhetnek a saját Azure AD-bérlőjükbe, és engedélyezve vannak a delegált ügyfél-előfizetésekben és erőforráscsoportokban való munkához. Ez lehetővé teszi számukra, hogy az ügyfelek nevében felügyeleti műveleteket hajtsanak végre anélkül, hogy minden egyes ügyfélbérlőbe be kellene jelentkezniük.

> [!NOTE]
> Az Azure delegált erőforrás-kezelése is használható [egy olyan vállalaton belül, amely több Azure AD-bérlők saját, hogy egyszerűsítse a](enterprise.md) bérlők közötti felügyelet.

Az Azure delegált erőforrás-kezelése, jogosult felhasználók közvetlenül dolgozhatnak az ügyfél-előfizetés környezetében anélkül, hogy az ügyfél bérlője, vagy hogy társtulajdonosa az ügyfél bérlője. Megtekinthetik [és kezelhetik az összes delegált ügyfél-előfizetést az **My customers** ](../how-to/view-manage-customers.md) Azure Portal új Ügyfelei lapján.

A [bérlők közötti felügyeleti élmény](cross-tenant-management-experience.md) segítségével hatékonyabban dolgozhat az Azure felügyeleti szolgáltatásaival, például az Azure Policy szolgáltatással, az Azure Security Centerrel és egyebekhasználatával. Minden szolgáltatótevékenység nyomon követhető a tevékenységnaplóban, amely a szolgáltató és az ügyfél bérlői között is tárolódik. Ez azt jelenti, hogy mind az ügyfél, mind a szolgáltató könnyen azonosíthatja a változásokhoz társított felhasználót.

Amikor egy ügyfelet az Azure delegált erőforrás-kezelésére **Service providers** alkalmaz, hozzáférhetnek az Azure Portal új Szolgáltatólapjához, ahol [megerősíthetik és kezelhetik ajánlataikat, szolgáltatóikat és delegált erőforrásaikat.](../how-to/view-manage-service-providers.md) Ha az ügyfél bármikor vissza kívánja vonni a szolgáltatóhoz való hozzáférést, ezt itt bármikor megteheti.

Az [azure piactéren közzéteheti az új felügyelt szolgáltatások ajánlattípusát,](../how-to/publish-managed-services-offers.md) így egyszerűen beszervezheti az ügyfeleket az Azure delegált erőforrás-kezelésére. Azt is megteheti, hogy [befejezi a bevezetési folyamatot az Azure Resource Manager-sablonok telepítésével.](../how-to/onboard-customer.md)

## <a name="how-azure-delegated-resource-management-works"></a>Az Azure delegált erőforrás-kezelése működése

Magas szinten az Azure delegált erőforrás-kezelése a következőképpen működik:

1. Szolgáltatóként azonosíthatja azokat a hozzáférést (szerepköröket), amelyeket a csoportoknak, egyszerű szolgáltatáscsoportoknak vagy felhasználóknak kell kezelnie az ügyfél Azure-erőforrásait. A hozzáférési definíció tartalmazza a szolgáltató bérlői azonosítóját, valamint az ajánlathoz szükséges hozzáférést, amelyet a bérlőből származó **rendszernévi** identitások használatával határoznak meg [a beépített **roleDefinition** értékekhez](../../role-based-access-control/built-in-roles.md) (közreműködő, virtuálisgép-közreműködő, olvasó stb.).
2. Ezt a hozzáférést és az azure delegált erőforrás-kezeléséhez az ügyfelet kétféleképpen adhatja meg:
   - Az Azure Marketplace felügyelt szolgáltatások (magán- vagy nyilvános) [ajánlatának közzététele,](../how-to/publish-managed-services-offers.md) amelyet az ügyfél elfogad
   - [Azure Resource Manager-sablon üzembe helyezése az ügyfél bérlője](../how-to/onboard-customer.md) számára egy vagy több konkrét előfizetéshez vagy erőforráscsoporthoz
3. Az ügyfél bedeszkázása után a jogosult felhasználók bejelentkezhetnek a szolgáltató bérlőjéhez, és felügyeleti feladatokat hajthatnak végre az adott ügyfélhatókörön, a megadott hozzáférés alapján.

> [!NOTE]
> Két bérlő közötti előfizetés delegálása külön felhők között nem támogatott.

## <a name="support-for-azure-delegated-resource-management"></a>Az Azure delegált erőforrás-kezelésének támogatása

Ha az Azure delegált erőforrás-kezelésével kapcsolatos segítségre van szüksége, megnyithat egy támogatási kérelmet az Azure Portalon. A **Problématípus mezőben**válassza a **Technical**lehetőséget. Válasszon ki egy előfizetést, majd válassza **a Világítótorony** **(a Monitoring & Management**csoportban).

## <a name="next-steps"></a>További lépések

- További információ a [bérlők közötti felügyeleti élményekről.](cross-tenant-management-experience.md)
- Ismerje meg [a felügyelt szolgáltatások ajánlatait az Azure Marketplace-en.](managed-services-offers.md)