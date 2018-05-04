---
title: Jóváhagyási munkafolyamata az Azure erőforrás-szerepkörök a Privileged Identity Management |} Microsoft Docs
description: Azure-erőforrások jóváhagyási munkafolyamat folyamatát mutatjuk be.
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
ms.openlocfilehash: 7781c858a5c0e4db8593df0cf77b868b6fd23622
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>A Privileged Identity Management az Azure erőforrás-szerepkörök jóváhagyási munkafolyamata

A jóváhagyási munkafolyamata Privileged Identity Management (PIM) az Azure erőforrás-szerepkörök a rendszergazdák további védelméhez, vagy fontos erőforrásokhoz való hozzáférés korlátozása. Ez azt jelenti, hogy a rendszergazdák is jóváhagyás megkövetelése, szerepkör-hozzárendelések aktiválásához. 

Egy erőforrás-hierarchia fogalma egyedi az Azure erőforrás-szerepkörökhöz. Ezt a hierarchiát lehetővé teszi, hogy az öröklési szerepkör-hozzárendelések egy erőforrás szülőobjektumtól lefelé az összes alárendelt erőforráshoz, a szülő-tárolóban. 

Például: Bob, egy erőforrás rendszergazda PIM használ egy jogosult taggal Alice hozzárendelése a tulajdonosi szerepkört, a Contoso előfizetésben. Ehhez a hozzárendeléshez Alice esetén az összes erőforrás csoport tároló Contoso előfizetésen belül jogosult tulajdonosa. Alice egyben erőforrások (például virtuális gépek) jogosult tulajdonosa az előfizetés mindegyik erőforráscsoporton belül. 

Tegyük fel, a Contoso előfizetés három erőforráscsoportok szerepelnek: Fabrikam vizsgálatára, a Fabrikam fejlesztői és a Fabrikam termék. Egyetlen virtuális gépet tartalmaz minden ezeket az erőforráscsoportokat.

A PIM beállításai az egyes szerepkörökhöz egy erőforrást. Hozzárendelések, ellentétben ezek a beállítások nem örökli, és feltétlenül vonatkozik az erőforrás-szerepkör. [További információk a jogosult hozzárendelések és erőforrás-láthatósági](pim-resource-roles-eligible-visibility.md).

A példa folytatása: Bálint PIM használ az összes tagjához a tulajdonosi szerepkört, a Contoso előfizetés kérelem jóváhagyás aktiválni kell. A Fabrikam termék erőforráscsoportban erőforrások védelme érdekében Bob is jóváhagyásra van szüksége a tagjai ennek az erőforrásnak a tulajdonosi szerepkört. A tulajdonos szerepkörök Fabrikam tesztelési és a Fabrikam fejlesztői nem jóváhagyás megkövetelése, az aktiváláshoz.

Ha Alice kér a tulajdonosi szerepkört, a Contoso előfizetés aktiválása, a jóváhagyó kell hagyja jóvá vagy visszautasítja a kérelmet, előtt ő válik aktívvá a szerepkörben. Ha Alice úgy dönt, hogy [saját aktiválási hatókör](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) a Fabrikam termék erőforráscsoporttal, a jóváhagyó jóváhagyása vagy túl elutasítja a kérelmet, kell. De ha Alice úgy dönt, hogy az egyik vagy mindkét Fabrikam teszt- vagy Fabrikam fejlesztői-aktiválás hatókör, a jóváhagyási nincs szükség.

A jóváhagyási munkafolyamata nem feltétlenül szükséges a szerepkör összes tagja. Fontolja meg egy olyan forgatókönyvet, ahol a szervezet bízza, több szerződés társult segítséget az Azure-előfizetés által futtatott alkalmazás fejlesztésének. Erőforrás-rendszergazdaként alkalmazottaknak jogosult hozzáféréssel rendelkeznek a jóváhagyás szükséges nélkül kíván, de a szerződés társult jóvá kell hagyatnia. Jóváhagyási munkafolyamata konfigurálása csak a szerződés társult, hozzon létre egy egyéni biztonsági szerepkört rendelt alkalmazottak szerepkörként ugyanazokkal az engedélyekkel. Adott egyéni szerepkör aktiválásához jóváhagyásra lehet szükség. [További tudnivalók az egyéni szerepkörök](pim-resource-roles-custom-role-policy.md).

A jóváhagyási munkafolyamat konfigurálását, és adja meg, akik jóváhagyása vagy megtagadja a hozzáférést kérelmek, az alábbi eljárásokkal.

## <a name="require-approval-to-activate"></a>Az aktiváláshoz jóváhagyásra van szükség

1. Keresse meg a PIM, hogy az Azure portálon, és válasszon ki egy erőforrástípust a listából.

   ![A kiválasztott erőforrás "Azure-erőforrások" ablaktábla](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. A bal oldali ablaktáblán válassza ki a **beállítások**.

3. Keresse meg és jelölje ki a szerepkört, majd válassza ki **szerkesztése** beállításainak módosítására.

   ![A jelentésoperátori szerepnek a "Szerkesztés" gomb](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. Az a **aktiválási** szakaszban jelölje be a **jóváhagyás aktiválásához szükséges** jelölőnégyzetet.

   ![Felhasználóiszerep-beállítások "Aktiválás" szakasza](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Adja meg a jóváhagyóknak

Kattintson a **válassza ki a jóváhagyóknak** megnyitásához a **egy felhasználó vagy csoport kiválasztása** ablaktáblán.

>[!NOTE]
>Legalább egy felhasználónak vagy csoportnak a frissítése a beállítást kell választania. Nincsenek nem alapértelmezett jóváhagyóknak.

Erőforrás-rendszergazdák felhasználók és csoportok kombinációja adhat hozzá a jóváhagyóknak listáját. 

!["A felhasználó vagy csoport kiválasztása" ablaktáblán a kiválasztott felhasználóhoz](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Aktiválja a kérelem jóváhagyása

Jóváhagyást kérő rendelkezik nincs hatással az eljárást, amely tagja aktiválni kell követnie. [Tekintse át a szerepkört aktiváló lépéseket](pim-resource-roles-activate-your-roles.md).

A kért aktiválás, amelyhez jóváhagyás szükséges szerepkör tagja, és a szerepkör már nincs szükség, ha a tag megszüntetheti a PIM kérelmüket.

Megszakítja, keresse meg a PIM, és válassza ki **saját kérések**. Keresse meg a kérelmet, és válasszon **Mégse**.

!["Saját kérések" ablak](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Hagyja jóvá vagy visszautasítja a kérelmet

Hagyja jóvá vagy visszautasítja a kérelmet, a jóváhagyó lista tagjának kell lennie. 

1. A PIM, válassza ki **kérelmek jóváhagyása** a bal oldali menüben a lapról, és keresse meg a kérelmet.

   !["A kérelmek jóváhagyása" ablak](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. A kérelem, adja meg a döntést indoklását válassza ki és **jóváhagyás** vagy **Megtagadás**. A kérelem majd meg van oldva.

   ![Részletes információkat a kijelölt kérelem](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Munkafolyamat-értesítést

Az alábbiakban néhány alapvető tudnivalók a munkafolyamat-értesítést:

- A jóváhagyó lista összes tagja értesítést kap e-mailben egy szerepkörhöz vonatkozó kérés van függőben lévő a felülvizsgálati. Értesítő e-mailek közvetlen mutató hivatkozást a kérést, ahol a jóváhagyó jóváhagyása vagy elutasítása.
- A lista, aki jóvá vagy letiltja az első tagtól kéréseket szünteti meg. 
- Jóváhagyó válaszol a kérelemre, a jóváhagyó lista összes tagja művelet értesítést kap. 
- Erőforrás-rendszergazdák értesítést kap egy jóváhagyott tag válik aktívvá a szerepkörben. 

>[!Note]
>Egy erőforrás-rendszergazda, aki úgy véli, hogy egy jóváhagyott tag nem lehet aktív aktív szerepkör-hozzárendelés eltávolítása a PIM. Bár az erőforrás-rendszergazdák nem értesítik függőben lévő kérelmek kivéve, ha a jóváhagyó lista tagjai, tekinthet meg és függőben lévő kérelmek az összes olyan felhasználó megszakítja a függőben levő kérelmekre PIM megtekintésével. 

## <a name="next-steps"></a>További lépések

[A PIM-beállítások alkalmazása az egyedi felhasználói csoportok](pim-resource-roles-custom-role-policy.md)
