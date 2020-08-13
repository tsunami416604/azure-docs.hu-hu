---
title: Delegált erőforrás-kezelés az Azure-ban
description: Az Azure-beli delegált erőforrás-kezelés az Azure világítótorony egyik kulcsfontosságú része, amely lehetővé teszi a szolgáltatók számára, hogy a nagy mennyiségű, rugalmasságot és pontosságot biztosító, felügyelt erőforrásokat kezelje.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 9a499ceda546b7ea5c71cd8c770f1a4b99001b08
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163526"
---
# <a name="azure-delegated-resource-management"></a>Delegált erőforrás-kezelés az Azure-ban

Az Azure-beli delegált erőforrás-kezelés az [Azure Lighthouse](../overview.md)egyik fő összetevője. Az Azure-beli delegált erőforrás-kezeléssel a szolgáltatók egyszerűsíthetik az ügyfelek bevonását és a bevezetést, miközben a delegált erőforrásokat a rugalmassággal és a pontossággal kezelik.

## <a name="what-is-azure-delegated-resource-management"></a>Mi az Azure-beli delegált erőforrás-kezelés?

Az Azure-beli delegált erőforrás-kezelés lehetővé teszi az erőforrások logikai kivetítését az egyik bérlőről egy másik bérlőre. Ez lehetővé teszi, hogy az egy Azure Active Directory (Azure AD) bérlő jogosult felhasználóinak felügyeleti műveleteket hajtsanak végre az ügyfelekhez tartozó különböző Azure AD-bérlők között. A szolgáltatók bejelentkezhetnek a saját Azure AD-bérlőbe, és engedéllyel rendelkeznek a delegált ügyfél-előfizetések és-erőforráscsoportok működéséhez. Ez lehetővé teszi, hogy az ügyfeleik nevében felügyeleti műveleteket végezzenek, anélkül, hogy be kellene jelentkezniük az egyes ügyfelek bérlői számára.

> [!TIP]
> Az Azure-beli delegált erőforrás-kezelés [olyan vállalaton belül is felhasználható, amely több Azure ad-Bérlővel rendelkezik](enterprise.md) a bérlők közötti felügyelet egyszerűsítése érdekében.

Az Azure delegált erőforrás-kezelési szolgáltatással az engedélyezett felhasználók közvetlenül az ügyfél-előfizetés kontextusában dolgozhatnak, anélkül, hogy az ügyfél bérlője fiókjával vagy az ügyfél bérlője társtulajdonosa.

A több [-bérlős felügyeleti felület](cross-tenant-management-experience.md) lehetővé teszi, hogy hatékonyabban működjön együtt az Azure felügyeleti szolgáltatásaival, például a Azure Policy, a Azure Security Center és más szolgáltatásokkal. Az összes szolgáltatói tevékenységet nyomon követik a tevékenység naplójában, amelyet az ügyfél bérlője tárol (és megtekintheti a bérlők felügyelete alatt lévő felhasználók). Ez azt jelenti, hogy a felügyelet és a felügyelt bérlőben lévő felhasználók könnyen azonosíthatják a módosításokhoz kapcsolódó felhasználókat.

[Az új felügyelt szolgáltatási ajánlat típusát az Azure Marketplace](../how-to/publish-managed-services-offers.md) -en teheti közzé, így egyszerűen bevezetheti az ügyfeleket az Azure Lighthouse szolgáltatásba. Azt is megteheti, hogy [a bevezetési folyamatot Azure Resource Manager sablonok üzembe helyezésével hajtja végre](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Az Azure-beli delegált erőforrás-kezelés működése

Magas szinten a következőképpen működik az Azure-beli delegált erőforrás-kezelés:

1. Először azonosítania kell a csoportoknak, a szolgáltatásoknak vagy a felhasználóknak az ügyfél Azure-erőforrásainak felügyeletéhez szükséges hozzáférési jogosultságokat (szerepköröket). A hozzáférési definíció tartalmazza a bérlő AZONOSÍTÓját, valamint a bérlő **principalId** -identitásait a [beépített **definíciós** -értékekre](../../role-based-access-control/built-in-roles.md) (közreműködő, VM közreműködő, olvasó stb.).
2. Ezt a hozzáférést és az ügyfelet az Azure Lighthouse-be a következő két módszer egyikével adhatja meg:
   - Az ügyfél által elfogadásra kerülő [Azure Marketplace-beli felügyelt szolgáltatási ajánlat](../how-to/publish-managed-services-offers.md) (privát vagy nyilvános) közzététele
   - [Azure Resource Manager-sablon üzembe helyezése az ügyfél bérlője](../how-to/onboard-customer.md) számára egy vagy több konkrét előfizetés vagy erőforráscsoport esetén
3. Az ügyfél bevezetését követően a jogosult felhasználók bejelentkezhetnek a felügyeleti bérlőbe, és feladatokat végezhetnek el a megadott ügyfél-hatókörben a megadott hozzáférés alapján.

> [!NOTE]
> A különböző [régiókban](../../availability-zones/az-overview.md#regions)található delegált erőforrásokat is kezelheti. Azonban az előfizetések delegálása egy [nemzeti felhőben](../../active-directory/develop/authentication-national-cloud.md) és az Azure nyilvános felhőben, vagy két külön nemzeti felhők között nem támogatott.

## <a name="support-for-azure-delegated-resource-management"></a>Azure-beli delegált erőforrás-kezelés támogatása

Ha segítségre van szüksége az Azure-beli delegált erőforrás-kezeléshez, egy támogatási kérést is megnyithat a Azure Portal. A **probléma típusa**beállításnál válassza a **technikai**lehetőséget. Válasszon ki egy előfizetést, majd válassza a **világítótorony** lehetőséget (a **figyelés & felügyelet**alatt).

## <a name="next-steps"></a>További lépések

- További információ a [bérlők közötti felügyeleti élményekről](cross-tenant-management-experience.md).
- Ismerje meg a [felügyelt szolgáltatások ajánlatait az Azure Marketplace-](managed-services-offers.md)en.
