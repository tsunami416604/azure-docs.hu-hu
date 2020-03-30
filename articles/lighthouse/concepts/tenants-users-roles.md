---
title: Bérlők, szerepkörök és felhasználók az Azure Világítótorony-forgatókönyvekben
description: Ismerje meg az Azure Active Directory-bérlők, -felhasználók és szerepkörök fogalmait, valamint azt, hogy miként használhatók az Azure Lighthouse-forgatókönyvekben.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 7540e17fd80f9a1d8e996295000c126614b838d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246891"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Bérlők, szerepkörök és felhasználók az Azure Világítótorony-forgatókönyvekben

Mielőtt az [Azure delegált erőforrás-kezeléséhez](azure-delegated-resource-management.md)ügyfeleket alkalmazna, fontos megérteni, hogyan működnek az Azure Active Directory (Azure AD) bérlői, felhasználói és szerepkörei, valamint hogyan használhatók az Azure Lighthouse-forgatókönyvekben.

A *bérlő* az Azure AD dedikált és megbízható példánya. Általában minden bérlő egyetlen szervezetet képvisel. Az Azure delegált erőforrás-kezelés lehetővé teszi az erőforrások logikai vetülete az egyik bérlőről a másikra. Ez lehetővé teszi a kezelőbérlő (például egy szolgáltatóhoz tartozó) felhasználók számára, hogy hozzáférjenek az ügyfél bérlőjében lévő delegált erőforrásokhoz, vagy lehetővé teszik a több bérlővel rendelkező vállalatok számára [a felügyeleti műveletek központosítását.](enterprise.md)

A logikai vetület elérése érdekében egy előfizetést (vagy egy előfizetésen belüli egy vagy több erőforráscsoportot) az ügyfél-bérlőben az Azure delegált erőforrás-kezeléséhez kell *beszállni.* Ez a bevezetési folyamat az [Azure Resource Manager-sablonokon keresztül,](../how-to/onboard-customer.md) vagy [egy nyilvános vagy privát ajánlat azure piactéren való közzétételével](../how-to/publish-managed-services-offers.md)végezhető el.

Bármelyik bevezetési módszert is választja, meg kell *határoznia az engedélyeket.* Minden engedélyezés egy felhasználói fiókot határoz meg a kezelő bérlőben, amely hozzáfér a delegált erőforrásokhoz, és egy beépített szerepkört, amely beállítja az egyes felhasználók engedélyeit ezekhez az erőforrásokhoz.

## <a name="role-support-for-azure-delegated-resource-management"></a>Szerepkör-támogatás az Azure delegált erőforrás-kezeléséhez

Az engedélyezés meghatározásakor minden felhasználói fiókhoz hozzá kell rendelni egy [szerepköralapú hozzáférés-vezérlési (RBAC) beépített szerepkört.](../../role-based-access-control/built-in-roles.md) Egyéni szerepkörök és [a klasszikus előfizetés-rendszergazdai szerepkörök](../../role-based-access-control/classic-administrators.md) nem támogatottak.

Az Azure delegált erőforrás-kezelése jelenleg minden [beépített szerepkört](../../role-based-access-control/built-in-roles.md) támogat, a következő kivételekkel:

- A [tulajdonos](../../role-based-access-control/built-in-roles.md#owner) szerepkör nem támogatott.
- A [DataActions](../../role-based-access-control/role-definitions.md#dataactions) engedéllyel rendelkező beépített szerepkörök nem támogatottak.
- A [Felhasználói hozzáférés rendszergazdája](../../role-based-access-control/built-in-roles.md#user-access-administrator) beépített szerepkör támogatott, de csak korlátozott célból, [hogy szerepköröket rendel egy felügyelt identitáshoz az ügyfél-bérlőben.](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant) A szerepkör által általában megadott egyéb engedélyek nem lesznek érvényesek. Ha ezzel a szerepkörrel rendelkező felhasználót határoz meg, meg kell adnia azokat a beépített szerepköröket is, amelyeket a felhasználó a felügyelt identitásokhoz rendelhet.

> [!NOTE]
> Miután egy megfelelő új beépített szerepkört hozzáadott az Azure-hoz, hozzárendelhető, amikor [az Azure Resource Manager-sablonok használatával egy ügyfél reszelője.](../how-to/onboard-customer.md) Előfordulhat, hogy a [felügyelt szolgáltatásajánlat közzétételekor](../how-to/publish-managed-services-offers.md)az újonnan hozzáadott szerepkör elérhetővé válik a Cloud Partner Portal webhelyen.

## <a name="best-practices-for-defining-users-and-roles"></a>Gyakorlati tanácsok a felhasználók és szerepkörök meghatározásához

Az engedélyek létrehozásakor a következő gyakorlati tanácsokat javasoljuk:

- A legtöbb esetben érdemes engedélyeket rendelni egy Azure AD felhasználói csoporthoz vagy egyszerű szolgáltatáshoz, nem pedig egyéni felhasználói fiókok sorozatához. Ez lehetővé teszi, hogy az egyes felhasználók hozzáférését anélkül adja hozzá vagy távolítsa el, hogy a hozzáférési követelmények változásakor frissítenie kellene és újra közzé kellene tennie a tervet.
- Ügyeljen arra, hogy kövesse a minimális jogosultság elvét, hogy a felhasználók csak a feladat uk elvégzéséhez szükséges engedélyekkel rendelkezhessenek, így csökkentve a véletlen hibák esélyét. További információ: [Ajánlott biztonsági eljárások](../concepts/recommended-security-practices.md).
- A [felügyelt szolgáltatások regisztrációs hozzárendelésének törlése szerepkörrel](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) rendelkező felhasználót is felvehet, így szükség esetén később [eltávolíthatja a delegáláshoz való hozzáférést.](../how-to/onboard-customer.md#remove-access-to-a-delegation) Ha ez a szerepkör nincs hozzárendelve, a delegált erőforrásokat csak az ügyfél bérlőjében lévő felhasználó távolíthatja el.
- Győződjön meg arról, hogy minden olyan felhasználó, akinek meg kell [tekintenie az Ügyfelek lapot az Azure Portalon,](../how-to/view-manage-customers.md) rendelkezik az [Olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör (vagy egy másik beépített szerepkör, amely magában foglalja a Reader-hozzáférést).

> [!IMPORTANT]
> Az Azure AD-csoport engedélyeinek hozzáadásához a **csoporttípusnak** **biztonságnak,** nem pedig **Office 365-nek**kell lennie. Ez a beállítás a csoport létrehozásakor van bejelölve. További információ: [Hozzon létre egy alapszintű csoportot, és adjon hozzá tagokat az Azure Active Directory használatával.](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

## <a name="next-steps"></a>További lépések

- Ismerje meg [az Azure delegált erőforrás-kezeléséhez ajánlott biztonsági eljárásokat.](recommended-security-practices.md)
- Az azure-beli delegált erőforrás-kezeléshez az [Azure Resource Manager-sablonjainak használatával,](../how-to/onboard-customer.md) vagy [egy privát vagy nyilvánosan felügyelt szolgáltatások azure Piactérre való közzétételével.](../how-to/publish-managed-services-offers.md)
