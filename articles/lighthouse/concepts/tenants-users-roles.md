---
title: Bérlők, szerepkörök és felhasználók az Azure Lighthouse-forgatókönyvekben
description: Megismerheti Azure Active Directory bérlők, a felhasználók és a szerepkörök fogalmait, valamint azt, hogy miként használhatók az Azure Lighthouse-forgatókönyvekben.
ms.date: 11/05/2019
ms.topic: conceptual
ms.openlocfilehash: a1ad004c79f90f4642640405da9e8876b9202e98
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463924"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Bérlők, szerepkörök és felhasználók az Azure Lighthouse-forgatókönyvekben

Az Azure-beli [delegált erőforrás-kezeléshez szükséges ügyfelek bevezetéséhez](azure-delegated-resource-management.md)fontos tisztában lennie azzal, hogyan működnek a Azure Active Directory (Azure ad) bérlők, a felhasználók és a szerepkörök, valamint hogyan használhatók az Azure Lighthouse-forgatókönyvekben.

A *bérlő* az Azure ad dedikált és megbízható példánya. Az egyes bérlők általában egyetlen szervezetnek felelnek meg. Az Azure-beli delegált erőforrás-kezelés lehetővé teszi az erőforrások logikai kivetítését az egyik bérlőről egy másik bérlőre. Ez lehetővé teszi a bérlők felügyeletét (például egy szolgáltatóhoz tartozót) a delegált erőforrások elérésére az ügyfél bérlője számára, vagy lehetővé teszi, hogy [több Bérlővel rendelkező vállalatok központosítsák a felügyeleti műveleteiket](enterprise.md).

Ahhoz, hogy ez a logikai leképezés elérhető legyen, előfizetést (vagy egy vagy több, előfizetésen belüli erőforráscsoportot) kell előkészíteni az ügyfél bérlője *számára az* Azure-beli delegált erőforrás-kezeléshez. Ez a [bevezetési folyamat Azure Resource Manager-sablonokkal](../how-to/onboard-customer.md) vagy [nyilvános vagy privát ajánlat Azure Marketplace-en való közzétételével](../how-to/publish-managed-services-offers.md)végezhető el.

Bármelyik bevezetési módszert választja, meg kell adnia az *engedélyeket*. Az egyes engedélyek egy felhasználói fiókot határoznak meg a bérlők kezelése szolgáltatásban, amely hozzáfér a delegált erőforrásokhoz, valamint egy beépített szerepkört, amely megadja, hogy az egyes felhasználók milyen engedélyeket kapnak ezekhez az erőforrásokhoz.

## <a name="role-support-for-azure-delegated-resource-management"></a>Szerepkör-támogatás az Azure-beli delegált erőforrás-kezeléshez

Az engedélyezés meghatározásakor minden felhasználói fiókhoz hozzá kell rendelni a [szerepköralapú hozzáférés-vezérlés (RBAC) beépített szerepköreinek](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)egyikét. Az egyéni szerepkörök és a [klasszikus előfizetés-rendszergazdai szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) nem támogatottak.

Az Azure-beli delegált erőforrás-kezelés jelenleg az összes [beépített szerepkört](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) támogatja, a következő kivételekkel:

- A [tulajdonosi](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) szerepkör nem támogatott.
- A [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) engedéllyel rendelkező beépített szerepkörök nem támogatottak.
- A [felhasználói hozzáférés rendszergazdai](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) beépített szerepköre támogatott, de csak azzal a korlátozott céllal, [hogy szerepköröket rendeljen hozzá egy felügyelt identitáshoz az ügyfél bérlője](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)számára. Ehhez a szerepkörhöz általában nem érvényesek más engedélyek. Ha megad egy felhasználót a szerepkörhöz, meg kell adnia azokat a beépített szerepkör (eke) t, amelyeket a felhasználó a felügyelt identitásokhoz hozzárendelhet.

## <a name="best-practices-for-defining-users-and-roles"></a>Ajánlott eljárások felhasználók és szerepkörök definiálásához

Az engedélyek létrehozásakor javasoljuk a következő ajánlott eljárásokat:

- A legtöbb esetben egy Azure AD-felhasználói csoporthoz vagy egyszerű szolgáltatáshoz kell engedélyeket rendelni, nem pedig egyéni felhasználói fiókokhoz. Ez lehetővé teszi az egyes felhasználók hozzáférésének hozzáadását vagy eltávolítását anélkül, hogy a hozzáférési követelmények változásakor frissítenie és újból közzé kellene tennie a tervet.
- Ügyeljen arra, hogy kövesse a legalacsonyabb jogosultsági szint elvét, hogy a felhasználók csak a feladataik elvégzéséhez szükséges engedélyekkel rendelkezzenek, ami segít csökkenteni a véletlen hibák esélyét. További információ: [ajánlott biztonsági eljárások](../concepts/recommended-security-practices.md).
- Vegyen fel egy felhasználót a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörével](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) , így szükség esetén később is [eltávolíthatja a delegáláshoz való hozzáférést](../how-to/onboard-customer.md#remove-access-to-a-delegation) . Ha ez a szerepkör nincs hozzárendelve, a delegált erőforrásokat csak egy felhasználó távolíthatja el az ügyfél bérlője számára.
- Győződjön meg arról, hogy minden olyan felhasználónak, akinek meg kell [tekintenie a saját ügyfelek lapot a Azure Portal](../how-to/view-manage-customers.md) rendelkezik az [olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) szerepkörrel (vagy egy másik beépített szerepkörrel, amely olvasói hozzáféréssel rendelkezik).

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [Az Azure-beli delegált erőforrás-kezelés ajánlott biztonsági eljárásait](recommended-security-practices.md).
- Az ügyfeleket az Azure-beli delegált erőforrás-kezeléshez [Azure Resource Manager sablonok használatával](../how-to/onboard-customer.md) vagy [egy magán-vagy nyilvános felügyelt szolgáltatás Azure Marketplace-re való közzétételével](../how-to/publish-managed-services-offers.md)teheti közzé.
