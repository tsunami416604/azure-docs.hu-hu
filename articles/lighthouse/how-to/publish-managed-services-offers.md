---
title: Felügyelt szolgáltatásajánlat közzététele az Azure Piactéren
description: Ismerje meg, hogyan tehet közzé egy felügyelt szolgáltatásajánlatot, amely az ügyfeleket az Azure delegált erőforrás-kezelésére hozza.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 922e78a0715ce5c758ca7d068a38af43e214a524
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673936"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Felügyelt szolgáltatásajánlat közzététele az Azure Piactéren

Ebből a cikkből megtudhatja, hogyan tehet közzé nyilvános vagy privát felügyelt szolgáltatási ajánlatot az [Azure Piactéren](https://azuremarketplace.microsoft.com) a [PartnerCenter](https://partner.microsoft.com/) [Kereskedelmi piactér](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) programjával. Az ajánlatot megvásárló ügyfelek ezután előadhatják az [Azure delegált erőforrás-kezeléséhez](../concepts/azure-delegated-resource-management.md)szükséges előfizetéseket és erőforráscsoportokat.

## <a name="publishing-requirements"></a>Közzétételi követelmények

Az ajánlatok létrehozásához és közzétételéhez érvényes fiókkal kell rendelkeznie [a Partnerközpontban.](../../marketplace/partner-center-portal/create-account.md) Ha még nem rendelkezik fiókkal, a [regisztrációs folyamat](https://aka.ms/joinmarketplace) végigvezeti a Partnerközpontban létrehozott és a Kereskedelmi piactér programba való regisztráció lépésein.

A [felügyelt szolgáltatás ajánlatminősítési követelményeinek megfelelően](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements) [Silver vagy Gold Cloud Platform kompetenciaszinttel kell rendelkeznie,](https://docs.microsoft.com/partner-center/learn-about-competencies) vagy Azure Expert [MSP-nek](https://partner.microsoft.com/membership/azure-expert-msp) kell lennie a felügyelt szolgáltatásajánlat közzétételéhez.

A Microsoft Partner Network (MPN) azonosító [automatikusan társítva](../../billing/billing-partner-admin-link-started.md) lesz a közzétett ajánlatokkal, hogy nyomon követhesse az ügyfélkapcsolatokra gyakorolt hatását.

> [!NOTE]
> Ha nem szeretne közzétenni egy ajánlatot az Azure Marketplace-en, manuálisan is bevezetheti az ügyfeleket az Azure Resource Manager-sablonok használatával. További információ: [Az Azure delegált erőforrás-kezelése ügyfél nek.](onboard-customer.md)

## <a name="create-your-offer"></a>Az ajánlat létrehozása

Az ajánlat létrehozásáról részletes útmutatást, beleértve az összes szükséges információt és eszközt, olvassa el az Új felügyelt szolgáltatások ajánlatának létrehozása című [témakört.](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)

Az általános közzétételi folyamatról az Azure Marketplace és az [AppSource közzétételi útmutatócímű témakörben olvashat.](../../marketplace/marketplace-publishers-guide.md) Tekintse át a [kereskedelmi piactér tanúsítási irányelveit](https://docs.microsoft.com/legal/marketplace/certification-policies)is, különösen a [Felügyelt szolgáltatások szakaszt.](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)

Miután egy ügyfél hozzáadja az ajánlatot, delegálhat egy vagy több előfizetést vagy erőforráscsoportot, amelyeket ezután [az Azure delegált erőforrás-kezeléséhez vesznek fel.](#the-customer-onboarding-process)

> [!IMPORTANT]
> A felügyelt szolgáltatásajánlat minden egyes csomagja tartalmaz egy **Manifest Details szakaszt,** ahol meghatározza az Azure Active Directory (Azure AD) entitásokat a bérlőben, amelyek hozzáférhetnek a delegált erőforráscsoportokhoz és/vagy előfizetésekhez az ilyen csomagot megvásárló ügyfelek számára. Fontos tudni, hogy minden csoport (vagy felhasználó vagy egyszerű szolgáltatás) tartalmazza lesz azonos engedélyekkel minden ügyfél, aki megvásárolja a csomagot. Ha különböző csoportokat szeretne hozzárendelni az egyes ügyfelekkel való munkához, külön [privát csomagot](../../marketplace/private-offers.md) kell közzétennie, amely kizárólag az egyes ügyfelek számára elérhető.

## <a name="publish-your-offer"></a>Az ajánlat közzététele

Miután elvégezte az összes szakaszt, a következő lépés az ajánlat közzététele az Azure Marketplace-en. Válassza a **Közzététel** gombot az ajánlat élő közvetítésének kezdeményezéséhez. További információ erről a folyamatról [itt](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish)található .

Az [ajánlat frissített verzióját](../../marketplace/partner-center-portal/update-existing-offer.md) bármikor közzéteheti. Előfordulhat például, hogy új szerepkör-definíciót szeretne hozzáadni egy korábban közzétett ajánlathoz. Ha így tesz, az ajánlathoz már hozzáadott ügyfelek egy ikont fognak látni az Azure Portal [**Szolgáltatói**](view-manage-service-providers.md) lapján, amely tudatja velük, hogy elérhető egy frissítés. Minden ügyfél [áttekintheti a módosításokat,](view-manage-service-providers.md#update-service-provider-offers) és eldöntheti, hogy szeretne-e frissíteni az új verzióra. 

## <a name="the-customer-onboarding-process"></a>Az ügyfél bevezetési folyamata

Miután egy ügyfél hozzáadja az ajánlatot, egy [vagy több konkrét előfizetést vagy erőforráscsoportot delegálhat,](view-manage-service-providers.md#delegate-resources)amelyeket ezután az Azure delegált erőforrás-kezeléséhez beszerveznek. Ha egy ügyfél elfogadott egy ajánlatot, de még nem delegált erőforrásokat, akkor megjelenik egy megjegyzés a **szolgáltató ajánlatok** szakaszának tetején a [**Szolgáltatók**](view-manage-service-providers.md) lap az Azure Portalon.

> [!IMPORTANT]
> A delegálást az ügyfél bérlőjében lévő nem vendégfióknak kell elvégeznie, amely rendelkezik a [tulajdonos beépített szerepkörrel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) (vagy amely a fedélzeti erőforráscsoportokat tartalmazza). Az előfizetésdelegálható összes felhasználó megtekintéséhez az ügyfél bérlőjének egyik felhasználója kiválaszthatja az előfizetést az Azure Portalon, megnyithatja a **hozzáférés-vezérlést (IAM)** és [megtekintheti a Tulajdonos szerepkörrel rendelkező összes felhasználót.](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)

Miután az ügyfél delegál egy előfizetést (vagy egy előfizetésen belül egy vagy több erőforráscsoportot), a **Microsoft.ManagedServices** erőforrás-szolgáltató regisztrálva lesz az adott előfizetéshez, és a bérlő felhasználói hozzáférhetnek a delegált erőforrásokhoz az ajánlatban szereplő engedélyek nek megfelelően.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [kereskedelmi piactérről.](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)
- További információ a [bérlők közötti felügyeleti élményekről.](../concepts/cross-tenant-management-experience.md)
- Az Azure Portalon az **Ügyfeleimmel** tekintheti meg és kezelheti az [ügyfeleket.](view-manage-customers.md)
