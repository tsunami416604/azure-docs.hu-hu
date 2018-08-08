---
title: A Privileged Identity Management Azure-erőforrásszerepkörök jóváhagyási munkafolyamata |} A Microsoft Docs
description: Az Azure-erőforrásokhoz a jóváhagyási munkafolyamatot ismerteti.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: dcb306bda8ef7d93314390bd9a90327ffdd14a0e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619637"
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>A Privileged Identity Management Azure-erőforrásszerepkörök jóváhagyási munkafolyamata

Jóváhagyási munkafolyamat Privileged Identity Management (PIM) az Azure-erőforrások szerepköreihez tartozó a rendszergazdák további védelméhez, vagy kritikus erőforrásokhoz való hozzáférés korlátozása. A rendszergazdák, szerepkör-hozzárendelések az aktiváláshoz jóváhagyásra lehet szükség. 

Az erőforrás hierarchia elképzelés az, csak az Azure-erőforrások szerepköreihez. Ez a hierarchia lehetővé teszi, hogy az öröklési szülőtároló található összes gyermek-erőforrás a szülőobjektum erőforrás lefelé szerepkör-hozzárendelések. 

Például: Bob, egy erőforrás-rendszergazda, használja a PIM módon megadott tagja jogosult Alice hozzárendelése a Contoso az előfizetésben tulajdonosi szerepkör. Ezt a hozzárendelést Alice a Contoso-előfizetésen belüli összes erőforrás csoport tárolók jogosult tulajdonosa. Alice az is az összes erőforrás (például virtuális gépek) jogosult tulajdonosa az előfizetés mindegyik erőforráscsoporton belül. 

Tegyük fel, a Contoso-előfizetés három erőforráscsoportok vannak: a Fabrikam tesztelés, a Fabrikam fejlesztői és a Fabrikam gyárt. Ezeket az erőforráscsoportokat mindegyike tartalmaz egy virtuális gépen.

A PIM beállításai az egyes szerepkörökhöz erőforrás. Hozzárendelések ellentétben ezek a beállítások nem öröklődnek, és feltétlenül vonatkozik az erőforrás-szerepkör. [További információ a jogosult hozzárendelések és láthatósága](pim-resource-roles-eligible-visibility.md).

A példa folytatása: Bob PIM használ, amelyet aktiválnia kell a tulajdonosi szerepkör a Contoso előfizetési kérés jóváhagyási szereplő összes tag igényelnek. A Fabrikam éles erőforráscsoportban az erőforrások védelme érdekében Bob is jóváhagyásra van szüksége ennek az erőforrásnak a tulajdonos szerepkör tagjai. A Fabrikam tesztelési és a Fabrikam fejlesztői tulajdonos szerepkörök nem igényel jóváhagyási az aktiváláshoz.

Amikor Ágnes a Contoso előfizetés tulajdonosi szerepkörét aktiválását, a jóváhagyó musí schválit nebo elutasítja a saját kérelmet, mielőtt ő válik aktívvá a szerepkörben. Ha úgy dönt, hogy Ágnes [saját aktiválási hatókör](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) a Fabrikam éles erőforráscsoporthoz a jóváhagyó jóváhagyása vagy elutasítása túl a ezt a kérelmet kell. De ha Alice úgy dönt, hogy saját aktiválási egyik vagy mindkét Fabrikam teszt vagy a Fabrikam fejlesztői hatókör, jóváhagyás, nem szükséges.

A jóváhagyási munkafolyamat nem feltétlenül szükséges a szerepkör összes tagja számára. Példaként vegyünk egy forgatókönyvet, ahol a szervezet több szerződés hozzárendeli egy Azure-előfizetésben futó alkalmazás fejlesztésének kiküszöbölni hires. Erőforrás-rendszergazdaként azt szeretné, hogy az alkalmazottak jogosult hozzáférése nélkül jóváhagyás szükséges, de a szerződés hozzárendeli engedélyt kell kérnie. Jóváhagyási munkafolyamatok konfigurálása csak a szerződés hozzárendeli, létrehozhat egy egyéni biztonsági szerepkört, az alkalmazottak rendelt szerepkör ugyanazokkal az engedélyekkel. Megkövetelheti, hogy egyéni szerepkör aktiválásához jóváhagyás. [További információ az egyéni szerepkörök](pim-resource-roles-custom-role-policy.md).

A jóváhagyási munkafolyamat konfigurálása, és adja meg, akik jóváhagyhatják vagy megtagadhatják a kérelmek, az alábbi eljárásokkal.

## <a name="require-approval-to-activate"></a>Az aktiváláshoz jóváhagyásra van szükség

1. A PIM az Azure Portalon keresse meg, és válasszon ki egy erőforrást a listából.

   ![A kiválasztott erőforrás "Azure-erőforrások" panel](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. A bal oldali panelen válassza ki a **szerepkör-beállítások**.

3. Keresse meg, és válasszon egy szerepkört, majd válassza ki **szerkesztése** beállításainak módosítására.

   ![Az operátor szerepkör "Szerkesztés" gombra](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. Az a **aktiválási** szakaszban jelölje be a **aktiválásához jóváhagyás szükséges** jelölőnégyzetet.

   ![Szerepkör-beállítások "Aktiválás" szakaszában](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Adja meg a jóváhagyóknak

Kattintson a **jóváhagyók kiválasztása** megnyitásához a **egy felhasználó vagy csoport kiválasztása** ablaktáblán.

>[!NOTE]
>Jelöljön ki legalább egy felhasználó vagy csoport a beállításainak frissítéséhez. Nincsenek nem alapértelmezett jóváhagyónak.

Erőforrás-rendszergazdák felhasználók és csoportok tetszőleges kombinációját is hozzáadhat a listát. 

!["A felhasználó vagy csoport kiválasztása" panelen a kiválasztott felhasználóhoz](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Kérelem jóváhagyása aktiválása

Engedély kérése nem befolyásolja az eljárást, amely a tag aktiválni kell követnie. [Tekintse át a szerepkört aktiváló lépéseit](pim-resource-roles-activate-your-roles.md).

Ha tagja a kért jóváhagyást igénylő szerepkörök aktiválása és a szerepkör már nem szükséges, a tag megszakíthatja a kérelmet az PIM-ben.

Megszakítja, keresse meg a PIM, és válassza a **saját kérések**. Keresse meg a kérelmet, és válasszon **Mégse**.

!["Saját kérések" panel](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Hagyja jóvá vagy utasítsa a kérelem

Jóváhagyja vagy elutasítja a kérelmet, a jóváhagyó lista tagjának kell lennie. 

1. A PIM, válassza ki **kérések jóváhagyása** a lapról, a bal oldali menüben, és keresse meg a kérelmet.

   !["A kérések jóváhagyása" panel](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Válassza ki a kérést, adja meg a döntést az indoklást, és válassza **jóváhagyás** vagy **Megtagadás**. A kérelem majd fog állni.

   ![Részletes információkat a kijelölt kérelem](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>A munkafolyamat-értesítések

Az alábbiakban néhány alapvető tudnivalók a munkafolyamat-értesítést:

- A jóváhagyó lista tagjainak az összes értesítést kap e-mailben a felülvizsgálatot függőben van egy kérelem egy adott szerepkör esetében. E-mail értesítések közé tartoznak a kérést, ha a jóváhagyó jóváhagyhatják vagy megtagadhatják a közvetlen hivatkozást.
- Kérelmek jóváhagyja vagy megtagadja a lista első tag által megoldott. 
- Amikor egy jóváhagyó válaszolt a kérelemre, a jóváhagyó lista összes tagja, a művelet értesítést kap. 
- Erőforrás-rendszergazdák értesítést kap egy jóváhagyott tagot a szerepkörük aktívvá válik. 

>[!Note]
>Úgy véli, hogy egy jóváhagyott tag nem lehet aktív erőforrás rendszergazda eltávolíthatja a aktív szerepkör-hozzárendelés az PIM-ben. Bár az erőforrás-rendszergazdák nem kapnak értesítést a függőben lévő kérések kivéve, ha a jóváhagyó lista tagjainak, megtekintheti és visszavonása függőben lévő kéréseket az összes olyan felhasználó megtekinti a függőben lévő kéréseket az PIM-ben. 

## <a name="next-steps"></a>További lépések

[A PIM-beállítások alkalmazása az egyedi felhasználói csoportok](pim-resource-roles-custom-role-policy.md)
