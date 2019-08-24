---
title: Hozzáférés engedélyezése Azure Active Directory
description: Ez a cikk a Azure Active Directory használatával történő Event Hubs erőforrásokhoz való hozzáférés engedélyezésére vonatkozó információkat tartalmaz.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 569399b2b5f676e11cd6540126e0b26dc0d26198
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992860"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Hozzáférés engedélyezése Event Hubs erőforrásokhoz a Azure Active Directory használatával
Az Azure Event Hubs támogatja a Azure Active Directory (Azure AD) használatát, hogy engedélyezze a kérelmeket Event Hubs erőforrásoknak. Az Azure AD-vel szerepköralapú hozzáférés-vezérlés (RBAC) használatával adhat meg engedélyeket egy rendszerbiztonsági tag számára, amely lehet egy felhasználó, egy csoport vagy egy egyszerű szolgáltatásnév. További információ a szerepkörökről és a szerepkör-hozzárendelésekről: [a különböző szerepkörök megismerése](../role-based-access-control/overview.md).

## <a name="overview"></a>Áttekintés
Ha egy rendszerbiztonsági tag (felhasználó, csoport vagy alkalmazás) egy Event Hubs erőforráshoz próbál hozzáférni, a kérést engedélyezni kell. Az Azure AD-vel az erőforrásokhoz való hozzáférés kétlépéses folyamat. 

 1. Először a rendszerbiztonsági tag identitása hitelesítve van, és a rendszer egy OAuth 2,0 tokent ad vissza. 
 1. Ezután a jogkivonat a Event Hubs szolgáltatásnak küldött kérelem részeként a megadott erőforráshoz való hozzáférés engedélyezéséhez lesz átadva.

A hitelesítési lépés megköveteli, hogy egy alkalmazás-kérelem OAuth 2,0 hozzáférési jogkivonatot tartalmazzon futásidőben. Ha egy alkalmazás egy Azure-entitáson, például egy Azure-beli virtuális gépen, egy virtuálisgép-méretezési csoporton vagy egy Azure Function-alkalmazáson belül fut, akkor a felügyelt identitás használatával férhet hozzá az erőforrásokhoz. Ha meg szeretné tudni, hogyan hitelesítheti a felügyelt identitások által küldött kéréseket Event Hubs szolgáltatásra, tekintse meg az Azure [Event Hubs-erőforrások hozzáférésének hitelesítése Azure Active Directory és felügyelt identitások Azure-erőforrásokhoz](authenticate-managed-identity.md)című témakört. 

Az engedélyezési lépés megköveteli, hogy egy vagy több RBAC-szerepkört hozzá lehessen rendelni a rendszerbiztonsági tag számára. Az Azure Event Hubs olyan RBAC-szerepköröket biztosít, amelyek a Event Hubs erőforrásokra vonatkozó engedélyek készleteit foglalják magukban. A rendszerbiztonsági tag számára hozzárendelt szerepkörök határozzák meg, hogy a résztvevő milyen engedélyeket fog tartalmazni. A RBAC szerepköreivel kapcsolatos további információkért lásd: [Az Azure Event Hubs beépített RBAC szerepkörei](#built-in-rbac-roles-for-azure-event-hubs). 

A Event Hubsra irányuló kérelmeket használó natív alkalmazások és webalkalmazások is engedélyezhetik az Azure AD-t. Ha szeretné megtudni, hogyan kérhet hozzáférési jogkivonatot, és hogyan engedélyezheti a kérelmeket Event Hubs erőforrásokhoz, tekintse meg az [azure Event Hubs hozzáférésének hitelesítése az Azure ad-vel](authenticate-application.md)az alkalmazásokból című témakört. 

## <a name="assign-rbac-roles-for-access-rights"></a>Hozzáférési jogosultságok RBAC-szerepköreinek kiosztása
Azure Active Directory (Azure AD) a [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md)segítségével engedélyezi a hozzáférési jogokat a biztonságos erőforrásokhoz. Az Azure Event Hubs olyan beépített RBAC-szerepköröket határoz meg, amelyek az Event hub-adatok eléréséhez használt általános engedélyeket foglalják magukban, és egyéni szerepköröket is meghatározhat az adatok eléréséhez.

Ha egy Azure AD-rendszerbiztonsági tag egy RBAC-szerepkört rendel hozzá, az Azure hozzáférést biztosít ezen rendszerbiztonsági tag erőforrásaihoz. A hozzáférés hatóköre az előfizetés, az erőforráscsoport, a Event Hubs névtér vagy az alatta lévő erőforrás szintjére is kiterjed. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy felügyelt [identitás az Azure](../active-directory/managed-identities-azure-resources/overview.md)-erőforrásokhoz.

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Az Azure Event Hubs beépített RBAC szerepkörei
Az Azure a következő beépített RBAC-szerepköröket biztosítja az Azure AD-vel és OAuth-vel Event Hubs való hozzáférés engedélyezéséhez:

- [Az Azure Event Hubs adattulajdonos](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner-preview): Ezzel a szerepkörrel teljes hozzáférést biztosíthat Event Hubs erőforrásaihoz.
- [Azure Event Hubs adatfeladó](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver-preview): Ezzel a szerepkörrel biztosíthatja a küldési hozzáférést Event Hubs erőforrásaihoz.
- [Azure Event Hubs adatfogadó](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender-preview): Ennek a szerepkörnek a használatával biztosíthatja a Event Hubs erőforrásainak felhasználását/fogadását.

## <a name="resource-scope"></a>Erőforrás hatóköre 
Mielőtt RBAC-szerepkört rendeljen egy rendszerbiztonsági tag számára, határozza meg a rendszerbiztonsági tag hozzáférésének hatókörét. Az ajánlott eljárások azt diktálják, hogy mindig csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyekkel a Event Hubs erőforrásaihoz férhet hozzá, a legszűkebb hatókörtől kezdve:

- **Fogyasztói csoport**: Ezen a hatókörön a szerepkör-hozzárendelés csak erre az entitásra vonatkozik. A Azure Portal jelenleg nem támogatja az RBAC-szerepkör hozzárendelését egy rendszerbiztonsági tag számára ezen a szinten. 
- **Event hub**: A szerepkör-hozzárendelés az Event hub-entitásra és az alatta lévő fogyasztói csoportra vonatkozik.
- **Névtér**: A szerepkör-hozzárendelés a névtér és a hozzá társított fogyasztói csoport teljes topológiáját Event Hubs.
- **Erőforráscsoport**: A szerepkör-hozzárendelés az erőforráscsoport összes Event Hubs erőforrására vonatkozik.
- **Előfizetés**: A szerepkör-hozzárendelés az előfizetés összes erőforrás-csoportjának összes Event Hubs erőforrására vonatkozik.

> [!NOTE]
> Ne feledje, hogy a RBAC szerepkör-hozzárendelések akár öt percet is igénybe vehetnek. 

További információ a beépített szerepkörök meghatározásáról: a szerepkör-definíciók [ismertetése](../role-based-access-control/role-definitions.md#management-and-data-operations). Az egyéni RBAC-szerepkörök létrehozásával kapcsolatos információkért tekintse meg [Az Azure szerepköralapú Access Control egyéni szerepköreinek létrehozása](../role-based-access-control/custom-roles.md)című témakört.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan rendeljen hozzá egy beépített RBAC szerepkört egy rendszerbiztonsági tag számára, lásd: [hozzáférés hitelesítése Event Hubs erőforrásokhoz Azure Active Directory használatával](authenticate-application.md).
- Ismerje meg, [hogyan hozhat létre egyéni szerepköröket a RBAC használatával](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- Ismerje meg [, hogyan használhatja a Azure Active Directoryt az Eh használatával](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Tekintse meg a következő kapcsolódó cikkeket:

- [Kérelmek hitelesítése az Azure Event Hubs alkalmazásból Azure Active Directory használatával](authenticate-application.md)
- [Felügyelt identitás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez](authenticate-managed-identity.md)
- [Kérelmek hitelesítése az Azure Event Hubs megosztott hozzáférési aláírások használatával](authenticate-shared-access-signature.md)
- [Hozzáférés engedélyezése Event Hubs erőforrásokhoz közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md)