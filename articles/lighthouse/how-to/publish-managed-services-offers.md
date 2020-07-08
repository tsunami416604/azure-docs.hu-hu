---
title: Felügyelt szolgáltatás ajánlatának közzététele az Azure Marketplace-en
description: Megtudhatja, hogyan tehet közzé egy felügyelt szolgáltatási ajánlatot, amely az ügyfeleket az Azure-beli delegált erőforrás-kezeléshez irányítja.
ms.date: 05/04/2020
ms.topic: how-to
ms.openlocfilehash: 214a71faca59072660f1e1f413cb107d8e8f6fc9
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85920892"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Felügyelt szolgáltatás ajánlatának közzététele az Azure Marketplace-en

Ebből a cikkből megtudhatja, hogyan tehet közzé nyilvános vagy privát felügyelt szolgáltatást az [Azure Marketplace](https://azuremarketplace.microsoft.com) -en a partner Center [kereskedelmi piactér](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) programjának használatával. Az ajánlatot megvásárló ügyfelek ezután előfizethetnek az Azure-beli [delegált erőforrás-kezeléshez](../concepts/azure-delegated-resource-management.md)szükséges előfizetések és erőforráscsoportok bevezetésére.

## <a name="publishing-requirements"></a>Közzétételi követelmények

Ajánlatok létrehozásához és közzétételéhez érvényes fiókkal kell rendelkeznie a [partner Centerben](../../marketplace/partner-center-portal/create-account.md) . Ha még nem rendelkezik fiókkal, a [regisztrációs folyamat](https://aka.ms/joinmarketplace) végigvezeti a fiók létrehozásának lépésein a partner Centerben és a kereskedelmi piactér programban való regisztrálásban.

A [felügyelt szolgáltatásokra vonatkozó minősítési követelmények](/legal/marketplace/certification-policies#7004-business-requirements)alapján a felügyelt szolgáltatások ajánlatának közzétételéhez egy [Silver vagy Gold Cloud platform kompetenciával](/partner-center/learn-about-competencies) vagy [Azure szakértői MSP](https://partner.microsoft.com/membership/azure-expert-msp) -vel kell rendelkeznie.

A [rendszer automatikusan hozzárendeli](../../billing/billing-partner-admin-link-started.md) az Ön Microsoft Partner Network (MPN) azonosítóját a közzétett ajánlatokhoz, és nyomon követheti az ügyfelek bevonásának következményeit.

> [!NOTE]
> Ha nem szeretne ajánlatot közzétenni az Azure Marketplace-en, Azure Resource Manager-sablonok használatával manuálisan is bevezetheti az ügyfeleket. További információ: az ügyfél beléptetése [Az Azure által delegált erőforrás-felügyeletbe](onboard-customer.md).

## <a name="create-your-offer"></a>Ajánlat létrehozása

Az ajánlat létrehozásával kapcsolatos részletes utasításokért, beleértve az összes szükséges információt és eszközt, tekintse meg [a felügyelt szolgáltatási ajánlat létrehozása](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)című témakört.

Az általános közzétételi folyamattal kapcsolatos további információkért lásd: az [Azure Marketplace és az AppSource közzétételi útmutatója](../../marketplace/marketplace-publishers-guide.md). Tekintse át a [kereskedelmi piactérre vonatkozó minősítési szabályzatokat](/legal/marketplace/certification-policies), különösen a [felügyelt szolgáltatások](/legal/marketplace/certification-policies#700-managed-services) szakaszt.

Ha egy ügyfél felveszi az ajánlatot, egy vagy több előfizetést vagy erőforráscsoportot is delegálhat, amelyek ezután bekerülnek [Az Azure-beli delegált erőforrás-kezelésbe](#the-customer-onboarding-process).

> [!IMPORTANT]
> A felügyelt szolgáltatási ajánlat minden csomagja tartalmaz egy **jegyzékfájl részleteit** tartalmazó szakaszt, ahol megadhatja a bérlő azon Azure Active Directory (Azure ad) entitásait, amelyek hozzáférnek a megvásárolt erőforrásokhoz és/vagy előfizetésekhez a tervet megvásárló ügyfelek számára. Fontos megjegyezni, hogy az Ön által belefoglalt bármely csoport (vagy felhasználó vagy szolgáltatásnév) ugyanazokkal az engedélyekkel fog rendelkezni minden olyan ügyfélnél, aki megvásárolja a csomagot. Ha különböző csoportokat szeretne hozzárendelni az egyes ügyfelekkel való munkához, egy külön [privát csomagot](../../marketplace/private-offers.md) kell közzétennie, amely kizárólag az egyes ügyfelekre vonatkozik.

## <a name="publish-your-offer"></a>Ajánlat közzététele

Miután elvégezte az összes szakaszt, a következő lépés az ajánlat közzététele az Azure Marketplace-en. Válassza a **Közzététel** gombot, hogy elindítsa az ajánlatát élőben. A folyamatról további információt [itt](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish)talál. 

Bármikor [közzéteheti az ajánlat frissített verzióját](../..//marketplace/partner-center-portal/update-existing-offer.md) . Előfordulhat például, hogy új szerepkör-definíciót szeretne hozzáadni egy korábban közzétett ajánlathoz. Ha így tesz, a már hozzáadott ügyfelek egy ikont láthatnak a Azure Portal [**szolgáltatók**](view-manage-service-providers.md) lapján, amely lehetővé teszi, hogy a frissítés elérhető legyen. Minden ügyfél [áttekintheti a módosításokat](view-manage-service-providers.md#update-service-provider-offers) , és eldöntheti, hogy szeretné-e frissíteni az új verzióra. 

## <a name="the-customer-onboarding-process"></a>Az ügyfél bevezetési folyamata

Miután az ügyfél hozzáadja az ajánlatot, [egy vagy több konkrét előfizetést vagy erőforráscsoportot delegálhat](view-manage-service-providers.md#delegate-resources), amelyek ezután bekerülnek az Azure-beli delegált erőforrás-kezelésbe. Ha egy ügyfél elfogadta az ajánlatot, de még nem delegált erőforrást, akkor a Azure Portal [**szolgáltatók**](view-manage-service-providers.md) lapján a **szolgáltatói ajánlatok** szakaszának felső részén egy megjegyzés jelenik meg.

> [!IMPORTANT]
> A delegálást olyan nem vendég fiókkal kell végrehajtani az ügyfél bérlője számára, amely az előfizetéshez tartozó [tulajdonos beépített szerepkörrel](../../role-based-access-control/built-in-roles.md#owner) rendelkezik (vagy amely tartalmazza az előkészítés alatt álló erőforráscsoportokat). Ha szeretné megtekinteni az összes olyan felhasználót, aki delegálhatja az előfizetést, az ügyfél bérlője kiválaszthatja az előfizetést a Azure Portalban, megnyithatja a **hozzáférés-vezérlés (iam)** elemet, és [megtekintheti a tulajdonosi szerepkörrel rendelkező felhasználókat](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Miután az ügyfél delegált egy előfizetést (vagy egy vagy több erőforráscsoportot egy előfizetésen belül), a **Microsoft. ManagedServices** erőforrás-szolgáltató regisztrálva lesz az adott előfizetéshez, és a bérlő felhasználói hozzáférhetnek a delegált erőforrásokhoz az ajánlatban foglalt jogosultságok alapján.

## <a name="next-steps"></a>További lépések

- További információ a [kereskedelmi piactérről](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).
- [Megtekintheti és kezelheti az ügyfeleket](view-manage-customers.md) a Azure Portalban lévő **ügyfelekkel** .
