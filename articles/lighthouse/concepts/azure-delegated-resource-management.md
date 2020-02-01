---
title: Az Azure által delegált erőforrás-kezelés
description: A felügyelt szolgáltatások lehetővé teszik a szolgáltatók számára, hogy erőforrás-kezelési ajánlatokat adjanak az ügyfeleknek az Azure piactéren.
ms.date: 01/30/2020
ms.topic: conceptual
ms.openlocfilehash: 15814b1ca3b1b78de521033836e3614d18fd0c71
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904316"
---
# <a name="azure-delegated-resource-management"></a>Az Azure által delegált erőforrás-kezelés

Az Azure-beli delegált erőforrás-kezelés az Azure Lighthouse egyik fő összetevője. Az Azure-beli delegált erőforrás-kezeléssel a szolgáltatók egyszerűsíthetik az ügyfelek bevonását és a bevezetést, miközben a delegált erőforrásokat a rugalmassággal és a pontossággal kezelik.

## <a name="what-is-azure-delegated-resource-management"></a>Mi az Azure-beli delegált erőforrás-kezelés?

Az Azure-beli delegált erőforrás-kezelés lehetővé teszi az erőforrások logikai kivetítését az egyik bérlőről egy másik bérlőre. Ez lehetővé teszi, hogy az egy Azure Active Directory (Azure AD) bérlő jogosult felhasználóinak felügyeleti műveleteket hajtsanak végre az ügyfelekhez tartozó különböző Azure AD-bérlők között. A szolgáltatók bejelentkezhetnek a saját Azure AD-bérlőbe, és engedéllyel rendelkeznek a delegált ügyfél-előfizetések és-erőforráscsoportok működéséhez. Ez lehetővé teszi, hogy az ügyfeleik nevében felügyeleti műveleteket végezzenek, anélkül, hogy be kellene jelentkezniük az egyes ügyfelek bérlői számára.

> [!NOTE]
> Az Azure-beli delegált erőforrás-kezelés [olyan vállalaton belül is felhasználható, amely több Azure ad-Bérlővel rendelkezik](enterprise.md) a bérlők közötti felügyelet egyszerűsítése érdekében.

Az Azure delegált erőforrás-kezelési szolgáltatással az engedélyezett felhasználók közvetlenül az ügyfél-előfizetés kontextusában dolgozhatnak, anélkül, hogy az ügyfél bérlője fiókjával vagy az ügyfél bérlője társtulajdonosa. Az [összes delegált ügyfél-előfizetést is megtekinthetik és kezelhetik az Azure Portal új **saját ügyfelek** lapján](../how-to/view-manage-customers.md) .

A több [-bérlős felügyeleti felülettel](cross-tenant-management-experience.md) hatékonyabban dolgozhat az Azure felügyeleti szolgáltatásaival, például a Azure Policy, a Azure Security Center és más szolgáltatásokkal. A rendszer az összes szolgáltatói tevékenységet nyomon követi a tevékenység naplójában, amelyet a szolgáltató és az ügyfél bérlői is tárolnak. Ez azt jelenti, hogy az ügyfél és a szolgáltató is könnyedén azonosíthatja a változásokhoz társított felhasználót.

Amikor az ügyfelet az Azure-beli delegált erőforrás-kezelésre helyezi, hozzáférhet a Azure Portal új **szolgáltatók** lapjához, ahol [megerősítheti és kezelheti az ajánlatait, szolgáltatóit és delegált erőforrásait](../how-to/view-manage-service-providers.md). Ha az ügyfél bármikor vissza kívánja vonni a szolgáltatóhoz való hozzáférést, itt bármikor megteheti.

[Az új felügyelt szolgáltatások ajánlat típusát az Azure Marketplace](../how-to/publish-managed-services-offers.md) -en teheti közzé, így az ügyfelek egyszerűen bevonhatók az Azure-beli delegált erőforrás-kezelésbe. Azt is megteheti, hogy [a bevezetési folyamatot Azure Resource Manager sablonok üzembe helyezésével hajtja végre](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Az Azure-beli delegált erőforrás-kezelés működése

Magas szinten a következőképpen működik az Azure-beli delegált erőforrás-kezelés:

1. Szolgáltatóként azonosítania kell a csoportok, a szolgáltatások és a felhasználók által az ügyfél Azure-erőforrásainak felügyeletéhez szükséges hozzáférési jogosultságokat (szerepköröket). A hozzáférési definíció tartalmazza a szolgáltató bérlői AZONOSÍTÓját, valamint az ajánlathoz szükséges hozzáférést, amely a bérlő **principalId** -identitások használatával van leképezve a [beépített **definíciós** -értékekre](../../role-based-access-control/built-in-roles.md) (közreműködő, VM közreműködő, olvasó stb.).
2. Ezt a hozzáférést és az ügyfelet az Azure-beli delegált erőforrás-kezeléshez kétféleképpen adhatja meg:
   - Az ügyfél által elfogadásra kerülő [Azure Marketplace-beli felügyelt szolgáltatások](../how-to/publish-managed-services-offers.md) (privát vagy nyilvános) közzététele
   - [Azure Resource Manager-sablon üzembe helyezése az ügyfél bérlője](../how-to/onboard-customer.md) számára egy vagy több konkrét előfizetés vagy erőforráscsoport esetén
3. Az ügyfél bevezetését követően a jogosult felhasználók bejelentkezhetnek a szolgáltatói bérlőbe, és a megadott hozzáférés alapján elvégezhetik a felügyeleti feladatokat az adott ügyfél hatókörében.

> [!NOTE]
> A két bérlő közötti előfizetés delegálása külön felhők között nem támogatott.

## <a name="support-for-azure-delegated-resource-management"></a>Azure-beli delegált erőforrás-kezelés támogatása

Ha segítségre van szüksége az Azure-beli delegált erőforrás-kezeléshez, egy támogatási kérést is megnyithat a Azure Portal. A **probléma típusa**beállításnál válassza a **technikai**lehetőséget. Válasszon ki egy előfizetést, majd válassza a **világítótorony** lehetőséget (a **figyelés & felügyelet**alatt).

## <a name="next-steps"></a>Következő lépések

- További információ a [bérlők közötti felügyeleti élményekről](cross-tenant-management-experience.md).
- Ismerje meg a [felügyelt szolgáltatások ajánlatait az Azure Marketplace-](managed-services-offers.md)en.