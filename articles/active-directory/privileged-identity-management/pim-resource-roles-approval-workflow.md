---
title: Privileged Identity Management - jóváhagyási munkafolyamata az Azure erőforrás-szerepkörök |} Microsoft Docs
description: Ismerteti, hogyan a a jóváhagyási munkafolyamat dolgozza fel az Azure-erőforrások.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: c02d595d75b2d63558896054c185102ebb23cc9e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---approve"></a>Privileged Identity Management - szerepkör erőforrás - jóváhagyása

Az Azure erőforrás-szerepkörök a PIM munkafolyamat jóváhagyást a rendszergazdák további védelméhez, vagy fontos erőforrásokhoz való hozzáférés korlátozása történő aktiválásához szerepkör-hozzárendelések jóváhagyásra van szükség. Az Azure erőforrás-szerepkörök egyedi van egy erőforrás-hierarchia fogalmát. A hierarchia lehetővé teszi, hogy az öröklési szerepkör-hozzárendelések egy erőforrás szülőobjektumtól lefelé az összes alárendelt gyermek erőforráshoz, a szülő-tárolóban. 

Például Bálint, egy erőforrás rendszergazda PIM Alice egy jogosult tagként való hozzárendelése a tulajdonosi szerepkört, a Contoso előfizetésben. Az ehhez a hozzárendeléshez Alice egyben a Contoso előfizetésen belüli összes erőforrás csoport tárolót, és az előfizetés mindegyik erőforráscsoporton belül található összes erőforrást (például virtuális gépek) jogosult tulajdonosa. Tegyük fel, három erőforráscsoportok szerepelnek a Contoso előfizetés; Fabrikam teszt, a Fabrikam fejlesztői és a Fabrikam termék. Egyetlen virtuális gépet tartalmaz minden ezeket az erőforráscsoportokat.

A PIM beállításai az egyes szerepkörökhöz erőforrás és hozzárendelések ellentétben ezek a beállítások nem örökli, és feltétlenül vonatkozik az erőforrás-szerepkör. [Tudjon meg többet a jogosult hozzárendelések és láthatósága.](pim-resource-roles-eligible-visibility.md)

A fenti példa Bob használatával PIM minden tagja a tulajdonosi szerepkört, a Contoso előfizetés kérelem jóváhagyás aktiválásához szükséges. A Fabrikam termék erőforráscsoportban található erőforrást védelme érdekében Bob jóváhagyásra van szüksége a tagjai ennek az erőforrásnak a tulajdonosi szerepkört is. A tulajdonos szerepkör Fabrikam tesztelési és a Fabrikam fejlesztői esetében nincs szükség jóváhagyásra aktiválásához.

Ha a tulajdonosi szerepkört, a Contoso előfizetés jóváhagyó Alice kérelmek aktiválásának kell hagyja jóvá vagy visszautasítja a kérelmet, előtt ő válik aktívvá a szerepkörben. Továbbá, ha úgy dönt, hogy Alice [saját aktiválási hatókör](pim-resource-roles-activate-your-roles.md#just-enough-administration) a Fabrikam termék erőforráscsoporthoz jóváhagyó jóvá kell, vagy túl elutasítja a kérelmet. Azonban ha Alice úgy dönt, hogy az egyik vagy mindkét Fabrikam teszt- vagy Fabrikam fejlesztői-aktiválás hatókör, jóváhagyási nem lesz szükség.

Jóváhagyási munkafolyamata nem lehet szükséges a szerepkör összes tagja. Fontolja meg egy olyan forgatókönyvet, ahol a szervezet bízza, több szerződés társult, amely egy Azure-előfizetés alkalmazás fejlesztésének segít. Erőforrás-rendszergazdaként szeretné, hogy az alkalmazottak jogosult hozzáférjenek nélkül jóváhagyás szükséges, de a szerződés társult jóvá kell hagyatnia. Csak a szerződés jóváhagyási munkafolyamata konfigurálása a ugyanazokkal az engedélyekkel rendelkezzen a szerepkörként is létrehozhat egy egyéni biztonsági szerepkört társult alkalmazottak rendelt, és, hogy egyéni szerepkör aktiválásához jóváhagyás szükséges. [További tudnivalók az egyéni szerepkörök](pim-resource-roles-custom-role-policy.md).

Jóváhagyási munkafolyamat konfigurálását, és adja meg, akik jóváhagyása vagy megtagadja a hozzáférést kérelmek az alábbi lépésekkel.

## <a name="require-approval-to-activate"></a>Az aktiváláshoz jóváhagyásra van szükség

A PIM navigáljon az Azure portálon, és válasszon ki egy erőforrástípust a listából.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

Válassza a bal oldali navigációs menü **beállítások**.

Keresse meg és jelölje ki a szerepkört, majd kattintson **szerkesztése** beállításainak módosítására.

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

Az az aktiválás szakaszban jelölje be a jelölőnégyzetet **jóváhagyás aktiválásához szükséges**.

![](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Adja meg a jóváhagyóknak

Kattintson a **válassza ki a jóváhagyóknak** megnyitásához a kiválasztására szolgáló képernyő megjelenítéséhez.

>[!NOTE]
>Legalább egy felhasználónak vagy csoportnak a frissítése a beállítást kell választania. Nincsenek nem alapértelmezett jóváhagyóknak.

Erőforrás-rendszergazdák felhasználók és csoportok kombinációja adhat hozzá a jóváhagyóknak listáját. 

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Aktiválja a kérelem jóváhagyása

Jóváhagyási nincs hatással van az eljárást a kért tag után kell következnie aktiválásához. [Tekintse át a szerepkört aktiváló lépéseket](pim-resource-roles-activate-your-roles.md).

A kért aktiválás, amelyhez jóváhagyás szükséges szerepkör tagja, és a szerepkör már nincs szükség, ha a tag megszakíthatja a PIM kérelmüket.

Szakítsa meg, keresse meg a PIM, és válassza ki a "Saját kérések". Keresse meg a kérelmet, és kattintson a "Mégse gombra".

![](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Hagyja jóvá vagy visszautasítja a kérelmet

Jóváhagyja vagy elutasítja a kérelmet a jóváhagyó lista tagjának kell lennie. A PIM a bal oldali navigációs menü lapján válassza a "Kérelmek jóváhagyása", és keresse meg a kérelmet.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

A kérelem, adja meg a döntést indoklását válassza ki és jóváhagyja vagy elutasítja a kérelem feloldását.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Munkafolyamat-értesítést

- A jóváhagyó lista összes tagja értesítést kap e-mailben egy szerepkörhöz vonatkozó kérés van függőben lévő a felülvizsgálati. Értesítő e-mailek közvetlen mutató hivatkozást a kérelmet, ahol a jóváhagyó jóváhagyása vagy elutasítása.
- A lista, amely jóváhagyja vagy letiltja az első tagtól kérelmek szünteti meg. 
- Jóváhagyó válaszol a kérelemre, a jóváhagyó lista összes tagja művelet értesítést kap. 
- Erőforrás-rendszergazdák értesítést kap egy jóváhagyott tag válik aktívvá a szerepkörben. 

>[!Note]
>A egy eseményt, ha egy erőforrás-rendszergazda úgy véli, hogy a jóváhagyott tagok nem lehetnek aktív akkor eltávolíthatja a PIM aktív szerepkör-hozzárendelést. Erőforrás-rendszergazdák nem értesítik függőben lévő kérelmek kivéve, ha a jóváhagyó lista tagjai, bár előfordulhat, hogy megtekinti és a függőben lévő kérelmek az összes olyan felhasználó megszakítja a függőben levő kérelmekre PIM megtekintésével. 

## <a name="next-steps"></a>További lépések

[A PIM-beállítások alkalmazása az egyedi felhasználói csoportok](pim-resource-roles-custom-role-policy.md)
